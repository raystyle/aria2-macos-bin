# aria2-macos-bin

Prebuilt `aria2c` for macOS (Intel x86_64 + Apple Silicon arm64), published by GitHub Actions.

## Assets

| File | Arch |
|------|------|
| `aria2c-darwin-x86_64.tar.gz` | Intel |
| `aria2c-darwin-arm64.tar.gz` | Apple Silicon |

## Install (dotfiles)

```bash
~/dotfiles/aria2/install.sh
# or
ARIA2_GITHUB_REPO=raystyle/aria2-macos-bin ~/dotfiles/deploy.sh --only aria2
```

## Build

```bash
gh workflow run build-release.yml -R raystyle/aria2-macos-bin
```

Or tag: `git tag v1.37.0 && git push --tags`

See `BUILD.md` for details.
