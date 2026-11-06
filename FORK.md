# Maintained fork

Version 16.0.18.1 is based on upstream release 16.0.18 and its subsequent README
update. It normalizes Telnyx SMS and MMS senders to exactly one leading plus.
Digit-only senders, destination shortcodes and other providers keep their
existing behavior. The public upstream proposal is
https://github.com/simontelephonics/smsconnector/pull/98.

The `fix/telnyx-e164-source` branch contains the upstream-ready fix and regression
test. The `release/gnet-16.0` branch adds the fork version and its native FreePBX
update manifest. The installed module's `updateurl` points to `module.json` on
that release branch; its `location` points to the versioned GitHub release
archive. The upstream PR does not include this fork-specific packaging.

## Release verification

Run `php tests/TelnyxSourceFormattingTest.php`. The test uses fake HTTP and no
real credentials or database; it checks SMS/MMS source variants, destination
shortcodes and provider rejection behavior. It is CLI-only. Also run PHP syntax
checks and `git diff --check`, compare with upstream history, and confirm that
the source archive contains the reviewed commit before publishing it.

Before promoting an update, verify the installed native call-history API and
an actual outbound SMS. Do not infer carrier delivery from a local checkmark.
Incoming callbacks and publicly reachable outbound MMS media are separate
deployment requirements. Keep unattended updates held until the new build has
been reviewed; do not replace it directly with an upstream archive while the
fix is still needed. Rebase upstream changes and rerun the affected checks for
each fork release. Once upstream includes the correction, compare behavior
before intentionally moving back to that update channel.

## Integrity and rollback

This fork is not signed with the upstream publisher's private signing key.
The original signature file is retained, so FreePBX may report modified or
untrusted files for the fork. Keep signature and file-integrity checks enabled;
do not suppress those notices to make the installation appear vendor-signed.
Verify the published release SHA-256 against the reviewed build before install.

Preserve an exact private module/configuration backup on the target PBX before
installation. Roll back using that backup if native history or a live outbound
SMS test regresses. The module archive and this repository contain code only;
PBX configuration, provider credentials, phone numbers and logs stay outside
the repository and release.
