# Quick Start Guide

## Initial Setup

1. **Add your library content:**
   - Create `symbols/` directory and add your `.kicad_sym` files
   - Create `footprints/` directory and add your `.pretty` folders with `.kicad_mod` files
   - (Optional) Create `3dmodels/` directory for 3D models

2. **Configure metadata.json:**
   - Update the `name` field with your library name
   - Update the `description` and `description_full` fields
   - Update the `identifier` (format: `com.github.username.repository`)
   - Update `author` information
   - Update `resources` URLs
   - Keep `versions` as an empty array (it will be auto-populated)

3. **Commit your changes:**
   ```bash
   git add .
   git commit -m "Add library content and configuration"
   git push
   ```

## Creating a Release

### Option 1: Using Git Tags (Recommended)

```bash
# Create and push a version tag
git tag v1.0.0
git push origin v1.0.0
```

This will automatically:
- Trigger the `release.yml` workflow
- Create a zip file with your library content
- Create a GitHub release
- Trigger the `update-metadata.yml` workflow
- Update metadata.json and repository.json

### Option 2: Manual Workflow Trigger

1. Go to your repository on GitHub
2. Click on "Actions" tab
3. Select "Create Release" workflow
4. Click "Run workflow"
5. Enter the version number (e.g., `1.0.0`)
6. Click "Run workflow"

## Workflow Details

### Release Workflow (`release.yml`)

**Triggers:**
- Push of version tags (`v*.*.*`)
- Manual workflow dispatch

**Actions:**
1. Creates a `package/` directory
2. Copies symbols, footprints, 3dmodels (if present)
3. Copies metadata.json, README.md, LICENSE
4. Creates a zip archive named `kicad-library-{version}.zip`
5. Calculates SHA256 hash
6. Creates GitHub release with the zip file

**Outputs:**
- Release archive on GitHub Releases page
- SHA256 hash in release notes
- File size in release notes

### Update Metadata Workflow (`update-metadata.yml`)

**Triggers:**
- When a release is published
- Manual workflow dispatch (with parameters)

**Actions:**
1. Fetches release information
2. Downloads the release zip to calculate SHA256 and size
3. Updates `metadata.json` with new version entry:
   - Version number
   - Download URL
   - SHA256 hash
   - File sizes
4. Updates `repository.json` with package information
5. Commits and pushes changes to main branch

**Result:**
- `metadata.json` updated with new version
- `repository.json` synchronized with metadata
- Changes committed to repository

## Version Numbering

Use semantic versioning: `MAJOR.MINOR.PATCH`

- **MAJOR**: Breaking changes
- **MINOR**: New features, backward compatible
- **PATCH**: Bug fixes, backward compatible

Examples:
- `v1.0.0` - Initial release
- `v1.1.0` - Added new symbols/footprints
- `v1.1.1` - Fixed footprint dimensions
- `v2.0.0` - Reorganized library structure

## Troubleshooting

### Workflow doesn't trigger

- Ensure you pushed the tag: `git push origin v1.0.0`
- Check that tag format is correct: `v{MAJOR}.{MINOR}.{PATCH}`
- Verify workflow files are in `.github/workflows/` directory

### Metadata not updating

- Check that release was published (not draft)
- Verify `update-metadata.yml` workflow completed successfully
- Check workflow logs in Actions tab

### Release zip is empty

- Ensure you have content in `symbols/` or `footprints/` directories
- Check that directories are committed to repository
- Review workflow logs for errors

## Using Your Library in KiCAD

### Method 1: KiCAD Plugin and Content Manager

Users can add your repository to KiCAD's PCM:

1. Open KiCAD
2. Tools → Plugin and Content Manager
3. Settings → Add repository
4. Enter your repository.json URL:
   `https://raw.githubusercontent.com/yourusername/kicad-library/main/repository.json`
5. Browse and install your library

### Method 2: Manual Installation

1. Download the release zip from GitHub Releases
2. Extract the contents
3. Add library paths in KiCAD:
   - Preferences → Manage Symbol Libraries
   - Preferences → Manage Footprint Libraries

## Next Steps

- Test the workflows by creating your first release
- Customize the metadata.json with accurate information
- Add documentation for your symbols and footprints
- Consider adding 3D models for better PCB visualization
