# Windows PowerShell 5.1 Startup Error: fnm Not Recognized

| Field | Details |
| --- | --- |
| Status | Partially Resolved — Installation Confirmed, Final Fix Unverified |
| Date resolved | Not recorded — complete resolution unverified |
| Category | Windows / PowerShell |
| Relevant hardware | Not recorded; architecture not recorded |
| Operating system and version | Microsoft Windows; exact version and build not recorded |
| Relevant applications | Windows PowerShell 5.1; Windows Package Manager (`winget`, version not recorded); Fast Node Manager (`fnm`) 1.39.0 installed during investigation |
| Node.js and npm | Reported version outputs: Node.js `v22.23.3`; npm `10.9.9` |
| Profile | `Microsoft.PowerShell_profile.ps1` in a OneDrive-managed Documents directory |
| Tested by / evidence source | Owner-supplied case report and explicit verified-evidence list from the original troubleshooting conversation; original transcript and screenshot not independently inspected for this article |
| Last reviewed | 2026-11-15 |

## Problem summary

Windows PowerShell 5.1 displayed `CommandNotFoundException` during startup because the first line of its profile invoked `fnm`, which the active shell could not resolve:

```powershell
fnm env --use-on-cd | Out-String | Invoke-Expression
```

The investigation checked command discovery and the package inventory, then installed Fast Node Manager through `winget`. Installation succeeded, but an immediate `fnm --version` check still failed in the existing session. Node.js and npm version checks succeeded.

> [!IMPORTANT]
> Package installation is confirmed; elimination of the startup error is not. Restarting the terminals was recommended, but there is no recorded post-restart result or explicit confirmation of complete resolution.

## Symptoms and exact error messages

The supplied report transcribes the startup error below. The personal username is replaced with `<username>`:

```text
fnm : The term 'fnm' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of
the name, or if a path was included, verify that the path is correct and try again.
At C:\Users\<username>\OneDrive\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1:1 char:1
+ fnm env --use-on-cd | Out-String | Invoke-Expression
+ ~~~
    + CategoryInfo          : ObjectNotFound: (fnm:String) [], CommandNotFoundException
    + FullyQualifiedErrorId : CommandNotFoundException
```

The location identifies profile initialization, rather than a manually entered Node.js command, as the source of this error. The supplied screenshot description reports that the prompt remained available afterward.

After installation, an interactive version check produced the same command-discovery failure at a different location:

```text
fnm : The term 'fnm' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of
the name, or if a path was included, verify that the path is correct and try again.
At line:1 char:1
+ fnm --version
+ ~~~
    + CategoryInfo          : ObjectNotFound: (fnm:String) [], CommandNotFoundException
    + FullyQualifiedErrorId : CommandNotFoundException
```

Neither error proves that the executable was absent from every location on disk.

## Investigation and diagnosis

The following sequence comes from the supplied troubleshooting evidence. Commands labeled executed were reported as run by the owner; recommendations are separate.

### 1. Inspect the startup failure

The reported screenshot identified the failed profile command at line 1, character 1. The immediate diagnostic question was whether PowerShell could find `fnm` and whether `winget` recognized an installation.

### 2. Check command discovery — executed

```powershell
Get-Command fnm -ErrorAction SilentlyContinue
```

**Observed result:** no output. The active session did not resolve `fnm`; the command suppressed the associated error. This was a diagnostic check, not a repair.

### 3. Check the package inventory — executed

```powershell
winget list --id Schniz.fnm
```

**Observed output:**

```text
No installed package found matching input criteria.
```

This established that the query found no matching installed package. It did not exclude a manually installed executable or another installation method.

### 4. Install Fast Node Manager — executed

```powershell
winget install --id Schniz.fnm -e
```

The recorded installation selected version `1.39.0`, verified the installer hash, extracted the archive, and completed installation. Relevant output:

```text
Found Fast Node Manager [Schniz.fnm] Version 1.39.0
Successfully verified installer hash
Successfully extracted archive
Command line alias added: "fnm"
Path environment variable modified; restart your shell to use the new value.
Successfully installed
```

This excerpt omits download progress and other informational lines. It records installer-reported success, not successful execution of `fnm` afterward.

### 5. Test fnm immediately afterward — executed, failed

```powershell
fnm --version
```

The immediate post-installation test still returned `CommandNotFoundException`. This test occurred before any documented terminal restart. The installer indicated that restarting the shell was necessary to load the updated PATH, making a stale process environment a plausible explanation. However, the root cause of the continued command-resolution failure was not conclusively verified.

### 6. Check Node.js and npm — executed, succeeded

```powershell
node --version
```

```text
v22.23.3
```

```powershell
npm --version
```

```text
10.9.9
```

Both commands remained accessible. These version checks establish availability at that time, not the provenance of either installation or successful fnm integration.

### 7. Restart and retest — recommended, not recorded as performed

The assistant recommended closing all PowerShell and Windows Terminal windows, opening a new terminal process, and repeating the version checks. If `fnm` still could not be found, `where.exe fnm` was suggested as a follow-up diagnostic. No results from these recommendations were supplied.

## What did not work

| Action actually performed | Result | Interpretation |
| --- | --- | --- |
| `Get-Command fnm -ErrorAction SilentlyContinue` before installation | No result | Failed command discovery; diagnostic only |
| `winget list --id Schniz.fnm` before installation | No matching installed package | Inventory finding; diagnostic only |
| `fnm --version` immediately after installation | `CommandNotFoundException` | Failed verification in the existing session; continued failure's cause unproven |

There is no recorded unsuccessful terminal-restart test. Profile edits, manual PATH changes, and Node.js reinstallation must not be counted as performed or failed repairs.

## Confirmed remediation and recommended next steps

### Confirmed remediation: package installation

The corrective action actually completed was:

```powershell
winget install --id Schniz.fnm -e
```

`winget` reported successful installation of fnm `1.39.0` and a PATH update. This command is not version-pinned; running it at another time may install a different version. The recorded installation did not establish that the startup problem was fixed.

### Recommended completion procedure: unverified in this case

The original guidance was to:

1. Close all PowerShell and Windows Terminal windows, then open a new terminal process. Save work and allow running jobs to finish before closing their sessions.
2. Repeat the version checks:

   ```powershell
   fnm --version
   node --version
   npm --version
   ```

3. If `fnm` remains unavailable, check command discovery:

   ```powershell
   where.exe fnm
   ```

The supplied report also lists `Get-Command fnm` as a proposed post-restart check. No post-restart command output was recorded. Starting a new tab was not demonstrated to refresh the relevant environment; the recommendation was to reopen the terminal process. Neither approach is a verified fix for this case.

### Optional profile guard: suggested, not tested

The supplied report describes an earlier suggestion to open the profile with:

```powershell
notepad $PROFILE
```

and replace only the unconditional fnm initialization with:

```powershell
if (Get-Command fnm -ErrorAction SilentlyContinue) {
    fnm env --use-on-cd --shell powershell | Out-String | Invoke-Expression
}
```

This checks command availability before attempting initialization. If the command is unavailable, initialization is skipped. The guard does not install fnm, repair PATH, or establish that Node.js version switching works. There is no evidence the owner applied this edit. Preserve unrelated profile content and review a backup before making changes.

## Verification

| Claim | Evidence and status |
| --- | --- |
| fnm 1.39.0 package installation completed | Confirmed by supplied installer output |
| Installer reported an alias and PATH update | Confirmed by supplied installer output; effective PATH not captured |
| fnm worked immediately after installation | No — the recorded check failed |
| Node.js and npm were accessible | Confirmed by their supplied version outputs |
| All terminal processes were restarted | Not recorded |
| fnm worked after restart | Not verified |
| A new PowerShell session started without the profile error | Not verified |
| Automatic Node.js version switching worked | Not verified |
| Owner explicitly confirmed complete resolution | No confirmation supplied |

No original Turkish resolution confirmation is present in the supplied evidence, so no quotation or translation of one can be provided. The record ends with a recommendation to restart and retest, not with evidence of recovery.

## Technical explanation

**Confirmed finding:** the profile attempted to execute a command that the active PowerShell session could not resolve. This accounts for the startup `CommandNotFoundException`, but does not establish why command discovery failed originally. The absent package-inventory result supports investigating installation; it does not prove there was no fnm executable anywhere on disk.

**Plausible explanation for continued failure:** the existing process may have retained an environment without the newly added PATH entry. Microsoft documents that a PowerShell process inherits environment variables from its parent. This behavior, together with the installer's restart instruction, supports the stale-environment hypothesis. No before/after PATH comparison or successful post-restart test establishes it as the cause in this case. See [Microsoft's environment-variable documentation](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_environment_variables?view=powershell-5.1).

**Node.js and npm:** their successful version checks show they were independently discoverable while `fnm` was not. Their installation sources and relationship to any previous version manager were not investigated.

**Profile integration:** fnm's [official shell setup documentation](https://github.com/Schniz/fnm#shell-setup) describes configuring the shell with `fnm env`. It provides context for the initialization line, not evidence that the proposed guard or restart was applied here.

## Screenshot evidence

The owner-supplied report describes an original screenshot showing:

- Windows PowerShell 5.1 in a Windows Terminal-style tabbed interface.
- An Administrator title and a red startup error.
- The profile initialization line, its line/character location, and `CommandNotFoundException`.
- A usable prompt after the error.

These are reported screenshot details, not a new independent inspection of the image. The terminal application's exact identity and version were not independently established. No screenshot is published with this article, and no post-restart success image was supplied.

The redacted profile path indicates a OneDrive-managed Documents location. It does not establish that OneDrive caused the failure.

## Safety and limitations

- Preserve the working Node.js and npm installations. No uninstall or replacement was shown to be necessary.
- Do not delete the whole PowerShell profile or indiscriminately rewrite PATH. The report records a failing initialization command, not a corrupt profile.
- Before any optional profile edit, preserve unrelated settings and consider whether OneDrive synchronizes the file to machines with different tools installed.
- `Invoke-Expression` evaluates the preceding output as PowerShell code. Only use this initialization with a trusted fnm executable; a command-availability guard is not an integrity check.
- The reported Administrator title does not establish that elevation was required for the repair.
- Save work before closing terminals; closing a session may interrupt running jobs.
- The exact OS build, architecture, installation directory, and effective PATH were not recorded. Results may differ across environments.
- Successful installation alone is insufficient to classify the startup issue as resolved. No new system tests were performed while preparing this article.

## Missing information

- Original troubleshooting date and any resolution date.
- Exact Windows build, hardware architecture, terminal version, and winget version.
- Previous fnm installation/removal history and the origin of the profile line.
- Original and post-installation PATH values, actual fnm installation directory, and `where.exe fnm` output.
- Confirmation that all terminal processes were restarted.
- Successful post-restart `fnm --version` output and a startup session without the original error.
- Whether the profile was edited and whether automatic Node.js version switching worked.
- An explicit owner confirmation of complete resolution.
- Original screenshot and complete transcript for independent source review; this article relies on the supplied report and verified-evidence list.

## References

- **Case evidence:** the owner's supplied technical case report and subsequent verified-evidence list. Recorded version outputs are reproduced as supplied, not independently reproduced tests.
- [Microsoft Learn: about_Environment_Variables (Windows PowerShell 5.1)](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_environment_variables?view=powershell-5.1) — process environment inheritance.
- [Fast Node Manager: shell setup](https://github.com/Schniz/fnm#shell-setup) — official shell initialization guidance.

The external references were checked for technical context. Neither verifies the owner's outcome or establishes the cause of the continued failure.

## Final case assessment

**Partially Resolved — Installation Confirmed, Final Fix Unverified.**

The owner installed fnm 1.39.0 successfully according to winget, but the immediate command check still failed before any documented terminal restart. A stale process environment is plausible, not proven. Node.js and npm remained accessible. The next recommended action was to restart the terminals and retest; the record contains neither those results nor confirmation that the startup error disappeared.
