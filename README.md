# Ileen IDE Remote SSH Server

Public distribution repository for matching Ileen IDE Remote SSH server
packages. Application source code is maintained separately; this repository
contains distribution information, not the private IDE source.

## Downloads

Download packages from this repository's **Releases**, not from the source ZIP:

https://github.com/Hypaz-AI/ileen-remote-ssh-server-ide/releases

An empty Releases page means that no server release has been published yet.
Supported release targets are Linux x64/arm64 (glibc), macOS x64/arm64 and
Windows x64. A platform is available only when its archive is present in the
selected release. Alpine/musl and Windows ARM64 are not currently supported.

Each release is named `v<version>` and contains:

```text
ileen-server-<os>-<arch>-<full-IDE-commit>.tar.gz
SHA256SUMS.txt
```

Desktop and server must have the same full Ileen IDE build commit. Do not rename
an unrelated VS Code/VSCodium server package or disable commit validation to
make it appear compatible.

## Automatic installation

In Ileen IDE, use the bundled Remote SSH connection command and select an SSH
host. The extension downloads the matching archive from this repository on the
remote machine and installs it automatically.

Requirements on the destination include SSH, outbound HTTPS access to GitHub
release downloads, archive extraction tools and the platform prerequisites of
the bundled server. The first connection requires a published matching release.
No GitHub token is required to download public release assets.

The archive contains its own Node runtime, server code, bundled remote
extensions and license notices. Ileen authentication stays in the local IDE.
The server requires a connection token; SSH host-key and server-commit
verification must remain enabled.

For manual checksum verification after downloading a release:

```bash
sha256sum --check SHA256SUMS.txt
```

On macOS, use `shasum -a 256 --check SHA256SUMS.txt`. The SSH extension uses
HTTPS and matching-commit checks; it does not automatically verify this checksum
file.

## Publishing

The private Ileen IDE build workflow produces platform packages and publishes
them here using a repository-scoped token. This repository does not need
permission to read the private source repository.

To publish locally prepared packages using GitHub CLI:

1. Authenticate with `gh auth login`, using an account with release write
   access to this repository.
2. Commit and push this repository's documentation if it has no initial commit.
3. In the directory containing the prepared archives and `SHA256SUMS.txt`,
   verify the checksums.
4. Create the release with the exact version expected by the desktop:

```bash
gh release create "v<version>" \
  ileen-server-*.tar.gz SHA256SUMS.txt \
  --repo Hypaz-AI/ileen-remote-ssh-server-ide \
  --title "Ileen IDE SSH Server <version>" \
  --notes "Matching Remote SSH servers for Ileen IDE <version>."
```

For an existing release, use `gh release upload` instead, after verifying that
the version and full IDE commit match. Do not publish a partial platform set as
an all-platform release.

Archives staged in the local `dist/` folder are intentionally ignored by Git.
Upload them as release assets; do not commit large binaries or tokens.

### Locally prepared development package

The local clone contains a verified **Linux x64 only** package in `dist/`:

```text
ileen-server-linux-x64-988f1253c8edf9ec1951bed86377321619fd933e.tar.gz
SHA256SUMS.txt
```

Its expected download release is `v1.144.0-dev`. The build directory and a fresh
extraction of the archive both passed native startup, expected commit,
unauthenticated HTTP rejection, bundled-extension inventory and license checks.
A desktop-to-SSH-host connection has not yet been tested. Nothing has been
published by this local preparation.

After authenticating and pushing the initial documentation commit, run from
this repository's root:

```bash
cd dist
sha256sum --check SHA256SUMS.txt
gh release create v1.144.0-dev \
  ileen-server-linux-x64-988f1253c8edf9ec1951bed86377321619fd933e.tar.gz \
  SHA256SUMS.txt \
  --repo Hypaz-AI/ileen-remote-ssh-server-ide \
  --title "Ileen IDE SSH Server 1.144.0-dev (Linux x64)" \
  --prerelease \
  --latest=false \
  --notes "Development server for Linux x64 only; requires Ileen IDE build 988f1253c8edf9ec1951bed86377321619fd933e. No other remote platforms are included."
```

This package is not compatible with arbitrary existing Ileen desktop builds.
Build the desktop from the matching revision. Commit the build/packaging fixes
in the private source repository before making a production release; if the
source commit changes, rebuild both components for the new commit. Do not edit
the identity inside an existing archive to bypass matching.

## License

See `ILEEN-LICENSE.txt`, `LICENSE`, `ThirdPartyNotices.txt` and the `licenses/`
inventory inside each package. Ileen product licensing does not replace the
licenses or attribution of redistributed open-source components.
