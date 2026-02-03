# opencode-llm-capture Installation Guide

A lightweight OpenCode plugin that captures and logs LLM API interactions for debugging and analysis.

## Installation

### Prerequisites

- Git installed
- Write access to the OpenCode config directory (default: `~/.config/opencode`)
- For macOS / Linux / WSL: symbolic links are recommended
- For Windows: administrator privileges are typically required to create symbolic links; directory junctions can be used as an alternative

### macOS / Linux / WSL

1. Clone or update the repository:

   ```bash
   if [ -d ~/.config/opencode/opencode-llm-capture ]; then
     cd ~/.config/opencode/opencode-llm-capture && git pull
   else
     git clone https://github.com/aiimoyu/opencode-llm-capture.git ~/.config/opencode/opencode-llm-capture
   fi
   ```

2. Create the plugins directory and create a symbolic link:

   ```bash
   mkdir -p ~/.config/opencode/plugins
   ln -s ~/.config/opencode/opencode-llm-capture/llm-capture.ts ~/.config/opencode/plugins/llm-capture.ts
   ```

   - To force overwrite an existing link: use `ln -sf ...`

3. Restart OpenCode (or reload plugins in OpenCode).

### Windows (CMD / PowerShell)

**Note:** Creating symbolic links on Windows typically requires administrator privileges or Developer Mode enabled. Alternatively, use directory junctions (`mklink /J`) or copy the file into the plugins directory.

**CMD (Administrator):**

```cmd
git clone https://github.com/aiimoyu/opencode-llm-capture.git "%USERPROFILE%\.config\opencode\opencode-llm-capture"
mkdir "%USERPROFILE%\.config\opencode\plugins"
mklink "%USERPROFILE%\.config\opencode\plugins\llm-capture.ts" "%USERPROFILE%\.config\opencode\opencode-llm-capture\llm-capture.ts"
```

**PowerShell (Administrator):**

```powershell
git clone https://github.com/aiimoyu/opencode-llm-capture.git "$env:USERPROFILE\.config\opencode\opencode-llm-capture"
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.config\opencode\plugins"
New-Item -ItemType SymbolicLink -Path "$env:USERPROFILE\.config\opencode\plugins\llm-capture.ts" -Target "$env:USERPROFILE\.config\opencode\opencode-llm-capture\llm-capture.ts"
```

---

## Usage / Verification

1. Verify the symbolic link (macOS / Linux):

   ```bash
   ls -l ~/.config/opencode/plugins/llm-capture.ts
   ```

   Should show a pointer to `~/.config/opencode/opencode-llm-capture/llm-capture.ts`.

2. Windows verification (PowerShell):

   ```powershell
   Get-Item "$env:USERPROFILE\.config\opencode\plugins\llm-capture.ts"
   ```

3. Restart OpenCode; check the plugins/skills panel to confirm `llm-capture` is loaded, or check the OpenCode console/logs for any plugin loading errors.

4. Refer to the README or examples in the repository for the plugin's exported commands/skills and configuration options.

---

## Updating

When the repository already exists, pull the latest code and restart OpenCode:

```bash
cd ~/.config/opencode/opencode-llm-capture && git pull
# Restart OpenCode
```

If you used a copy instead of a symbolic link, you will need to re-copy the entry file into the plugins directory after updating.

---

## Uninstall

1. Remove the symbolic link / plugin file:
   - macOS / Linux:

     ```bash
     rm ~/.config/opencode/plugins/llm-capture.ts
     ```

   - Windows (Administrator CMD):

     ```cmd
     del "%USERPROFILE%\.config\opencode\plugins\llm-capture.ts"
     ```

2. (Optional) Remove the repository:

   ```bash
   rm -rf ~/.config/opencode/opencode-llm-capture
   ```

3. Restart OpenCode.

---

## Troubleshooting

- **Symbolic link creation fails / Permission denied (EACCES)**
  - Check directory permissions: `ls -ld ~/.config/opencode ~/.config/opencode/plugins`
  - Avoid having the config directory owned by root; if files were created with `sudo`, they may not be readable by your user.

- **Cannot create symbolic links on Windows**
  - Run terminal as Administrator, or enable Developer Mode in Settings, or use `mklink /J` to create a directory junction, or copy the file to the plugins directory (not recommended for updates).

- **OpenCode does not load the plugin**
  - Confirm the plugin file extension (`.ts` / `.js`) is supported by OpenCode.
  - Check for syntax/compilation errors in the plugin and review the OpenCode console logs for error details.

- **Link points to wrong target path**
  - Use absolute paths when creating symbolic links to avoid issues caused by relative paths and differing working directories.

---

## Testing

- After installation, use OpenCode's plugin/skill list to check if `llm-capture` appears.
- Add a simple `console.log` or logging statement in the plugin code, restart OpenCode, and observe the console output to verify the plugin is being executed.

---

## Getting Help

- Repository & Issues: <https://github.com/aiimoyu/opencode-llm-capture>
- If you encounter issues you cannot resolve, please open an Issue including your platform, OpenCode version, error logs, and the commands you executed.

---

License and contribution guidelines can be found in the LICENSE and CONTRIBUTING files in the repository root.
