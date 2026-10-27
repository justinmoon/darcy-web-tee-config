# Darcy web TEE configuration

Public deployment policy and release workflows. No app source, owner access key,
provider key, or user data belongs here.

The app runs as a standard Tinfoil container deployment with an encrypted
persistent workspace. It does not use a separate activation service. Release
tags and this repository's current branch can differ from the running release;
read the verified deployment evidence before sending private data.

The browser pins the expected repository and measured release before sending its
owner key or chat. The owner key unlocks the encrypted workspace. The Rust
supervisor holds the inference credential and verifies its Tinfoil connection
against a separate release pin. Tools run without either key.

## Release and deploy

1. Build and review the runtime image in the private app workspace. Attach its
   archive to an `image-*` release. Run **Publish reviewed runtime image** with
   that release tag and the reviewed archive SHA256.
2. Put the resulting immutable GHCR image digest in `tinfoil-config.yml`. Review
   the inference release pin, origin, phone flag, owner-key hash, disk policy and
   secret names. Never put a raw owner key or provider key in the configuration.
3. Run **Tinfoil Release** with a fresh version. Its pinned measurement action
   produces the measured release evidence. Deploy that release through Tinfoil's
   normal container deployment path, keeping the existing encrypted disk.
4. Update the reviewed frontend's exact release digest and configuration repo
   pin. Check live attestation before owner unlock, then test chat, persistence
   after restart and phone pairing when enabled. A successful workflow alone
   does not prove the deployed application works.

`DARCY_MARMOT_ENABLED` controls the optional phone transport. The same Rust agent
handles web and phone tasks. After a compute restart, an owner must first unlock
the disk through the verified browser app. Pair only a public phone npub and
compare the invitation sender with the Darcy identity shown in that app.

`DARCY_INITIALIZE_NEW_VOLUME` is for a new blank disk only; keep it off for normal
operation. The runtime fails closed if the encrypted workspace is unavailable.
Deleting the volume destroys state; publishing a new image is not a backup.

## Trust and recovery

The runtime needs `cvm_admin: true`; its supervisor, sandbox policy and image
form part of the trusted code. Run one owner per CVM. TEE hardware/firmware,
attestation services/verifiers and the build/dependency chain remain trusted.
Attestation binds the measured deployment; it does not establish that every
program is free of flaws or prevent storage rollback.

A changed browser bundle can read text before encryption. Keep the client fixed
and free of third-party scripts. The frontend host, browser and device remain
trusted. GitHub Pages project paths share an origin with other projects on that
account; a dedicated app domain narrows that trust. Native phone clients use the
identity paired through the verified browser; they do not independently attest
the TEE. Relays see metadata and ciphertext.

No key recovery exists in this prototype. Keep a private backup of the random
owner key. Losing it loses access to the workspace. Persistence is not disaster
recovery: a full encrypted backup/restore flow still needs implementation and a
restore test. Download important results separately in the meantime.

An owner-approved, attested TEE debugger is a future design, not an installed
service or an operator access path in this release.
