# mta-sts-rybbt-com

The [MTA-STS](https://www.rfc-editor.org/rfc/rfc8461) policy for `rybbt.com`, served by
GitHub Pages at <https://mta-sts.rybbt.com/.well-known/mta-sts.txt>.

MTA-STS lets a domain tell sending mail servers to deliver only over TLS, and only to a
server holding a valid certificate for one of the MX hosts the policy lists. Senders
discover the policy through a DNS record and fetch it over HTTPS from `mta-sts.<domain>`.

## Do the same for your domain

1. Create a repository containing three files:
   - `docs/.well-known/mta-sts.txt`, the policy, with one `mx:` line per MX host:

     ```
     version: STSv1
     mode: testing
     mx: <your MX host>
     max_age: 86400
     ```

   - `docs/CNAME`, a single line: `mta-sts.<your domain>`
   - `docs/.nojekyll`, empty. Without it Pages does not publish `.well-known`.
2. In the repository settings, enable GitHub Pages from the `docs/` folder of `main`.
3. Add two DNS records. If your DNS provider can proxy records, leave the CNAME unproxied
   so GitHub can issue the certificate.

   | Type | Name | Value |
   |------|------|-------|
   | CNAME | `mta-sts.<your domain>` | `<owner>.github.io` |
   | TXT | `_mta-sts.<your domain>` | `v=STSv1; id=<any new value, such as a timestamp>` |

4. When Pages shows the certificate as issued, turn on **Enforce HTTPS** and check that
   the policy URL loads.
5. `testing` mode only reports problems. To receive the reports, add a TXT record at
   `_smtp._tls.<your domain>` with `v=TLSRPTv1; rua=mailto:<address>`. When the reports
   are clean, change `mode` to `enforce`.

Senders cache the policy. Change the `id` in the TXT record every time the policy file
changes, or they will not fetch the new one.
