# Fix POST /uploads 500 when scanOptions is sent as multipart nested fields

## Problem

When uploading a file via REST API (`POST /repo/api/v1/uploads` with `uploadType=file`) and providing `scanOptions` as multipart nested fields (e.g. `scanOptions[analysis][nomos]=true`), the endpoint returns HTTP 500 with a `TypeError`.

**Error message:**
```
json_decode(): Argument #1 ($json) must be of type string, array given
```

**Reproduction:**
```bash
curl -X POST 'http://localhost:8081/repo/api/v1/uploads' \
  -H "Authorization: Bearer <token>" \
  -H 'folderId: 1' \
  -H 'uploadType: file' \
  -F 'fileInput=@sample.tar.gz' \
  -F 'scanOptions[analysis][nomos]=true' \
  -F 'scanOptions[analysis][monk]=true'
```

**Expected:** HTTP 201 Created with upload ID  
**Actual:** HTTP 500 Internal Server Error

## Root Cause

`UploadController::postUpload()` unconditionally called `json_decode()` on `scanOptions`, assuming it would always be a JSON string. However, when using `multipart/form-data` (which is the natural encoding for file uploads), the Slim framework automatically parses nested form fields like `scanOptions[analysis][nomos]=true` into PHP arrays. This causes PHP 8's strict type checking to throw a `TypeError` when `json_decode()` receives an array instead of a string.

## Solution

- **Added validation** to ensure `$reqBody` is always an array (prevents `array_key_exists()` errors on null)
- **Updated `scanOptions` parsing** to be polymorphic:
  - If `scanOptions` is already an **array** (from multipart nested fields), use it directly
  - If `scanOptions` is a **string** (JSON), decode it with `json_decode()`
- **Added unit test coverage** for the multipart array format to prevent regressions

## Files Changed

- `src/www/ui/api/Controllers/UploadController.php` - Fixed scanOptions parsing logic
- `src/www/ui_tests/api/Controllers/UploadControllerTest.php` - Added test coverage for multipart array format

## Testing

### Manual Testing
- **Before fix:** Reproduced the 500 error with curl multipart request
- **After fix:** Same request now returns HTTP 201 (Created) successfully
- Verified upload is created and jobs are scheduled correctly

### Unit Tests
- Updated existing `testPostUpload` tests to use proper `ScanOptions` object format
- Added new test case `testPostUploadFileWithScanOptionsArray` to cover multipart array format
- All tests pass

## Impact

This fix improves API usability by supporting the standard multipart form encoding that many HTTP clients (including OpenAPI generators) use by default. The change is backward compatible - existing clients that send `scanOptions` as a JSON string will continue to work.

## Checklist

- [x] Code follows FOSSology coding style guidelines
- [x] Commit message follows Conventional Changelog format
- [x] DCO (Signed-off-by) included in commit
- [x] Changes are minimal and focused on the bug fix
- [x] Unit tests added/updated
- [x] Manual testing performed
- [x] No breaking changes (backward compatible)
