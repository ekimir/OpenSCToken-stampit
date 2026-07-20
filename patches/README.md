# StampIT patches

This fork adapts OpenSCToken for Bulgarian StampIT qualified-signature cards
(IAS-ECC, IDEMIA). These cards do not expose SDO metadata via `GET DATA`
(every SDO read returns `6A88`), while the plain ISO 7816 commands (VERIFY,
MSE SET, INTERNAL AUTHENTICATE) work fine.

- `0001-opensc-iasecc-stampit-sdo-fallbacks.patch` — applied by CI to a pinned
  OpenSC checkout before building. Makes SDO lookup failures non-fatal in the
  IAS-ECC driver: PIN commands continue with an empty policy, and
  `set_security_env` falls back to a 2048-bit CHV-protected default.
- `0002-opensctoken-pkcs1v15-digestinfo-expired-filter.patch` — reference copy
  only; these changes are already committed in this tree. Activates the
  RSASSA-PKCS1-v1.5 digest algorithms by wrapping the digest in a software
  DigestInfo and signing via raw RSA-PKCS (the path IAS-ECC cards actually
  support), and filters expired certificates out of the keychain identities so
  signature pickers (Adobe Acrobat, browser client auth) can only select valid
  certificates.
