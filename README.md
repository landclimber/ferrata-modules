# Ferrata modules

Ferrata's Logos module catalog: `logos-repo.json` (identity, the rolling `index` release in
`indexUrl`, the signer the releases are signed by in `trustedSigners`), the modules as git
submodules under `submodules/`, and the release workflows that build, sign, verify and publish each one with
[`logos-co/logos-modules-release-action`](https://github.com/logos-co/logos-modules-release-action)
(pinned to a commit). How it is published, and each lambda-prize release-integrity rule it meets:
`modules/catalog/RELEASE.md` in the source repository (https://github.com/rainbowpuffpuff/ferrata).

The modules are prototype, unaudited, not for production.

## Trust the signing key first

Logos clients do not trust a catalog's `trustedSigners` by themselves: a package's signature counts
only for a key in the installer's own keyring, and by default (`signature_policy: warn`) an unsigned
package, or one signed by a key the keyring lacks, installs with a warning. So, before installing:

1. Check the signer DID below against the one in the release-integrity manifest of the source
   repository (`releases/` of https://github.com/rainbowpuffpuff/ferrata), not only against this page:

       did:jwk:eyJjcnYiOiJFZDI1NTE5Iiwia3R5IjoiT0tQIiwieCI6ImNLVmpDSnFiWEFqYTBtenc1VFBOODhqWUs2QXkwTmtpRWNldUduekN6bGsifQ

2. Add it to the keyring and make signatures required (Logos Core's `logosctl`, per session):

       logosctl key add ferrata --did did:jwk:eyJjcnYiOiJFZDI1NTE5Iiwia3R5IjoiT0tQIiwieCI6ImNLVmpDSnFiWEFqYTBtenc1VFBOODhqWUs2QXkwTmtpRWNldUduekN6bGsifQ --display-name "Ferrata module releases"
       # in the daemon configuration (node.yaml), then `logosctl daemon config set ./node.yaml`
       # and restart the daemon (the policy takes effect at the next start):
       signature_policy: require

   With `lgpm`: `lgx keyring add ferrata did:jwk:eyJjcnYiOiJFZDI1NTE5Iiwia3R5IjoiT0tQIiwieCI6ImNLVmpDSnFiWEFqYTBtenc1VFBOODhqWUs2QXkwTmtpRWNldUduekN6bGsifQ`, then install with `--require-signatures`.
   Basecamp's package manager keeps its own keyring: add the key there too.

## Install

    logosctl catalog add https://raw.githubusercontent.com/landclimber/ferrata-modules/refs/heads/main/logos-repo.json
    logosctl catalog refresh
    logosctl search ferrata
    logosctl install ferrata_attest_module -y

The same URL works in the package-manager UI and with `lgpd`.
