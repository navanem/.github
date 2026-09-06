# Navanem GitHub Organization Refresh Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Make the `navanem` GitHub organization a coherent extension of the Navanem open-source software studio by publishing a branded profile, normalizing seven public repositories, and verifying all public routes without changing `gta6-leonida-atlas`.

**Architecture:** A special public `.github` repository owns the organization README and its local SVG asset. GitHub organization and repository settings own display metadata, pins, names, website links, and topics; focused repository pull requests update only active references that would otherwise retain old canonical URLs. Every state-changing step is bracketed by a public API or rendered-page assertion.

**Tech Stack:** GitHub organization/repository settings, Git, Markdown, SVG, PowerShell, GitHub REST API, existing repository-specific validation commands

**Spec:** `docs/superpowers/specs/2026-09-06-navanem-github-organization-design.md`

## Global Constraints

- Do not change any file, setting, metadata field, topic, link, branch, release, issue, or pull request in `gta6-leonida-atlas`.
- Do not rename the `navanem` organization handle.
- Do not activate organization Discussions.
- Do not change product behavior; source changes are limited to active names, clone commands, package metadata, and canonical links.
- Use the exact repository names, descriptions, website URLs, topics, pins, and profile copy defined in the approved specification.
- Preserve public visibility, default branches, fork relationships, releases, issues, pull requests, Actions, and security settings.
- Never reuse an old repository name after a rename because that would replace GitHub's redirect.
- Use focused branches and pull requests for repository-content changes; merge only after required checks pass.

---

### Task 1: Capture preflight state and prove rename targets are free

**Files:**
- Create: `docs/audits/2026-09-06-before.json`

**Interfaces:**
- Consumes: Public GitHub REST endpoints for the organization and eight relevant repositories.
- Produces: A committed rollback record and a seven-name availability result used by all rename tasks.

- [ ] **Step 1: Capture the organization and relevant repository state**

Run this from the organization-profile working repository:

```powershell
$headers = @{ 'User-Agent' = 'Codex-Navanem-Refresh'; 'Accept' = 'application/vnd.github+json' }
$names = @(
  'gta6-leonida-atlas',
  'navanem_OpsCenter',
  'navanem_RoboCopyGUI',
  'navanem_SysInfoTool',
  'navanem_payload_contact',
  'navanem_payload_comments',
  'navanem_SuperDelete',
  'powershell_scripts'
)
$snapshot = [ordered]@{
  capturedAt = (Get-Date).ToUniversalTime().ToString('o')
  organization = Invoke-RestMethod 'https://api.github.com/orgs/navanem' -Headers $headers
  repositories = @($names | ForEach-Object {
    Invoke-RestMethod "https://api.github.com/repos/navanem/$_" -Headers $headers
  })
}
$snapshot | ConvertTo-Json -Depth 20 | Set-Content -Encoding utf8 'docs/audits/2026-09-06-before.json'
```

Expected: the JSON contains all eight repositories, including the untouched GTA repository baseline.

- [ ] **Step 2: Verify every target name returns 404**

```powershell
$targets = @('opscenter','robocopygui','sysinfo-tool','payload-contact','payload-comments','superdelete','powershell-scripts')
$headers = @{ 'User-Agent' = 'Codex-Navanem-Refresh'; 'Accept' = 'application/vnd.github+json' }
foreach ($target in $targets) {
  try {
    Invoke-WebRequest "https://api.github.com/repos/navanem/$target" -Headers $headers -ErrorAction Stop | Out-Null
    throw "Target already exists: $target"
  } catch {
    if ($_.Exception.Response.StatusCode.value__ -ne 404) { throw }
    Write-Output "AVAILABLE=$target"
  }
}
```

Expected: seven `AVAILABLE=` lines and no exception.

- [ ] **Step 3: Validate and commit the snapshot**

```powershell
Get-Content -Raw docs/audits/2026-09-06-before.json | ConvertFrom-Json | Out-Null
git diff --check
git add docs/audits/2026-09-06-before.json
git commit -m "docs: capture organization metadata baseline"
```

Expected: JSON parses, `git diff --check` is silent, and the commit succeeds.

### Task 2: Publish the organization profile repository and review branch

**Files:**
- Create: `README.md`
- Create: `profile/README.md`
- Create: `profile/assets/navanem-header.svg`
- Existing: `docs/superpowers/specs/2026-09-06-navanem-github-organization-design.md`
- Existing: `docs/superpowers/plans/2026-09-06-navanem-github-organization-refresh.md`

**Interfaces:**
- Consumes: Approved Navanem brand copy and public project URLs.
- Produces: Public `navanem/.github` repository plus a `feat/organization-profile` pull request ready for final merge.

- [ ] **Step 1: Create the empty public `.github` repository**

In GitHub, create `navanem/.github` with public visibility and no generated README, `.gitignore`, or license. Do not create `.github-private`.

Expected: `https://github.com/navanem/.github` exists publicly and has no default content.

- [ ] **Step 2: Connect and publish the documentation baseline**

```powershell
git remote add origin https://github.com/navanem/.github.git
git push -u origin main
git switch -c feat/organization-profile
```

Expected: `origin/main` contains the approved spec, plan, and preflight snapshot; the working branch is `feat/organization-profile`.

- [ ] **Step 3: Add the repository-purpose README**

Create `README.md` with exactly:

```markdown
# Navanem organization profile

This public repository contains the profile displayed on the
[Navanem GitHub organization](https://github.com/navanem).

- Profile content: [`profile/README.md`](profile/README.md)
- Website: [www.navanem.com](https://www.navanem.com/)
- Projects: [www.navanem.com/projects](https://www.navanem.com/projects)
```

- [ ] **Step 4: Add the brand header asset**

Create `profile/assets/navanem-header.svg` with exactly:

```svg
<svg xmlns="http://www.w3.org/2000/svg" width="1280" height="360" viewBox="0 0 1280 360" role="img" aria-labelledby="title desc">
  <title id="title">Navanem</title>
  <desc id="desc">A home for open-source applications</desc>
  <defs>
    <linearGradient id="bg" x1="0" y1="0" x2="1" y2="1">
      <stop offset="0" stop-color="#00101f"/>
      <stop offset="1" stop-color="#031a31"/>
    </linearGradient>
    <linearGradient id="mark" x1="0" y1="0" x2="1" y2="1">
      <stop offset="0" stop-color="#20c7ff"/>
      <stop offset="1" stop-color="#1557ff"/>
    </linearGradient>
  </defs>
  <rect width="1280" height="360" rx="24" fill="url(#bg)"/>
  <path d="M95 98 129 78v79l-34 20zm44-26 34-20v79l-34 20zm44 26 34-20v79l-34 20z" fill="url(#mark)"/>
  <text x="252" y="148" fill="#f5f9ff" font-family="Arial, Helvetica, sans-serif" font-size="62" font-weight="700">Navanem</text>
  <text x="95" y="246" fill="#f5f9ff" font-family="Arial, Helvetica, sans-serif" font-size="46" font-weight="400">A home for open-source applications.</text>
  <text x="95" y="296" fill="#8ca8c4" font-family="ui-monospace, SFMono-Regular, Consolas, monospace" font-size="22">IDEAS INTO OPEN SOFTWARE · GENEVA, SWITZERLAND</text>
</svg>
```

- [ ] **Step 5: Add the organization profile README**

Create `profile/README.md` with exactly:

```markdown
<div align="center">
  <a href="https://www.navanem.com/">
    <img src="./assets/navanem-header.svg" width="100%" alt="Navanem — A home for open-source applications" />
  </a>
</div>

Navanem is an independent software studio. We build, maintain and share practical open-source applications that solve real problems—software you can inspect, use, learn from and improve.

<p align="center">
  <a href="https://www.navanem.com/"><strong>Website</strong></a> ·
  <a href="https://www.navanem.com/projects"><strong>Explore projects</strong></a> ·
  <a href="https://github.com/orgs/navanem/repositories?q=visibility%3Apublic"><strong>Repositories</strong></a> ·
  <a href="https://www.navanem.com/contact"><strong>Contact</strong></a>
</p>

## Open source, always

| Open source | A practical mix | Easy to use | Built in public |
| --- | --- | --- | --- |
| Code you can read, use and improve. | Desktop apps, plugins, scripts and platforms. | Straightforward docs and sensible defaults. | Feedback, issues and pull requests shape the work. |

## Featured work

| Project | What it does | Stack |
| --- | --- | --- |
| [**GTA VI Leonida Atlas**](https://github.com/navanem/gta6-leonida-atlas) | A community-driven interactive atlas for exploring Leonida in GTA VI. | TypeScript · AGPL-3.0 |
| [**RoboCopyGUI**](https://github.com/navanem/robocopygui) | A friendly Windows interface for reliable copy, mirror, move and sync operations. | C# · Windows |
| [**OpsCenter**](https://github.com/navanem/opscenter) | Open-source operations software for MSP tickets, visits, assets and client work. | TypeScript · MSP/ITSM |
| [**Payload Contact**](https://github.com/navanem/payload-contact) | Contact forms, spam protection and an admin inbox for Payload CMS 3. | TypeScript · Payload CMS |
| [**SysInfo Tool**](https://github.com/navanem/sysinfo-tool) | A BGInfo-style Windows system-information wallpaper utility. | C# · .NET |
| [**PowerShell Scripts**](https://github.com/navanem/powershell-scripts) | Practical automation, troubleshooting and hardening for administrators. | PowerShell |

## More from the portfolio

- [**Payload Comments**](https://github.com/navanem/payload-comments) — comments, reactions, moderation and threaded replies for Payload CMS 3.
- [**SuperDelete**](https://github.com/navanem/superdelete) — a .NET command-line utility for deleting paths beyond the traditional Windows limit.

Browse the complete portfolio at [navanem.com/projects](https://www.navanem.com/projects).

## Build with us

Try a project, report a reproducible issue or open a focused pull request when you spot something worth improving. Each repository documents its own setup, contribution and security process.

Please report suspected vulnerabilities through the security instructions of the affected repository rather than a public issue.

<p align="center">
  <strong>Open source builds brighter solutions.</strong><br />
  <a href="https://www.navanem.com/about">About Navanem</a> ·
  <a href="https://www.navanem.com/contact">Contact</a> ·
  <a href="https://github.com/navanem">GitHub</a>
</p>
```

- [ ] **Step 6: Validate, commit, push, and open the profile PR**

```powershell
$links = Select-String -Path README.md,profile/README.md -Pattern 'https://[^)" ]+' -AllMatches | ForEach-Object { $_.Matches.Value.TrimEnd('>') } | Sort-Object -Unique
foreach ($link in $links) { Write-Output "LINK=$link" }
[xml](Get-Content -Raw profile/assets/navanem-header.svg) | Out-Null
git diff --check
git add README.md profile/README.md profile/assets/navanem-header.svg
git commit -m "feat: add Navanem organization profile"
git push -u origin feat/organization-profile
```

Open a pull request titled `feat: add Navanem organization profile`. Leave it open until all repository renames are complete so every profile link resolves before merge.

Expected: XML parsing and `git diff --check` pass; the PR preview renders the header, tables, and links without clipping.

### Task 3: Rename and normalize `opscenter`

**Files:**
- No repository-content files change; the read-only audit found no active old-name reference.

**Interfaces:**
- Consumes: `navanem/navanem_OpsCenter` repository settings.
- Produces: `navanem/opscenter` with canonical public metadata.

- [ ] **Step 1: Rename the repository**

In repository Settings → General, change `navanem_OpsCenter` to `opscenter` and confirm.

- [ ] **Step 2: Set exact metadata**

Set description to `An open-source MSP operations platform for tickets, field visits, client assets, subscriptions, time tracking, and client portals.`

Set website to `https://www.navanem.com/projects/navanem-opscenter`.

Replace topics with: `open-source`, `navanem`, `typescript`, `msp`, `itsm`, `ticketing-system`, `asset-management`, `field-service`.

- [ ] **Step 3: Verify canonical and redirected routes**

```powershell
Add-Type -AssemblyName System.Net.Http
function Get-NoRedirectStatus([string]$Uri) {
  $handler = [System.Net.Http.HttpClientHandler]::new()
  $handler.AllowAutoRedirect = $false
  $client = [System.Net.Http.HttpClient]::new($handler)
  try { return [int]($client.GetAsync($Uri).GetAwaiter().GetResult().StatusCode) }
  finally { $client.Dispose(); $handler.Dispose() }
}
if ((Get-NoRedirectStatus 'https://github.com/navanem/opscenter') -ne 200) { throw 'Canonical OpsCenter URL failed' }
$oldStatus = Get-NoRedirectStatus 'https://github.com/navanem/navanem_OpsCenter'
if ($oldStatus -notin 301,302) { throw "Old OpsCenter URL did not redirect: $oldStatus" }
```

Verify default branch, visibility, issues, releases, Actions, and security tabs match the baseline.

### Task 4: Rename and normalize Windows repositories

**Files:**
- Modify in `robocopygui`: `README.md:96-97`, `README.md:143`
- Modify in `sysinfo-tool`: `README.md:72`, `README.md:181`, `CHANGELOG.md:63-66`
- Modify in `superdelete`: `README.md:41`

**Interfaces:**
- Consumes: Three existing public Windows repositories and their baseline metadata.
- Produces: Canonical repositories, metadata, and active links for RoboCopyGUI, SysInfo Tool, and SuperDelete.

- [ ] **Step 0: Read repository-local instructions**

Before editing, recursively locate and read every `AGENTS.md` that applies to the files listed in this task. Stop and revise the task if repository-local instructions conflict with this plan.

- [ ] **Step 1: Rename and configure RoboCopyGUI**

Rename `navanem_RoboCopyGUI` to `robocopygui`.

Set description to `A friendly Windows desktop interface for reliable copy, mirror, move, and sync operations powered by Robocopy.`

Set website to `https://www.navanem.com/projects/navanem-robocopygui`.

Set topics to `open-source`, `navanem`, `windows`, `robocopy`, `file-sync`, `file-copy`, `desktop-app`, `csharp`.

In a focused branch, change only:

```diff
-git clone https://github.com/navanem/navanem_RoboCopyGUI.git
-cd navanem_RoboCopyGUI
+git clone https://github.com/navanem/robocopygui.git
+cd robocopygui
```

```diff
-navanem_RoboCopyGUI/
+robocopygui/
```

Run `dotnet build RoboSync.sln --configuration Release`, `git diff --check`, and `git grep -n -I 'navanem_RoboCopyGUI' -- README.md`; expect build success and zero old-name matches. Commit `docs: update repository references`, push, open a PR, wait for checks, and squash-merge.

- [ ] **Step 2: Rename and configure SysInfo Tool**

Rename `navanem_SysInfoTool` to `sysinfo-tool`.

Set description to `A lightweight BGInfo-style Windows utility that generates a polished system information panel and applies it as the desktop wallpaper.`

Set website to `https://www.navanem.com/projects/navanem-sysinfotool`.

Set topics to `open-source`, `navanem`, `windows`, `bginfo`, `system-information`, `desktop-wallpaper`, `dotnet`, `csharp`.

Replace `https://github.com/navanem/navanem_SysInfoTool` with `https://github.com/navanem/sysinfo-tool` in the two active README release links and four CHANGELOG comparison links. Run the repository's solution build, `git diff --check`, and `git grep -n -I 'github.com/navanem/navanem_SysInfoTool' -- README.md CHANGELOG.md`; expect success and zero matches. Commit, push, open a PR, wait for checks, and squash-merge.

- [ ] **Step 3: Rename and configure SuperDelete without changing its fork relationship**

Rename `navanem_SuperDelete` to `superdelete`.

Set description to `A .NET command-line utility for deleting Windows files and directories whose paths exceed the traditional 260-character limit.`

Set website to `https://www.navanem.com/projects/navanem-superdelete`.

Set topics to `open-source`, `navanem`, `windows`, `long-paths`, `file-management`, `command-line`, `dotnet`, `csharp`.

Replace the README release URL with `https://github.com/navanem/superdelete/releases`. Run the repository build, `git diff --check`, and a zero-match grep for the old URL. Commit, push, open a PR against the existing `master` default branch, wait for checks, and squash-merge. Verify GitHub still labels the repository as a fork.

- [ ] **Step 4: Verify old URLs redirect**

Check all three old GitHub URLs with redirects disabled and expect HTTP 301 or 302. Check the three new repository pages and three Navanem project URLs and expect HTTP 200.

### Task 5: Rename and normalize Payload CMS plugins

**Files:**
- Modify in `payload-contact`: `package.json:41-47`, `src/index.ts:13`
- Modify in `payload-comments`: `package.json:7-14`, `README.md:54`, `CHANGELOG.md:72-76`
- Do not modify historical files under `docs/planning/`.

**Interfaces:**
- Consumes: Existing package metadata and public plugin repositories.
- Produces: Canonical GitHub URLs while preserving the published package names `@navanem/payload-contact` and `@navanem/payload-comments`.

- [ ] **Step 0: Read repository-local instructions**

Before editing, recursively locate and read every `AGENTS.md` that applies to `package.json`, `src/index.ts`, `README.md`, and `CHANGELOG.md`. Stop and revise the task if repository-local instructions conflict with this plan.

- [ ] **Step 1: Rename and configure Payload Contact**

Rename `navanem_payload_contact` to `payload-contact`.

Set description to `A Payload CMS 3 contact-form plugin with validation, spam protection, an admin inbox, and runtime configuration.`

Set website to `https://www.navanem.com/projects/navanem-payload-contact`.

Set topics to `open-source`, `navanem`, `typescript`, `payloadcms`, `payloadcms-v3`, `payload-plugin`, `contact-form`, `spam-protection`.

In `package.json`, set repository URL to `git+https://github.com/navanem/payload-contact.git`, homepage to `https://github.com/navanem/payload-contact#readme`, and bugs URL to `https://github.com/navanem/payload-contact/issues`. Change the source comment label from `navanem_payload_contact` to `payload-contact`; do not change the npm package name.

Run `pnpm install --frozen-lockfile`, `pnpm test`, `pnpm build`, `Get-Content -Raw package.json | ConvertFrom-Json`, `git diff --check`, and a zero-match grep excluding `docs/planning`. Commit, push, open a PR, wait for checks, and squash-merge.

- [ ] **Step 2: Rename and configure Payload Comments**

Rename `navanem_payload_comments` to `payload-comments`.

Set description to `A Payload CMS 3 plugin for anonymous comments, reactions, optional moderation, and threaded replies.`

Set website to `https://www.navanem.com/projects/navanem-payload-comments`.

Set topics to `open-source`, `navanem`, `typescript`, `payloadcms`, `payloadcms-v3`, `payload-plugin`, `comments`, `moderation`.

In `package.json`, set repository URL to `git+https://github.com/navanem/payload-comments.git`, homepage to `https://github.com/navanem/payload-comments#readme`, and bugs URL to `https://github.com/navanem/payload-comments/issues`. In `README.md`, replace the Git dependency with `github:navanem/payload-comments`. Replace the five active CHANGELOG release URLs with the new repository URL. Do not modify historical planning files or the npm package name.

Run `pnpm install --frozen-lockfile`, `pnpm test`, `pnpm build`, JSON parsing, `git diff --check`, and a zero-match grep across `package.json`, `README.md`, and `CHANGELOG.md`. Commit, push, open a PR, wait for checks, and squash-merge.

- [ ] **Step 3: Verify package and redirect continuity**

Verify both old GitHub URLs redirect, both new repository pages resolve, package names are unchanged, and the two Navanem project pages return HTTP 200.

### Task 6: Rename and normalize PowerShell Scripts

**Files:**
- Modify: `README.md:1`, `README.md:35-36`

**Interfaces:**
- Consumes: Existing `powershell_scripts` public repository.
- Produces: Canonical `powershell-scripts` repository and user-facing clone instructions.

- [ ] **Step 0: Read repository-local instructions**

Before editing `README.md`, recursively locate and read every applicable `AGENTS.md`. Stop and revise the task if repository-local instructions conflict with this plan.

- [ ] **Step 1: Rename and configure the repository**

Rename `powershell_scripts` to `powershell-scripts`.

Set description to `Practical PowerShell scripts for everyday system administration, automation, troubleshooting, and hardening.`

Set website to `https://www.navanem.com/projects`.

Set topics to `open-source`, `navanem`, `powershell`, `sysadmin`, `automation`, `windows`, `security`, `troubleshooting`.

- [ ] **Step 2: Update active README naming**

Change the title to `# PowerShell Scripts`, the clone example to `https://github.com/<your-account>/powershell-scripts.git`, and the directory command to `cd powershell-scripts`.

Run `git diff --check` and `git grep -n -I 'powershell_scripts' -- README.md`; expect zero matches. Run any repository-provided PowerShell test command if present; otherwise parse every tracked `.ps1` file with the PowerShell parser and require zero syntax errors:

```powershell
$errors = @()
git ls-files '*.ps1' | ForEach-Object {
  $tokens = $null
  $parseErrors = $null
  [System.Management.Automation.Language.Parser]::ParseFile((Resolve-Path $_), [ref]$tokens, [ref]$parseErrors) | Out-Null
  $errors += $parseErrors
}
if ($errors.Count -gt 0) { $errors; exit 1 }
```

Commit, push, open a PR, wait for checks, and squash-merge.

- [ ] **Step 3: Verify redirect and public page**

Expect a redirect from `/navanem/powershell_scripts` and HTTP 200 for `/navanem/powershell-scripts` and the configured Navanem Projects page.

### Task 7: Finalize organization identity, profile, and pins

**Files:**
- Existing PR content: `profile/README.md`, `profile/assets/navanem-header.svg`, `README.md`

**Interfaces:**
- Consumes: All canonical repository URLs from Tasks 3–6.
- Produces: The final public organization page.

- [ ] **Step 1: Revalidate every profile link after renames**

Open the profile PR's rendered preview. Verify every repository link resolves directly to its canonical URL with no 404 and that the Navanem site links target the intended pages.

- [ ] **Step 2: Merge the `.github` profile PR**

Wait for required checks, use squash merge, confirm the PR is closed as merged, and delete the feature branch if GitHub offers the normal branch-delete action.

- [ ] **Step 3: Update organization profile metadata**

In organization Settings → General, set display name to `Navanem` and description to `Independent software studio building practical open-source applications and tools.` Keep URL, social accounts, location, email, avatar, and all other settings unchanged.

- [ ] **Step 4: Configure the six pins in this order**

Pin `gta6-leonida-atlas`, `robocopygui`, `opscenter`, `payload-contact`, `sysinfo-tool`, and `powershell-scripts`. This changes only the organization display; do not enter GTA repository settings.

- [ ] **Step 5: Verify public-view rendering**

Use GitHub's public-view control and confirm the hero, principles, project table, portfolio, contribution text, and six pins render correctly. Confirm Discussions remains disabled.

### Task 8: Record final state and run the complete verification matrix

**Files:**
- Create: `docs/audits/2026-09-06-after.json`

**Interfaces:**
- Consumes: The completed public organization and repository state.
- Produces: Auditable evidence that the refresh met the spec and preserved GTA.

- [ ] **Step 1: Capture the final organization and repository state**

Repeat the Task 1 REST snapshot with these names: `gta6-leonida-atlas`, `opscenter`, `robocopygui`, `sysinfo-tool`, `payload-contact`, `payload-comments`, `superdelete`, and `powershell-scripts`. Save it as `docs/audits/2026-09-06-after.json`.

- [ ] **Step 2: Compare the GTA baseline**

Compare the before/after GTA entries for `id`, `name`, `full_name`, `description`, `homepage`, `topics`, `visibility`, `default_branch`, `fork`, `archived`, `has_issues`, `has_projects`, `has_wiki`, and `license.spdx_id`. Require equality for every field.

- [ ] **Step 3: Verify all canonical metadata**

Assert the seven final API entries match the exact names, descriptions, homepages, and topics in the specification. Assert every repository remains public, `superdelete` remains a fork, and default branches match the baseline.

- [ ] **Step 4: Verify redirects and public links**

Request all seven old repository URLs with redirects disabled and require 301 or 302. Request all eight current repository pages, the organization page, and every configured Navanem homepage URL and require HTTP 200.

- [ ] **Step 5: Commit the final audit record**

```powershell
Get-Content -Raw docs/audits/2026-09-06-after.json | ConvertFrom-Json | Out-Null
git diff --check
git switch main
git pull --ff-only origin main
git switch -c docs/record-refresh-verification
git add docs/audits/2026-09-06-after.json
git commit -m "docs: record organization refresh verification"
git push -u origin docs/record-refresh-verification
```

Open a focused pull request, wait for required checks, and squash-merge it. Then run:

```powershell
git switch main
git pull --ff-only origin main
git fetch --prune
git status --short --branch
```

Expected: JSON parses, the diff check is silent, the PR merges, and local `main` is clean and synchronized with `origin/main`.

- [ ] **Step 6: Leave the deliverable open**

Open `https://github.com/navanem`, switch to the public view, visually inspect the first screen and profile sections, and mark that organization page as the final deliverable.
