# lsdf-automation-modules

Public host for **binary releases** (`.zip` packages) of PowerShell modules consumed by Azure Automation runbooks.

## Why a public repository?

Azure Automation Accounts can only ingest custom PowerShell modules via a publicly accessible HTTP URL (the `ContentLinkUri` parameter of `New-AzAutomationModule`). GitHub Releases provides this URL natively, with versioning, full history, and trivial rollback — at zero cost and with no infrastructure to maintain.

No secrets, credentials, or sensitive data are published here — only the source of generic utility libraries (HTML rendering helpers, logging helpers, etc.).

## Repository contents

The `main` branch contains only this README. **Module packages (`.zip` files) are stored as GitHub Release assets** — see the [Releases](../../releases) tab.

## Tag naming convention

`<ModuleName>-v<SemVer>` (dash separator, not slash, to avoid URL-encoding issues), e.g.:

- `MyModule-v1.0.0`
- `MyModule-v1.1.0`

Prefixing each tag with the module name allows multiple modules to coexist in the same repo without tag collisions.

## Publication workflow

A private deployment script reads the module's `.psd1` manifest, packages it as a `.zip`, and creates a corresponding GitHub Release with the asset attached. The asset's stable download URL is then passed to `New-AzAutomationModule -ContentLinkUri` for ingestion into the target Automation Account.

Releases are **immutable**: rolling back to an earlier version is done by simply re-importing the module from the URL of an older tag.
