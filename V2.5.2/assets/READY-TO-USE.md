# ICTApp V2.5.2 Assets — Ready to Use

This package preserves the existing V2.5.2/assets folder structure and filenames.

## Changes
- `icons/*.svg`: removed decorative yellow accent dots; retained the original navy line-art icons.
- `app-icons/*.png`: original production-size files retained for compatibility with existing references.
- `app-icons/hd/*-1024.png`: 1024×1024 HD master exports for design/review and future resizing.
- `logos/`, `design-system/`, manifests and existing documentation retained.

## Integration
Replace the current `V2.5.2/assets/` directory with this package's `assets/` directory.
Existing references to the original filenames continue to work.
Use the 1024px files when a high-resolution source is needed. Tiny favicons remain at their required 16×16 and 32×32 sizes.
