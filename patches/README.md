# StampIT patches

This fork adapts OpenSCToken for Bulgarian StampIT qualified-signature cards
(IAS-ECC, IDEMIA; since 2026 also Gemalto IDPrime 940). These cards do not expose SDO metadata via `GET DATA`
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
- `0003-opensc-idprime-reselect-applet.patch` — applied by CI after 0001.
  An IDPrime 940 starts in its default applet after a reset, and macOS resets
  an idle card after ~10 s, so VERIFY then fails with `6D00`. The IDPrime
  driver now checks the PIN status when it obtains the reader lock and
  re-selects the IDPrime applet only if that applet does not answer.
