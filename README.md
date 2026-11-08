# Waddle Nest

The package repository ("Nest") for **Waddle Linux**, the little distro starring Pip the mint penguin.

- `INDEX` lists every egg (package): name, version, dependencies, file, SHA-256 and size.
- `INDEX.sig` is the Ed25519 signature of `INDEX`. Waddle checks it with the public key built into Waddle,
  and checks each egg's SHA-256 against the signed INDEX, so nothing here can be changed without Waddle noticing.
- `eggs/` holds the eggs. Every one is built from original source code by Waddle's `hatch` tool.
- `waddle-system-*.egg`, when present, is Waddle itself (kernel + system), used by `sudo waddle upgrade`.

On Waddle: `sudo waddle nest https://raw.githubusercontent.com/benjaminsoble91-eng/waddle-nest/main`
