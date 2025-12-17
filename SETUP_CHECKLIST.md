# Setup Checklist

Use this checklist to ensure your KiCad library is properly configured and ready for releases.

## Initial Configuration

- [ ] **Customize metadata.json**
  - [ ] Update `name` field with your library name
  - [ ] Update `description` with a short description
  - [ ] Update `description_full` with detailed information
  - [ ] Update `identifier` to match format: `com.github.USERNAME.REPOSITORY`
  - [ ] Update `author.name` with your name
  - [ ] Update `author.contact.web` with your website/GitHub URL
  - [ ] Update `maintainer` information (can be same as author)
  - [ ] Verify `license` is correct (default: MIT)
  - [ ] Update `resources.homepage` URL
  - [ ] Update `resources.repository` URL
  - [ ] Keep `versions` as empty array `[]`

- [ ] **Review repository.json**
  - [ ] Update `name` field for repository name
  - [ ] Update `maintainer` information
  - [ ] Keep `packages` as empty array `[]`

- [ ] **Review LICENSE file**
  - [ ] Update copyright year if needed
  - [ ] Update copyright holder name
  - [ ] Or replace with different license if preferred

## Add Library Content

- [ ] **Create directory structure**
  - [ ] Create `symbols/` directory
  - [ ] Create `footprints/` directory
  - [ ] Create `3dmodels/` directory (optional)

- [ ] **Add your symbols**
  - [ ] Add `.kicad_sym` files to `symbols/` directory
  - [ ] Verify symbol files are valid KiCad format

- [ ] **Add your footprints**
  - [ ] Create `.pretty` directories in `footprints/`
  - [ ] Add `.kicad_mod` files to each `.pretty` directory
  - [ ] Verify footprint files are valid KiCad format

- [ ] **Add 3D models (optional)**
  - [ ] Add `.step`, `.wrl`, or other 3D model files to `3dmodels/`
  - [ ] Ensure footprints reference correct 3D model paths

## Commit Initial Setup

- [ ] **Stage all changes**
  ```bash
  git add .
  ```

- [ ] **Commit changes**
  ```bash
  git commit -m "Initial library setup with symbols and footprints"
  ```

- [ ] **Push to GitHub**
  ```bash
  git push
  ```

## Create First Release

- [ ] **Create version tag**
  ```bash
  git tag v1.0.0
  ```

- [ ] **Push tag to trigger release workflow**
  ```bash
  git push origin v1.0.0
  ```

- [ ] **Verify workflow execution**
  - [ ] Go to Actions tab on GitHub
  - [ ] Check "Create Release" workflow completed successfully
  - [ ] Check "Update Metadata" workflow completed successfully

- [ ] **Verify release created**
  - [ ] Go to Releases section on GitHub
  - [ ] Verify release v1.0.0 exists
  - [ ] Download and verify zip file contains your library

- [ ] **Verify metadata updated**
  - [ ] Pull latest changes: `git pull`
  - [ ] Check `metadata.json` has version 1.0.0 entry
  - [ ] Check `repository.json` is populated
  - [ ] Verify SHA256 hash is present
  - [ ] Verify download URL is correct

## Test in KiCad (Optional)

- [ ] **Add repository to KiCad PCM**
  - [ ] Open KiCad
  - [ ] Go to Tools → Plugin and Content Manager
  - [ ] Settings → Add repository
  - [ ] Add URL: `https://raw.githubusercontent.com/USERNAME/REPOSITORY/main/repository.json`

- [ ] **Install your library**
  - [ ] Find your library in PCM
  - [ ] Click Install
  - [ ] Verify installation completes

- [ ] **Test library in project**
  - [ ] Create test project
  - [ ] Open symbol editor
  - [ ] Verify your symbols are available
  - [ ] Open footprint editor
  - [ ] Verify your footprints are available

## Ongoing Maintenance

- [ ] **For each new release:**
  - [ ] Update your library content
  - [ ] Commit changes
  - [ ] Create new version tag (e.g., `v1.1.0`)
  - [ ] Push tag to GitHub
  - [ ] Verify workflows run successfully
  - [ ] Verify metadata is updated

## Troubleshooting

If something doesn't work:

1. **Check workflow logs**
   - Go to Actions tab
   - Click on failed workflow
   - Review error messages

2. **Verify file structure**
   - Ensure directories exist: `symbols/`, `footprints/`
   - Ensure files are in correct format

3. **Validate JSON files**
   ```bash
   python3 -m json.tool metadata.json
   python3 -m json.tool repository.json
   ```

4. **Check tag format**
   - Must be `v*.*.*` (e.g., `v1.0.0`, `v2.1.3`)
   - Use semantic versioning

5. **Review documentation**
   - See README.md for overview
   - See QUICKSTART.md for detailed guide
   - See IMPLEMENTATION_SUMMARY.md for technical details

## Resources

- [KiCad PCM Documentation](https://dev-docs.kicad.org/en/addons/)
- [KiCad Library Conventions](https://klc.kicad.org/)
- [Semantic Versioning](https://semver.org/)
- Repository README.md
- Repository QUICKSTART.md
- Repository IMPLEMENTATION_SUMMARY.md
