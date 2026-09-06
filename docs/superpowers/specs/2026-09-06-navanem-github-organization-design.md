# Navanem GitHub Organization Profile and Repository Metadata Design

**Date:** 2026-09-06  
**Status:** Approved in conversation; awaiting written-spec review  
**Scope owner:** `navanem` GitHub organization

## Context

Navanem's current website presents the organization as an independent software studio and a home for practical open-source applications. Its GitHub organization page still carries an older security-news description, has no organization profile README, pins only one repository, and uses inconsistent repository names and metadata.

The website at `https://www.navanem.com/` is the source of truth for the public identity, product names, visual direction, and project landing pages.

## Goals

- Make the GitHub organization page feel like a direct extension of the Navanem website.
- Present Navanem as an independent studio building useful open-source applications, plugins, scripts, and platforms.
- Create a polished English organization profile README with clear paths to explore and contribute.
- Normalize the names, descriptions, website links, and topics of every public repository except `gta6-leonida-atlas`.
- Preserve discoverability and compatibility when repositories are renamed.
- Keep the organization easy to maintain without adding a new community surface.

## Non-goals

- Do not change any file, setting, metadata field, topic, link, branch, release, issue, or pull request in `gta6-leonida-atlas`.
- Do not change application behavior or product code as part of the metadata cleanup.
- Do not activate organization Discussions.
- Do not rename the `navanem` organization handle.
- Do not redesign `www.navanem.com`.

## Brand and organization metadata

The organization display name will be **Navanem** instead of `navanem.com`.

The organization description will be:

> Independent software studio building practical open-source applications and tools.

The primary website remains `https://www.navanem.com`. The existing social accounts and Switzerland location remain unchanged.

The profile language will be English, matching the current website. The visual direction will use the site's dark navy, cyan, electric-blue, white, and muted-blue palette. Copy will be concise, practical, and contribution-friendly.

## Organization profile repository

A public special repository named `.github` will hold the organization profile at `profile/README.md`. It will also contain locally versioned visual assets so the profile does not depend on third-party image hosting or tracking pixels.

The profile will contain these sections:

1. **Hero** — Navanem wordmark, “A home for open-source applications,” a short mission statement, and links to the website and public repositories.
2. **Principles** — Open source, practical mix, easy to use, and built in public, mirroring the website.
3. **Featured work** — Six concise project cards with product name, purpose, primary technology, and a direct repository link.
4. **Full portfolio** — A compact list of the remaining public projects so no maintained public repository is hidden.
5. **Contribute** — Invitations to try projects, open focused issues, and submit pull requests according to each repository's contribution guidance.
6. **Footer** — Links to Navanem Projects, About, Contact, and GitHub repositories, ending with “Open source, always.”

The profile will avoid activity counters, generated statistics cards, excessive badges, animated content, and remote analytics. Images will have useful alternative text, links will use descriptive labels, and the layout will remain readable in GitHub light and dark themes.

## Featured and pinned repositories

GitHub allows up to six pinned repositories. The public organization page will pin:

1. `gta6-leonida-atlas` — kept as the existing latest project, with no repository changes.
2. `robocopygui`
3. `opscenter`
4. `payload-contact`
5. `sysinfo-tool`
6. `powershell-scripts`

`payload-comments` and `superdelete` will remain visible in the full portfolio. `superdelete` will continue to be identified by GitHub as a fork.

## Repository renames

The redundant `navanem_` prefix and underscore naming will be removed. Product names displayed on the Navanem website are authoritative.

| Current repository | Target repository |
| --- | --- |
| `navanem_OpsCenter` | `opscenter` |
| `navanem_RoboCopyGUI` | `robocopygui` |
| `navanem_SysInfoTool` | `sysinfo-tool` |
| `navanem_payload_contact` | `payload-contact` |
| `navanem_payload_comments` | `payload-comments` |
| `navanem_SuperDelete` | `superdelete` |
| `powershell_scripts` | `powershell-scripts` |

Before each rename, the target name must be confirmed as available in the organization. Existing default branches, visibility, fork relationships, releases, issues, pull requests, Actions, and security settings must be recorded and rechecked afterward.

GitHub's redirect from the old repository URL will be verified after every rename. The old repository names must not be reused because doing so would replace those redirects.

## Descriptions, website links, and topics

Descriptions will name the product directly and stay short enough to scan in the organization repository list.

### `opscenter`

- **Description:** An open-source MSP operations platform for tickets, field visits, client assets, subscriptions, time tracking, and client portals.
- **Website:** `https://www.navanem.com/projects/navanem-opscenter`
- **Topics:** `open-source`, `navanem`, `typescript`, `msp`, `itsm`, `ticketing-system`, `asset-management`, `field-service`

### `robocopygui`

- **Description:** A friendly Windows desktop interface for reliable copy, mirror, move, and sync operations powered by Robocopy.
- **Website:** `https://www.navanem.com/projects/navanem-robocopygui`
- **Topics:** `open-source`, `navanem`, `windows`, `robocopy`, `file-sync`, `file-copy`, `desktop-app`, `csharp`

### `sysinfo-tool`

- **Description:** A lightweight BGInfo-style Windows utility that generates a polished system information panel and applies it as the desktop wallpaper.
- **Website:** `https://www.navanem.com/projects/navanem-sysinfotool`
- **Topics:** `open-source`, `navanem`, `windows`, `bginfo`, `system-information`, `desktop-wallpaper`, `dotnet`, `csharp`

### `payload-contact`

- **Description:** A Payload CMS 3 contact-form plugin with validation, spam protection, an admin inbox, and runtime configuration.
- **Website:** `https://www.navanem.com/projects/navanem-payload-contact`
- **Topics:** `open-source`, `navanem`, `typescript`, `payloadcms`, `payloadcms-v3`, `payload-plugin`, `contact-form`, `spam-protection`

### `payload-comments`

- **Description:** A Payload CMS 3 plugin for anonymous comments, reactions, optional moderation, and threaded replies.
- **Website:** `https://www.navanem.com/projects/navanem-payload-comments`
- **Topics:** `open-source`, `navanem`, `typescript`, `payloadcms`, `payloadcms-v3`, `payload-plugin`, `comments`, `moderation`

### `superdelete`

- **Description:** A .NET command-line utility for deleting Windows files and directories whose paths exceed the traditional 260-character limit.
- **Website:** `https://www.navanem.com/projects/navanem-superdelete`
- **Topics:** `open-source`, `navanem`, `windows`, `long-paths`, `file-management`, `command-line`, `dotnet`, `csharp`

### `powershell-scripts`

- **Description:** Practical PowerShell scripts for everyday system administration, automation, troubleshooting, and hardening.
- **Website:** `https://www.navanem.com/projects`
- **Topics:** `open-source`, `navanem`, `powershell`, `sysadmin`, `automation`, `windows`, `security`, `troubleshooting`

The Navanem project-page paths intentionally retain their current `navanem-` slugs because those are website routes, not GitHub repository names.

## Internal repository references

Each renamed repository will be audited for hard-coded references to its previous GitHub URL or clone command. Direct self-references will be updated to the new canonical URL through a focused branch and pull request when repository content must change. Generated files, vendored dependencies, lockfiles, historical changelogs, and third-party attribution will not be mechanically rewritten unless they actively produce a broken user-facing link.

Cross-repository references from other Navanem public repositories will be corrected only when they point to one of the renamed repositories. Redirects provide compatibility while these canonical references are updated.

## Execution sequence

1. Capture the current public metadata and repository state.
2. Confirm that all seven target names are available.
3. Create the public `.github` profile repository and prepare its profile on a review branch.
4. Rename repositories one at a time, verifying the redirect and repository state after each rename.
5. Update each repository's description, website link, and topics.
6. Audit and correct active self-links or cross-repository links through focused pull requests when required.
7. Merge the organization profile after its rendered preview is verified.
8. Update the organization display name and description.
9. Configure the six pinned repositories.
10. Verify the organization page as a public visitor and leave it open as the final deliverable.

## Verification and rollback

Verification will cover:

- All target names resolve under `https://github.com/navanem/`.
- Every old repository URL redirects to the intended new URL.
- Public visibility, fork status, default branch, issue state, releases, Actions, and security configuration remain intact.
- Every configured Navanem website URL returns the intended project page.
- Descriptions and topics match this specification exactly.
- The `.github/profile/README.md` renders correctly in GitHub light and dark themes, with no broken images or links.
- The public organization page shows the new display name, description, profile README, and six intended pins.
- `gta6-leonida-atlas` has the same repository metadata and commit identity captured before execution.

If a rename causes an unexpected integration failure, the repository will be renamed back immediately before any old-name repository can be created. Metadata values captured during preflight provide the rollback source. No destructive history rewrite, branch deletion, release deletion, or issue migration is permitted.

## Success criteria

The completed organization page should communicate, within one screen, that Navanem is an independent open-source software studio; show visitors what it builds; and give them direct paths to explore, use, report issues, and contribute. Repository URLs, names, descriptions, project links, and topics should follow one coherent system, while `gta6-leonida-atlas` remains untouched.
