# Domeleon LLM Readme

*You (the language model) are reading this inside Cursor/Windsurf.*

### Why you're reading this
Before you can scaffold or refactor a Domeleon app you need to *learn* the framework. The quickest way to do that is to give you its **types, docs and full source** in one local folder you can crawl instantly.

### How that happens
You will run a short, automated script (steps 1-7 below). It will:
1. Create `LLM/domeleon/` in the workspace.
2. Copy type declarations (`*.d.ts`) from the node-modules directory.
3. Download the official documentation.
4. Download the latest source code.

When that material at your fingertips, you can rapidly understand Domeleon without having to access the Internet or crawl through the node-modules directory.

### What you will do *after* the preparation
• Treat `LLM/domeleon/` as your knowledge base.  
• Generate or edit files only inside `src/` using normal **npm + Vite** workflow (`npm run dev`, `npm run build`).

> If `LLM/domeleon/` doesn't exist yet, start by executing the commands in Steps 1-7 to create it. Once the folder is ready, proceed to scaffold or enhance the app.

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

**⚠️ CRITICAL - PowerShell Hanging Issue:** PowerShell `mkdir` commands can hang indefinitely. Use `New-Item` with `-Force` instead.

- **Bash:**
  ```bash
  mkdir -p LLM/domeleon/typedefs LLM/domeleon/docs LLM/domeleon/source
  ```
- **PowerShell (Fixed - Use New-Item to avoid hanging):**
  ```powershell
  New-Item -ItemType Directory -Path "LLM" -Force
  New-Item -ItemType Directory -Path "LLM\domeleon" -Force
  New-Item -ItemType Directory -Path "LLM\domeleon\typedefs" -Force
  New-Item -ItemType Directory -Path "LLM\domeleon\docs" -Force
  New-Item -ItemType Directory -Path "LLM\domeleon\source" -Force
  ```

---

## Step 4: Copy Unified Type Declarations

Copy the `.d.ts` files from `node_modules/domeleon/dist` into `LLM/domeleon/typedefs/`, preserving the folder structure.

### Copy the Type Definitions
- **Bash:**
  ```bash
  mkdir -p LLM/domeleon/typedefs
  rsync -av --include='*/' --include='*.d.ts' --exclude='*' node_modules/domeleon/dist/ LLM/domeleon/typedefs/
  ```
- **PowerShell:**
  ```powershell
  mkdir LLM\domeleon\typedefs
  robocopy node_modules\domeleon\dist LLM\domeleon\typedefs *.d.ts /S
  ```

This copies all `.d.ts` files from `node_modules/domeleon/dist` (preserving the folder structure) into `LLM/domeleon/typedefs/`.

**Result:**  
`LLM/domeleon/typedefs/` contains all Domeleon type definitions, organized and ready for LLM consumption.

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

- **typedefs/** — unified type definitions
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
> 2. Hook it up with `app({ root: new MyRootComponent(), id: 'app'})`.
> 3. Use forms, routing, or counters as examples.
>
> Reply with file diffs only for `src/`.

Feed the project to your LLM. In minutes, you'll have a working Domeleon UI scaffold.

---

## Step 8: Dev-Server & TypeScript Quality-of-Life (Highly Recommended)

1. **Install Vite + Preact (runtime + dev-server)**
   ```bash
   npm install vite preact
   ```

2. **Add scripts to `package.json`** (use the *positional* root path – recent Vite versions deprecated `--root`):
   ```jsonc
   "scripts": {
     "dev": "vite src",
     "build": "vite build src --outDir ../dist --emptyOutDir",
     "preview": "vite preview ../dist --port 4180"
   }
   ```

3. **Create a root `tsconfig.json`** to silence editor squiggles like *Cannot find module './Foo'*:
   ```jsonc
   {
     "compilerOptions": {
       "target": "ESNext",
       "module": "ESNext",
       "moduleResolution": "Node",
       "strict": true,
       "jsx": "preserve",
       "lib": ["DOM", "ESNext"],
       "types": ["vite/client"],
       "skipLibCheck": true,
       "allowSyntheticDefaultImports": true
     },
     "include": ["src/**/*"]
   }
   ```

With these three tweaks the next LLM can `npm run dev` and iterate immediately – zero friction.

---

## Summary

✅ **Successfully completed:** All Domeleon materials collected in `LLM/domeleon/`  
✅ **Type definitions:** Unified files with all APIs and JSDoc  
✅ **Documentation:** Comprehensive guides covering all features  
✅ **Source code:** src/, package.json, README.md 

**Ready for LLM consumption!**

---

## Troubleshooting

### PowerShell Commands Hanging
- **Problem:** PowerShell `mkdir` commands can hang indefinitely
- **Solution:** Use `New-Item -ItemType Directory -Path "path" -Force` instead
- **Example:** Instead of `mkdir LLM\domeleon`, use:
  ```powershell
  New-Item -ItemType Directory -Path "LLM\domeleon" -Force
  ```

### PowerShell PSReadLine Errors
- **Problem:** PowerShell may show PSReadLine errors during git operations
- **Solution:** These are cosmetic errors and don't affect the actual operations
- **Note:** Git clone operations complete successfully despite the error messages

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