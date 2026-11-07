# Releasing this fork

Fork of `herdrdev/herdr-app` that publishes macOS Intel builds to GitHub Releases.
The fork's own files: `.github/workflows/release-fork.yml`, `scripts/package-macos-zip`,
this file. Upstream's `release.yml` and `ci.yml` are disabled in the fork's Actions.

## Release

```sh
git checkout fork
version=$(sed -n 's/^version = "\(.*\)"/\1/p' Cargo.toml | head -1)
git tag "intel-v$version"
git push origin fork "intel-v$version"
```

The workflow builds on `macos-15-intel`, checks the tag matches `Cargo.toml`, and
creates a Release with `Herdr-<version>-macos-x86_64.zip`. Bump `version` in
`Cargo.toml` (or take upstream's bump) before tagging a new version.

## Take a new upstream version

```sh
git fetch upstream
git rebase upstream/master   # on branch fork
git push --force-with-lease origin fork
```

## Build locally

```sh
ZIG="$(brew --prefix zig@0.16)/bin/zig" scripts/package-macos-zip
```

Output: `dist-fork/Herdr-<version>-macos-<arch>.zip`. Needs `git submodule update --init --depth 1 vendor/ghostty`
and Zig 0.16.x.

## Installing a downloaded build

The app is ad-hoc signed, not notarized. Unzip, then
`xattr -dr com.apple.quarantine Herdr.app`, and move it to `/Applications`.
