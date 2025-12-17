# KiCAD Library

A KiCAD library repository with automated GitHub Actions workflows for creating releases and managing metadata.

## Repository Structure

```
kicad-library/
├── symbols/              # KiCAD symbol libraries (.kicad_sym files)
├── footprints/           # KiCAD footprint libraries (.pretty directories)
├── 3dmodels/            # Optional: 3D models for footprints
├── metadata.json        # KiCAD Package Manager metadata
├── repository.json      # Package repository listing
└── .github/
    └── workflows/
        ├── release.yml          # Creates release zip files
        └── update-metadata.yml  # Updates metadata files
```

## Adding Your Content

1. **Symbols**: Add your `.kicad_sym` files to the `symbols/` directory
2. **Footprints**: Add your `.pretty` directories (containing `.kicad_mod` files) to the `footprints/` directory
3. **3D Models** (optional): Add your 3D model files to the `3dmodels/` directory

## GitHub Actions Workflows

### 1. Create Release (`release.yml`)

This workflow creates a release zip file containing your library content.

**Trigger Methods:**

- **Automatic**: Push a version tag (e.g., `v1.0.0`)
  ```bash
  git tag v1.0.0
  git push origin v1.0.0
  ```

- **Manual**: Run the workflow from GitHub Actions tab
  - Go to Actions → Create Release → Run workflow
  - Enter the version number (e.g., `1.0.0`)

**What it does:**
- Packages symbols, footprints, and 3D models into a zip file
- Calculates SHA256 hash of the archive
- Creates a GitHub release with the zip file attached
- Outputs release information (version, SHA256, file size)

### 2. Update Metadata (`update-metadata.yml`)

This workflow automatically updates `metadata.json` and `repository.json` with new release information.

**Trigger Methods:**

- **Automatic**: Runs when a release is published
- **Manual**: Run the workflow from GitHub Actions tab (requires release info)

**What it does:**
- Downloads release information from GitHub
- Updates `metadata.json` with new version entry
- Updates `repository.json` with package information
- Commits and pushes changes back to the repository

## Configuration

### metadata.json

Edit this file to configure your library's metadata:

```json
{
  "$schema": "https://go.kicad.org/pcm/schemas/v1",
  "name": "Your Library Name",
  "description": "Short description",
  "description_full": "Full description of your library",
  "identifier": "com.github.yourusername.your-library",
  "type": "library",
  "author": {
    "name": "Your Name",
    "contact": {
      "web": "https://github.com/yourusername/your-library"
    }
  },
  "license": "MIT",
  "resources": {
    "homepage": "https://github.com/yourusername/your-library",
    "repository": "https://github.com/yourusername/your-library"
  },
  "versions": []
}
```

The `versions` array will be automatically populated by the `update-metadata.yml` workflow.

### repository.json

This file lists all packages in your repository. It will be automatically updated by the workflow to mirror the metadata.json content.

## Usage Workflow

1. **Add your library content** (symbols, footprints, etc.)
2. **Update metadata.json** with your library information
3. **Commit and push** your changes
4. **Create a release**:
   ```bash
   git tag v1.0.0
   git push origin v1.0.0
   ```
5. **GitHub Actions will automatically**:
   - Create a release zip file
   - Calculate SHA256 hash
   - Update metadata.json and repository.json
   - Commit the metadata changes

## Installing in KiCAD

Users can install your library in KiCAD using the Plugin and Content Manager:

1. Open KiCAD
2. Go to Plugin and Content Manager
3. Add your repository URL
4. Install your library package

Alternatively, users can download the release zip file and manually install it.

## License

MIT