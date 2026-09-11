# mta-sts.crisinel.ch

Serves the MTA-STS policy (RFC 8461) for the mail domain `crisinel.ch`, whose MX
is Thundermail.

Published via GitHub Pages at
<https://mta-sts.crisinel.ch/.well-known/mta-sts.txt>.

## Why this repo exists

MTA-STS needs two things: a `_mta-sts` TXT record in DNS, and the policy itself
served over HTTPS on the `mta-sts.<domain>` host with a publicly trusted
certificate. The TXT record was already in place, but Thundermail does not
provision that host for customer domains, so the policy was unfetchable and
senders fell back to no-policy (confirmed by Google TLS-RPT reporting
`no-policy-found`). GitHub Pages provides the host and the certificate.

## Layout

| File | Role |
|------|------|
| `.well-known/mta-sts.txt` | the policy — the only file that matters |
| `CNAME` | binds the Pages site to `mta-sts.crisinel.ch` |
| `.nojekyll` | without it Jekyll strips dot-directories and `.well-known` 404s |
| `index.html` | courtesy landing page |

## Changing the policy

Any edit to `.well-known/mta-sts.txt` **must** be paired with a new `id` in the
DNS TXT record — that is the only signal senders have to refetch a cached
policy.

```bash
# 1. edit .well-known/mta-sts.txt, commit, push
# 2. bump the id in _mta-sts.crisinel.ch TXT (Infomaniak zone)
```

Current DNS side, for reference:

```
_mta-sts.crisinel.ch.   TXT   "v=STSv1; id=<id>"
_smtp._tls.crisinel.ch. TXT   "v=TLSRPTv1; rua=mailto:postmaster@crisinel.ch"
```

`mode: testing` means failures are reported via TLS-RPT but mail is still
delivered. See the *MTA-STS* section of `../README.md` for the promotion path to
`mode: enforce`.
