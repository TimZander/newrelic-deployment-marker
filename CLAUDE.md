# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an Azure DevOps extension that creates deployment markers in New Relic. It allows Azure DevOps pipelines to notify New Relic when deployments occur, including revision info, changelog, description, and the user who triggered the deployment.

## Build & Publish Commands

```bash
# Install tfx-cli (one-time setup)
npm install -g tfx-cli

# Create extension package (.vsix file)
npx tfx-cli extension create
```

If UUID errors occur during extension creation, generate a new UUID v5 and update `buildtask/task.json`.

Upload the created .vsix file to the [Visual Studio Marketplace](https://aka.ms/vsmarketplace-manage).

## Architecture

- **vss-extension.json** - Extension manifest defining metadata, version (1.3.7), publisher (TimZander), and the contribution pointing to the buildtask folder
- **buildtask/task.json** - Task definition with inputs (APIKey, APPID, Revision, changelog, description) and execution config
- **buildtask/powershell.ps1** - Main execution script that calls New Relic's deployment API (`POST /v2/applications/{appid}/deployments.json`)
- **buildtask/ps_modules/VstsTaskSdk/** - Azure DevOps task SDK for PowerShell

## Key Details

- The task uses PowerShell 3 execution mode (Windows only)
- Requires TLS 1.1 or 1.2 for New Relic API calls
- Captures `$env:RELEASE_REQUESTEDFOREMAIL` as the deployment user
- Version must be synchronized between `vss-extension.json` and `buildtask/task.json`
