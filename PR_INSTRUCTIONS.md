# Pull Request Instructions

## Status
✅ Branch created: `sakirr05/fix/scanoptions-multipart-array`  
✅ Commit created with proper format and DCO  
⏳ Ready to push and create PR

## Next Steps

### 1. Push the branch to your fork
```bash
cd /home/sakirr7/fossology1/fossology
git push -u origin sakirr05/fix/scanoptions-multipart-array
```

If you need to authenticate, GitHub will prompt you or you can use a personal access token.

### 2. Create the Pull Request

Go to: https://github.com/fossology/fossology/compare/master...sakirr05:fossology:sakirr05/fix/scanoptions-multipart-array

Or manually:
1. Visit https://github.com/fossology/fossology
2. Click "Pull requests" → "New pull request"
3. Select base: `fossology/fossology:master`
4. Select compare: `sakirr05/fossology:sakirr05/fix/scanoptions-multipart-array`
5. Use the PR description from `PR_DESCRIPTION.md`

### 3. PR Title
```
fix(api): handle scanOptions as array or JSON string in POST /uploads
```

### 4. PR Description
See `PR_DESCRIPTION.md` for the full description to paste into GitHub.

## Commit Details

**Commit hash:** `09be357e6`  
**Format:** Follows Conventional Changelog (`fix(api): ...`)  
**DCO:** ✅ Signed-off-by included  
**Files changed:**
- `src/www/ui/api/Controllers/UploadController.php`
- `src/www/ui_tests/api/Controllers/UploadControllerTest.php`

## Verification

Before pushing, you can verify:
```bash
# Check commit format
git log -1 --pretty=fuller

# Verify changes
git diff upstream/master...HEAD

# Check branch is up to date
git pull --rebase upstream master
```
