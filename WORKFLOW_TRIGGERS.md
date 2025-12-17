# Workflow Triggers Reference

This document explains when each GitHub Actions workflow runs and what triggers them.

## Release Workflow (`release.yml`)

### Automatic Triggers

**Tag Push (Recommended)**
- Trigger: When you push a version tag to GitHub
- Pattern: `v*.*.*` (e.g., `v1.0.0`, `v2.1.5`, `v0.1.0-beta`)
- Example:
  ```bash
  git tag v1.0.0
  git push origin v1.0.0
  ```

### Manual Triggers

**Workflow Dispatch**
- Trigger: Manual run from GitHub Actions web interface
- Inputs Required:
  - `version`: Version number without 'v' prefix (e.g., `1.0.0`)
- Steps:
  1. Go to repository on GitHub
  2. Click "Actions" tab
  3. Select "Create Release" workflow
  4. Click "Run workflow" button
  5. Enter version number
  6. Click "Run workflow" to start

### What It Does

1. Checks out the repository code
2. Determines version from tag or input
3. Validates that library content exists (symbols/, footprints/, or 3dmodels/)
4. Creates package directory
5. Copies library content to package
6. Copies metadata.json, README.md, LICENSE to package
7. Creates zip archive: `kicad-library-{version}.zip`
8. Calculates SHA256 hash
9. Gets file size
10. Creates GitHub release with zip attached
11. Adds release notes with download info

### Outputs

- GitHub Release created
- Zip file attached to release
- Release notes with SHA256 and file size

## Update Metadata Workflow (`update-metadata.yml`)

### Automatic Triggers

**Release Published**
- Trigger: When a GitHub release is published
- Typically triggered automatically after `release.yml` completes
- No manual action needed

### Manual Triggers

**Workflow Dispatch**
- Trigger: Manual run from GitHub Actions web interface
- Inputs Required:
  - `version`: Version number (e.g., `1.0.0`)
  - `tag`: Git tag (e.g., `v1.0.0`)
  - `sha256`: SHA256 hash of the release archive
  - `size`: Size of the release archive in bytes
- Use Case: Updating metadata for an existing release
- Steps:
  1. Go to repository on GitHub
  2. Click "Actions" tab
  3. Select "Update Metadata" workflow
  4. Click "Run workflow" button
  5. Fill in all required inputs
  6. Click "Run workflow" to start

### What It Does

1. Checks out the repository (main branch)
2. Gets release information (version, tag, download URL)
3. Downloads the release zip file
4. Calculates SHA256 and file size (if not provided)
5. Updates metadata.json:
   - Adds new version entry
   - Sets download URL
   - Sets SHA256 hash
   - Sets file sizes
   - Sorts versions (newest first)
6. Updates repository.json:
   - Synchronizes with metadata.json
   - Ensures package is listed
7. Commits changes to main branch
8. Pushes changes to GitHub

### Outputs

- Updated metadata.json committed to main branch
- Updated repository.json committed to main branch

## Workflow Sequence

### Normal Release Flow

```
1. Developer creates tag
   └─> git tag v1.0.0
   └─> git push origin v1.0.0

2. release.yml triggers automatically
   └─> Validates content exists
   └─> Creates zip package
   └─> Calculates SHA256
   └─> Creates GitHub release

3. update-metadata.yml triggers automatically
   └─> Downloads release zip
   └─> Updates metadata.json
   └─> Updates repository.json
   └─> Commits to main branch

4. Done!
   └─> Release is published
   └─> Metadata is updated
   └─> Users can install via PCM
```

### Manual Release Flow

```
1. Developer triggers release.yml manually
   └─> Goes to Actions tab
   └─> Runs "Create Release" workflow
   └─> Enters version: 1.0.0

2. release.yml runs
   └─> Creates tag v1.0.0
   └─> Creates zip package
   └─> Calculates SHA256
   └─> Creates GitHub release

3. update-metadata.yml triggers automatically
   └─> Same as automatic flow

4. Done!
```

## Timing and Dependencies

### Workflow Timing

- **release.yml**: 1-3 minutes (depends on library size)
- **update-metadata.yml**: 1-2 minutes (depends on zip size)
- **Total**: 2-5 minutes from tag push to metadata update

### Workflow Dependencies

- `update-metadata.yml` depends on `release.yml` completing
- If `release.yml` fails, `update-metadata.yml` won't run
- Both workflows require `contents: write` permission

## Troubleshooting

### Release workflow doesn't trigger

**Problem**: Pushed tag but workflow didn't run
**Solutions**:
- Verify tag format: must be `v*.*.*`
- Check tag exists on GitHub: `git tag -l` then `git push --tags`
- Check Actions tab for workflow runs
- Verify workflows are enabled in repository settings

### Metadata workflow doesn't update files

**Problem**: Release created but metadata.json not updated
**Solutions**:
- Check if release is published (not draft)
- Verify update-metadata.yml workflow completed
- Check workflow logs for errors
- Ensure zip file matches pattern: `kicad-library-*.zip`

### Release created but empty

**Problem**: Zip file has no content
**Solutions**:
- Ensure symbols/, footprints/, or 3dmodels/ directories exist
- Verify directories contain files
- Check release.yml validation step in logs

### Manual trigger not working

**Problem**: Can't run workflow manually
**Solutions**:
- Ensure you have write permissions to repository
- Check if workflows are enabled
- Verify you're on correct branch (should be main/master)
- Check Actions tab is accessible

## Best Practices

1. **Use automatic triggers** (tag push) for normal releases
2. **Tag format**: Always use `v{MAJOR}.{MINOR}.{PATCH}` (e.g., `v1.0.0`)
3. **Test first**: Create a test release (e.g., `v0.0.1`) to verify workflows
4. **Wait for completion**: Let both workflows complete before testing
5. **Pull changes**: After workflows complete, pull main branch to get metadata updates
6. **Verify metadata**: Check metadata.json has correct version info

## Monitoring Workflows

### Check Workflow Status

1. Go to repository on GitHub
2. Click "Actions" tab
3. View recent workflow runs
4. Click on a run to see details
5. Check each step for success/failure

### View Workflow Logs

1. Click on workflow run
2. Click on job name
3. Expand steps to see output
4. Look for errors or warnings
5. Download logs if needed for debugging

## Advanced Usage

### Creating Pre-releases

For beta or alpha versions:
```bash
git tag v1.0.0-beta.1
git push origin v1.0.0-beta.1
```

Workflows will run normally. Consider marking the GitHub release as "pre-release" in the UI after creation.

### Updating Old Releases

If you need to update metadata for an old release:

1. Go to Actions → Update Metadata
2. Run workflow manually
3. Provide version, tag, SHA256, and size
4. Workflow will update metadata.json

### Re-running Failed Workflows

If a workflow fails:

1. Click on the failed workflow run
2. Click "Re-run jobs" → "Re-run failed jobs"
3. Or "Re-run all jobs" to start fresh

## Questions?

See also:
- README.md - General overview
- QUICKSTART.md - Setup instructions
- SETUP_CHECKLIST.md - Step-by-step checklist
- IMPLEMENTATION_SUMMARY.md - Technical details
