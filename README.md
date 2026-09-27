# FinOps Multitool TUI

An **unofficial, standalone preview snapshot** of the FinOps Multitool terminal
UI, shared so you can download and try it while toolkit availability is pending.
This is not an official Microsoft release, not the full
[FinOps toolkit](https://github.com/microsoft/finops-toolkit), and not the older
WPF Multitool.

The runtime retains its **v15.0-dev-wip.20260924.1** version label. Preview status does not
imply production support or complete live-data validation. This repository
contains a clean source snapshot, not previous private development history or
test release archives.

## Download

- [Download the source ZIP](https://github.com/z-larsen/FinOps-Multitool-TUI/archive/refs/heads/main.zip)
  and extract it into a **new folder**. GitHub normally names that folder
  `FinOps-Multitool-TUI-main`.
- Alternatively, clone `https://github.com/z-larsen/FinOps-Multitool-TUI.git`.

No GitHub account is required to download the public source. Keep `Public`,
`Private`, and `en-US` together with the root files; do not copy just the public
entry-point script.

## Prerequisites

- **PowerShell 7** (`pwsh`), not Windows PowerShell 5.1. The separate Azure Tool
  Launcher requires **PowerShell 7.4+**.
- PowerShell modules **Az.Accounts**, **Az.ResourceGraph**, and **Az.Storage**,
  installed in the PowerShell 7 environment.
- Azure sign-in and appropriate access to the scope being scanned. Reader is a
  starting point; billing, cost, storage export, and hub data can require
  additional permissions. See the
  [Multitool documentation](Private/FinOpsMultitool/README.md) for details.
- Network access to Azure and any data-source/dependency endpoints used by your
  selections. This is not an offline scanner.

Windows is the documented launch path for this distribution. Native Linux and
macOS operation has not been verified for this public snapshot.

## Start the TUI

Open a fresh PowerShell 7 terminal in the extracted folder. After reviewing and
trusting the downloaded code, unblock the PowerShell files if Windows marked
them as downloaded:

```powershell
Get-ChildItem -LiteralPath . -Recurse -File |
    Where-Object Extension -in '.ps1', '.psm1', '.psd1' |
    Unblock-File
```

Load the public entry point, then call the function:

```powershell
$ErrorActionPreference = 'Stop'
. .\Public\Start-FinOpsMultitool.ps1
Start-FinOpsMultitool
```

Running the public file with `-File` alone only defines the function; it does
**not** open the TUI. Interactive startup handles authentication and lets you
select subscriptions, data sources, and scans. Numbered prompts are used when
the terminal cannot support arrow-key menus.

For an unattended run, authenticate separately with the intended Azure identity
first, then use `-NonInteractive` and explicit scope/scan parameters. Do not
assume the current Azure context belongs to the intended tenant.

The root `FinOpsToolkit.psm1` loader is also included for compatibility. Importing
it exports the additional toolkit commands packaged in this snapshot, including
commands unrelated to the TUI. The direct entry-point instructions above avoid
importing that broader command surface. Only the Multitool scan modules are
described as read-only; this is not a blanket statement about every packaged
toolkit command.

## Reports and privacy

Scans read Azure data and write **local reports**. By default, each run creates
a private timestamped directory beneath `FinOpsToolkit\Multitool\Reports` in
the current user's local application data directory. It contains CSV results,
an HTML report, and a text summary.

Reports can include tenant/subscription identifiers, resource names, tags,
billing information, and costs. Do not upload reports, credentials, access
tokens, screenshots with sensitive data, or customer information to this
repository or its issues. Keep custom output paths outside Git repositories,
network shares, and OneDrive or other synced folders.

Missing access, incomplete reads, unavailable forecasts, and API throttling can
affect results. Inspect reported errors and scan status before relying on a
result; a preview scan is not a substitute for reviewing Azure's source data.

## Use with Azure Tool Launcher

Download [Azure Tool Launcher](https://github.com/z-larsen/AzureToolLauncher)
and set its local configuration's `FinOpsToolkitRoot` to this extracted folder.
For sibling folders named `AzureToolLauncher` and `FinOps-Multitool-TUI`:

```powershell
FinOpsToolkitRoot = '..\FinOps-Multitool-TUI'
```

That setting belongs **inside the launcher's PSD1 configuration**, not at a
PowerShell prompt. Use `..\FinOps-Multitool-TUI-main` instead if you kept the
ZIP's original folder name. The launcher recognizes the standalone `Public`
and `Private` layout as well as a toolkit checkout's `src\powershell` layout.

**FTKLocal is not bundled in this snapshot.** The existing FTKLocal scripts
expect a full toolkit source checkout with `src\powershell`. Keep that checkout
configured for FTKLocal, or leave `FTKLocalScript` empty when using only this
standalone preview. Synthetic hub costs do not make other scans offline or
prevent access to real Azure tenant information.

## Scope and validation

This is the runtime distribution, not the full toolkit test suite or CI setup.
Test links in the [detailed documentation](Private/FinOpsMultitool/README.md)
refer to the full toolkit checkout. Publishing or loading the code does not
prove that every Azure scan works for your identity and data source.

Use a test scope first and review results before wider use. Report reproducible
issues with sanitized details only.

For this public packaging, all 79 PowerShell files passed syntax parsing, the
KPI catalog parsed as JSON, and the root module and public entry point loaded
without running scans. The bundled empty Power BI template had its opaque
encrypted `SecurityBindings` entry and corresponding content-type reference
removed; all other template payloads were preserved. The sanitized template
opened successfully in Power BI Desktop with all four report pages present.
These are local packaging checks, not end-to-end Azure scan tests.

## License

Distributed under the [MIT License](LICENSE). Original Microsoft copyright and
license notices are preserved.
