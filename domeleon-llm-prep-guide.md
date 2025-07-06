# Domeleon LLM-Prep Guide

*This document is intended for LLM consumption within Cursor, Windsurf, etc.*.

Enable an LLM to scaffold or assist in building a Domeleon-based app within **5 minutes** by collecting all required Domeleon materials (types, docs, source) into `LLM/domeleon`, for two scenarios:

1. **From Scratch:** A blank Cursor/Windsurf project.
2. **Existing Domeleon App:** You already have Domeleon installed and code in place, but now want to generate `LLM/domeleon` to help the LLM understand your project.

---

## Common Prerequisites

- You are in a Cursor or Windsurf project directory.
- If Domeleon is missing, install:
  - **Mac/Linux (Bash):**
    ```bash
    npm install domeleon
    ```
  - **Windows (PowerShell):**
    ```powershell
    npm install domeleon
    ```
- Add the bundler for TypeScript declarations:
  - **Bash:**
    ```bash
    npm install --save-dev dts-bundle-generator
    ```
  - **PowerShell:**
    ```powershell
    npm install --save-dev dts-bundle-generator
    ```

---

## Scenario A: Starting From Scratch

1. **Initialize your project** (if not already):
   - **Bash:**
     ```bash
     npm init -y
     npm install domeleon
     ```
   - **PowerShell:**
     ```powershell
     npm init -y
     npm install domeleon
     ```
2. **Create an `src/` folder** and add a placeholder `index.html` with a `<div id="app"></div>`.

   > **Note:** In PowerShell, avoid using `echo` to create files as it may hang or behave unexpectedly. Use `Set-Content` instead.

   - **Bash:**
     ```bash
     mkdir -p src
     echo '<div id="app"></div>' > src/index.html
     ```
   - **PowerShell:**
     ```powershell
     mkdir src
     Set-Content -Path src\index.html -Value '<div id="app"></div>'
     ```

Proceed to **Step 3** to prepare `LLM/domeleon`.

---

## Scenario B: Existing Domeleon App

If you already have:

- `node_modules/domeleon`
- Your own `src/` with Domeleon code

Simply proceed to **Step 3** to generate the LLM support folder.

---

## Step 3: Create the LLM Support Folder

**⚠️ IMPORTANT:** PowerShell can hang when creating multiple directories in one command. Use individual commands instead.

- **Bash:**
  ```bash
  mkdir -p LLM/domeleon/typedefs LLM/domeleon/docs LLM/domeleon/source
  ```
- **PowerShell (Fixed - Create directories individually):**
  ```powershell
  mkdir LLM
  mkdir LLM\domeleon
  mkdir LLM\domeleon\typedefs
  mkdir LLM\domeleon\docs
  mkdir LLM\domeleon\source
  ```

---

## Step 4: Create Unified Type Declarations

**Note:** The current approach creates a unified file but still contains import statements. For a truly self-contained file, the Domeleon library should generate this as part of its build process.

### Simple Concatenation (Recommended - Creates Unified File)
- **Bash:**
  ```bash
  cat node_modules/domeleon/dist/*.d.ts > LLM/domeleon/typedefs/api.d.ts
  cat node_modules/domeleon/dist/addons/*/index.d.ts >> LLM/domeleon/typedefs/api.d.ts
  ```
- **PowerShell:**
  ```powershell
  Get-Content node_modules/domeleon/dist/*.d.ts | Out-File -FilePath LLM/domeleon/typedefs/api.d.ts -Encoding UTF8
  Get-Content node_modules/domeleon/dist/addons/*/index.d.ts | Out-File -FilePath LLM/domeleon/typedefs/api.d.ts -Append -Encoding UTF8
  ```

This creates a single file with all type definitions, though it still contains import statements that reference other files.

**Result:**  
`LLM/domeleon/typedefs/api.d.ts` contains all Domeleon type definitions in one file.

**Future Enhancement:** The Domeleon library could generate a truly self-contained API reference file optimized for LLM consumption.

---

## Step 5: Download the Documentation

**Strategy:** Both git clone and ZIP download get the latest version. Git is slightly more efficient, but ZIP is more reliable.

### Option 1: Git Clone (Recommended - More Efficient)
- **Bash:**
  ```bash
  rm -rf LLM/domeleon/docs
  timeout 30 git clone --depth=1 https://github.com/domeleon/domeleon-docs.git LLM/domeleon/docs || echo "Git clone failed, trying ZIP..."
  rm -rf LLM/domeleon/docs/.git
  ```
- **PowerShell:**
  ```powershell
  if (Test-Path LLM/domeleon/docs) { Remove-Item -Recurse -Force LLM/domeleon/docs }
  git clone --depth=1 https://github.com/domeleon/domeleon-docs.git LLM/domeleon/docs
  if (Test-Path LLM/domeleon/docs/.git) { Remove-Item -Recurse -Force LLM/domeleon/docs/.git }
  ```

### Option 2: ZIP Download (Fallback - More Reliable)
- **Bash:**
  ```bash
  curl -L -o domeleon-docs.zip https://github.com/domeleon/domeleon-docs/archive/refs/heads/main.zip
  unzip domeleon-docs.zip -d temp-domeleon-docs
  cp temp-domeleon-docs/domeleon-docs-main/README.md LLM/domeleon/docs/
  mkdir -p LLM/domeleon/docs/docs
  cp temp-domeleon-docs/domeleon-docs-main/docs/* LLM/domeleon/docs/docs/
  rm -rf temp-domeleon-docs domeleon-docs.zip
  ```
- **PowerShell:**
  ```powershell
  Invoke-WebRequest -Uri "https://github.com/domeleon/domeleon-docs/archive/refs/heads/main.zip" -OutFile "domeleon-docs.zip"
  Expand-Archive -Path "domeleon-docs.zip" -DestinationPath "temp-domeleon-docs"
  Copy-Item -Path "temp-domeleon-docs\domeleon-docs-main\README.md" -Destination "LLM\domeleon\docs\" -Force
  mkdir LLM\domeleon\docs\docs
  Copy-Item -Path "temp-domeleon-docs\domeleon-docs-main\docs\*" -Destination "LLM\domeleon\docs\docs\" -Recurse -Force
  Remove-Item -Recurse -Force temp-domeleon-docs; Remove-Item domeleon-docs.zip
  ```

**Result:**  
`LLM/domeleon/docs/` contains:
- `README.md` (main documentation)
- `docs/` subdirectory with all topic guides (components, forms, routing, etc.)

**Note:** Both methods download the same content (latest commit). Git clone is slightly more efficient, but ZIP is more reliable if git hangs or isn't available.

**Clean up:** Remove any build artifacts (if present):
- **Bash:**
  ```bash
  rm -f LLM/domeleon/docs/.gitignore LLM/domeleon/docs/LICENSE LLM/domeleon/docs/package.json
  ```
- **PowerShell:**
  ```powershell
  Remove-Item -Force LLM\domeleon\docs\.gitignore, LLM\domeleon\docs\LICENSE, LLM\domeleon\docs\package.json -ErrorAction SilentlyContinue
  ```

**Result:** Clean docs directory with only essential files:
- `README.md` - Main documentation
- `docs/` - Topic guides (components, forms, routing, etc.)

---

## Step 6: Download the Source Code

**Strategy:** Both git clone and ZIP download get the latest version. Git is slightly more efficient, but ZIP is more reliable.

### Option 1: Git Clone (Recommended - More Efficient)
- **Bash:**
  ```bash
  rm -rf LLM/domeleon/source
  timeout 30 git clone --depth=1 https://github.com/domeleon/domeleon.git LLM/domeleon/source || echo "Git clone failed, trying ZIP..."
  rm -rf LLM/domeleon/source/.git
  ```
- **PowerShell:**
  ```powershell
  if (Test-Path LLM/domeleon/source) { Remove-Item -Recurse -Force LLM/domeleon/source }
  git clone --depth=1 https://github.com/domeleon/domeleon.git LLM/domeleon/source
  if (Test-Path LLM/domeleon/source/.git) { Remove-Item -Recurse -Force LLM/domeleon/source/.git }
  ```

### Option 2: ZIP Download (Fallback - More Reliable)
- **Bash:**
  ```bash
  curl -L -o domeleon-source.zip https://github.com/domeleon/domeleon/archive/refs/heads/main.zip
  unzip domeleon-source.zip -d temp-domeleon-source
  mv temp-domeleon-source/domeleon-main LLM/domeleon/source
  rm -rf temp-domeleon-source domeleon-source.zip
  ```
- **PowerShell:**
  ```powershell
  Invoke-WebRequest -Uri "https://github.com/domeleon/domeleon/archive/refs/heads/main.zip" -OutFile "domeleon-source.zip"
  Expand-Archive -Path "domeleon-source.zip" -DestinationPath "temp-domeleon-source"
  Move-Item temp-domeleon-source/domeleon-main LLM/domeleon/source
  Remove-Item -Recurse -Force temp-domeleon-source; Remove-Item domeleon-source.zip
  ```

**Note:** Both methods download the same content (latest commit). Git clone is slightly more efficient, but ZIP is more reliable if git hangs or isn't available.

**Clean up:** Remove build artifacts to reduce clutter:
- **Bash:**
  ```bash
  rm -f LLM/domeleon/source/.gitignore LLM/domeleon/source/LICENSE LLM/domeleon/source/tsconfig.json LLM/domeleon/source/tsup.config.ts
  ```
- **PowerShell:**
  ```powershell
  Remove-Item -Force LLM\domeleon\source\.gitignore, LLM\domeleon\source\LICENSE, LLM\domeleon\source\tsconfig.json, LLM\domeleon\source\tsup.config.ts
  ```

**Result:** Clean source directory with only essential files:
- `src/` - Implementation code
- `package.json` - Exports and dependencies  
- `README.md` - Architecture and examples

---

## Step 7: LLM Hand-Off

Your `LLM/domeleon` folder now contains:

- **typedefs/api.d.ts** — unified type definitions
- **docs/README.md** — documentation overview
- **docs/docs/** — topic guides (components, forms, routing, etc.)
- **source/** — full Domeleon source code

**Provide** `LLM/domeleon` (and your blank or existing `src/`) to the LLM with a prompt such as:

> You have:
> - `src/` (current app files or empty)
> - `LLM/domeleon` containing types, docs, and source for Domeleon
>
> Scaffold or modify the app to:
> 1. Create/import a root component.
> 2. Hook it up with `new App({rootComponent, containerId:'app'})`.
> 3. Use forms, routing, or counters as examples.
>
> Reply with file diffs only for `src/`.

Feed the project to your LLM. In minutes, you'll have a working Domeleon UI scaffold.

---

## Summary

✅ **Successfully completed:** All Domeleon materials collected in `LLM/domeleon/`  
✅ **Type definitions:** Unified file with all APIs and JSDoc  
✅ **Documentation:** Comprehensive guides covering all features  
✅ **Source code:** src/, package.json, README.md 

**Ready for LLM consumption!**

---

## Troubleshooting

### PowerShell Commands Hanging
- **Problem:** PowerShell `New-Item` with multiple paths can hang
- **Solution:** Use individual `mkdir` commands instead
- **Example:** Instead of `New-Item -ItemType Directory -Force -Path a,b,c`, use:
  ```powershell
  mkdir a
  mkdir b  
  mkdir c
  ```

### Source Repository Access
- **Problem:** Git clone hangs or ZIP download fails
- **Solution:** 
  1. Try the alternative method (git vs ZIP)
  2. Check your internet connection
  3. Use the types and documentation (sufficient for most LLM tasks)
  4. The repository is public at https://github.com/domeleon/domeleon

### Network Issues
- **Problem:** Downloads fail or timeout
- **Solution:** 
  1. Check your internet connection
  2. Try using a VPN if GitHub is blocked
  3. Use alternative download methods (curl, wget)

---

## Future Enhancement: Library-Generated API Reference

**Note:** The Domeleon library could potentially generate this `LLM/domeleon` folder as part of its build process, producing a single consolidated API reference file with JSDoc comments optimized for LLM consumption rather than just tree-shaking. This would eliminate the need for manual bundling and provide better documentation for AI assistants.
