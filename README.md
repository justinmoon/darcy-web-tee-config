# Darcy web TEE configuration

Public deployment policy and release workflows. No app source, owner access key,
provider key, or user data belongs here.

Status: deployment preparation. No measured release or running app yet.

The browser pins a measured release before sending its owner key or chat. The
owner key unlocks the encrypted workspace. The Rust supervisor holds the inference
credential and verifies its Tinfoil connection. Tools run without either key.

The runtime needs `cvm_admin: true`; the supervisor and its image form part of the
trusted code. Run one owner per CVM. A changed browser bundle can read text before
encryption; keep the client fixed and free of third-party scripts.

No key recovery exists in this prototype. Keep a private backup of the random
owner key. Losing it loses access to the workspace.
