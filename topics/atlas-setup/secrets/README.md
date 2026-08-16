# Secrets

Files in this folder are **encrypted at rest** in git (GitHub sees
ciphertext) but stay **plaintext in your working tree**. This is
handled transparently by the `aicrypt` git filter — normal
`git commit` / `git pull` just works once the repo is set up.

One-time setup per clone:

    axis crypt install    # register the git filter + guard
    axis crypt unlock     # enter your passphrase

Put secret notes/files here (e.g. `creds.md`). Never commit the
passphrase or the derived key file — only the non-secret salt in
`.ai/crypt.yaml` is committed.
