# Development APKs

The **Android dev APKs** Actions workflow builds ARM64 on code pushes to
`android`. Documentation-only pushes are skipped. Older in-progress builds are
cancelled when a newer code commit arrives. **Run workflow → all_abis** builds
all four ABIs. Pull requests do not trigger this workflow.

These APKs use the optimized Rust `release` profile (including ThinLTO), public
payload checks and official resource downloads. The package is
`dev.horizon.android.dev`, separate from the normal release, and the visible
version contains the run number and source commit. Test data is separate;
worlds and accounts are not automatically copied from the release app. The
workflow does not publish GitHub Releases or send Discord messages.

## Signing and installation

Every workflow run generates a disposable development signing key on the
GitHub runner. No maintainer signing secret is required, and the key is deleted
when the job finishes. Because each run gets a different signing identity, a
new APK generally cannot update an earlier dev APK in place: uninstall the
previous **dev** build before installing a newer one. Back up any test worlds
and test data first. The normal Horizon release app has a separate package and
is not affected.

Do not upload or share the signed key or local build inputs. Only the APK,
checksums, license notices and `BUILD.json` are uploaded as the workflow
artifact.

## Sharing with testers

Open Actions → Android dev APKs → the successful run → Artifacts. Download and
unpack the artifact ZIP, then install the APK. GitHub requires sign-in to
download workflow artifacts; artifacts expire after 14 days. Each artifact
contains checksums, license notices and `BUILD.json` identifying the exact
source and build profile. Experimental builds have not automatically passed
gameplay or performance tests.

Private signing keys, raw Minecraft inputs, prepared resource directories,
player data and local diagnostics are not uploaded as downloadable artifacts.
Only Cargo/Gradle build caches are retained separately for subsequent CI
builds. Cold builds also prepare generated engine tables and may take
substantially longer than an incremental local build.
