# Publish WLAN Commander Sensor images and documentation

This is the maintainer publication contract. User instructions live at
[the installation guide](https://wlancommander.com/guides/unified-sensor-image/)
and [the screenshot console guide](https://wlancommander.com/guides/sensor-console/).

## Where each change belongs

| Change | Maintained source | Public destination |
| --- | --- | --- |
| Image, kernel patches and runtime | Sensor build repository, `packaging/unified/` | Immutable GitHub release assets and corresponding-source archive |
| User guide and feature copy | Website repository, `content/pages/*.md` | Generated pages at wlancommander.com |
| Shared layout/navigation | Website `templates/`, `scripts/build.py`, `data/` | Generated `public/` |
| Guide screenshots | Website `public/assets/guides/`, with private QA originals elsewhere | Redacted PNGs linked by guide Markdown |
| App/portal/sensor update offer | Website `data/releases.json` | Generated `public/releases.json` |
| Factory download panel | Website `data/unified-factory.json` | Generated Downloads page |
| GitHub overview | This repository's `README.md` | Repository landing page |
| Legacy GitHub Pages URLs | This repository's redirect HTML | Current canonical guide/field-note URLs |

Do not hand-edit generated HTML, `public/releases.json`, or `search-index.json`.
Preserve substantial local work: use an isolated checkout and record its revision.
Do not mirror an entire dirty private workspace into this public repository.

## Documentation-only publication

1. Edit the maintained Markdown and any shared layout/assets. Keep detailed
   existing guides and field notes; preserve old URLs or add redirects.
2. Record which image/app version each screenshot actually shows. An older
   screenshot can illustrate unchanged controls, but must not be presented as
   proof of a new release or new physical-phone test. Label mock/simulator views.
3. Before publication, cover SSIDs, BSSIDs, MACs, public IPs, device identities,
   account/email details, logs and credentials with opaque redactions. Hide the
   complete setup QR and one-time code, including any URL fragment. Inspect the
   final raster, metadata and thumbnail. Keep unredacted originals out of
   `public/`, GitHub release assets and public receipts. Never use a live setup
   code as an example.
4. From the website checkout, use its pinned environment and run:

   ```sh
   .venv/bin/python scripts/build.py
   ./scripts/check-catalogue.sh
   .venv/bin/python scripts/validate.py
   .venv/bin/python scripts/validate_content.py
   npm run check:js
   ```

   A worktree without `.venv` can use the verified parent checkout's Python
   environment. Do not omit validation because generated pages look correct.
5. Review desktop and narrow-screen output. Commit maintained sources and
   rebuilt `public/` together. Deploy **only `public/`** to Cloudflare Pages:

   ```sh
   npx --yes wrangler@4.131.0 pages deploy public \
     --project-name wlancommander --branch redesign-preview
   # After preview validation:
   npx --yes wrangler@4.131.0 pages deploy public \
     --project-name wlancommander --branch production
   ```

6. Fetch the affected routes and images from wlancommander.com and compare
   their bytes/hash to the committed output. Confirm redirects, navigation,
   screenshots, privacy/security headers and search results. Save the deployment
   ID, website commit and public readback receipt. A local preview is not a
   completed public documentation update.
7. Update this GitHub overview/redirects when hardware or setup directions
   change, and publish a separate documentation commit. Do not rebuild an image
   for wording-only website changes.

## Image release publication

Use a new version and tag (`unified-vVERSION`); never replace a published
version's assets. A beta stays `channel: beta` and a GitHub prerelease. The
owner waived the full GA matrix for the October 2026 public beta; state every
remaining hardware/feature limit. A waiver does not claim that a check passed.
Pro OTG tests are deferred, not a promised charging fix.

1. Build through `packaging/unified` with exact input pins. Save the runtime,
   kernel, build-tool and any distribution-tool revisions separately. Require
   read-only image checks on both factory slots, boot/initramfs consistency,
   matching factory/OTA roots, and artifact integrity. Candidate 12 is not a
   qualified base. Record exact-candidate boot/runtime checks and scoped results.
2. Verify each image's SHA-256 and size, sign each independently using the
   existing Mac-held release key, and verify against the public key shipped in
   the image. The key never goes on a build server or into public assets. If
   recompression is needed, do it before signing and verify the full decompressed
   image hash is unchanged. Keep each GitHub asset below its per-file limit.
   Never advertise an optional `.p7` chain unless it exists.
3. Prepare a public release manifest and notes with artifact hashes/sizes,
   source/kernel revisions, actual tests and remaining limits. Package the
   exact corresponding sources, patches, dependency pins, licenses and build
   instructions. Exclude private credentials, raw device logs and recovery
   backups. Include the full corresponding C5 firmware source when distributing
   that firmware. A screenshot or signed image alone is not corresponding source.
4. Create a **draft** GitHub prerelease in `WiFivomFranMan/wlan-commander-sensor`.
   Attach factory and OTA archives, both signatures/checksums, corresponding
   sources and public manifest. Review the complete draft before publishing.
   Leave legacy stable/board-specific releases untouched.
5. Publish the GitHub prerelease **before** making an updater offer. Download
   the public factory and OTA assets independently, stream their full hashes,
   and verify the downloaded signatures with the shipped public key. Compare
   every byte count/digest with the local release manifest. A HEAD request,
   GitHub asset listing or checksum sidecar alone is insufficient.
6. In the website checkout, update the factory record from the actual artifact
   (size and SHA-256 computed, never typed), then preview the OTA entry:

   ```sh
   # VERSION and ARTIFACT_DIR refer to the reviewed, signed local release.
   # PUBLIC_KEY is the exact public key shipped in the image; never the private key.
   python3 scripts/update_unified_factory.py \
     --version "$VERSION" \
     --image "$ARTIFACT_DIR/wlan-commander-sensor-$VERSION-factory.img.xz" \
     --public-key "$PUBLIC_KEY"
   # Review the computed factory diff; repeat with --yes to write it.
   python3 scripts/update_catalogue.py \
     --product wlan-commander-sensor --version "$VERSION" \
     --channel beta \
     --image "$ARTIFACT_DIR/wlan-commander-sensor-$VERSION-ota.img.xz" \
     --sig "$ARTIFACT_DIR/wlan-commander-sensor-$VERSION-ota.img.xz.sig" \
     --download-url "https://github.com/WiFivomFranMan/wlan-commander-sensor/releases/download/unified-v$VERSION/wlan-commander-sensor-$VERSION-ota.img.xz" \
     --checksum-url "https://github.com/WiFivomFranMan/wlan-commander-sensor/releases/download/unified-v$VERSION/wlan-commander-sensor-$VERSION-ota.img.xz.sha256" \
     --install-url https://wlancommander.com/guides/unified-sensor-image/ \
     --notes-url "https://github.com/WiFivomFranMan/wlan-commander-sensor/releases/tag/unified-v$VERSION"
   # Review the diff; repeat the same command with --yes to write data/releases.json.
   ```

   `data/unified-factory.json` must contain this version's **factory** URL,
   computed `size_bytes`, `sha256`, and matching `.sha256`/`.sig` URLs. The
   catalogue must contain the independently hashed **OTA**. The site build
   rejects mismatched versions. Retain `nanopi-zero2`, `wlanpi-go`, `wlanpi-plus`
   and the separate stable ESP32-C5 firmware entry. New experimental C5 firmware
   in a sensor image must not silently replace stable BLE firmware downloads.
7. Update user guides for the actual image: setup routes, hardware profiles,
   tools, known limits and genuine screenshot versions. Run all documentation
   gates above, inspect the diff, and commit. Save the previous catalogue and
   factory record for recovery. Use a minimum supported version only for a real
   verified incompatibility/key transition; do not infer a key rotation.
8. Deploy `public/` as above, then require:

   ```sh
   python3 scripts/verify_published.py \
     --url https://wlancommander.com/releases.json \
     --expect "wlan-commander-sensor=$VERSION"
   ```

   Do not use `--no-verify-digest` for acceptance. Confirm the live catalogue
   matches the committed bytes, the full OTA digest passes, and the Downloads
   page offers the matching factory version. Inspect a running sensor's updater
   result and the app's parsed catalogue/version comparison where available;
   record CLI, browser, simulator and physical phone evidence separately.
9. Save a final publication receipt: image and website revisions, GitHub tag,
   deployment ID, public asset/guide/catalogue checks, channel and outstanding
   limits. Publication is complete only after public readback succeeds.

## Current automation boundary

The website checkout currently has **no GitHub remote**; Cloudflare Pages is
an explicit ad-hoc deployment, not a git-triggered build. Its checked-in
`.github/workflows/release.yml` is a template, not an exercised release service.
It currently updates only the OTA entry and does not download/update the factory
record. **Do not dispatch it for a unified release as-is.** The local sequence
above is the working route. To enable CI, first add factory asset/metadata and
signature checks, configure a real remote and scoped deployment secrets, and
exercise a dry run. Never copy private signing keys into CI.

This README/runbook repository push does not deploy wlancommander.com. A
website deploy does not create a GitHub release. A GitHub prerelease does not
automatically update the apps. The version, immutable assets and public catalogue
connect those three destinations.

## Recovery from publication failure

If upload or public asset verification fails, leave the previous catalogue
active. If deployment verification fails, restore the saved website catalogue,
factory record and matching generated pages in a new corrective commit and
redeploy; investigate before offering the version again. Keep published bytes
immutable and annotate/withdraw a bad release without reusing its version.
Never direct devices to bypass signature, layout, associated-station or health
gates. Keep recovery images and protected device backups.
