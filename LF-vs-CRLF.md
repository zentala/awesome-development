# Line Endings: LF vs CRLF

**Line endings** are invisible characters that mark the end of a line in text files. Different operating systems use different conventions, which can cause problems in cross-platform development. Understanding and properly configuring line endings is essential for modern software development.

## What Are Line Endings?

| Type | Characters | Size | Used By |
|------|-----------|------|---------|
| **LF** (Line Feed) | `\n` | 1 byte | Unix, Linux, macOS, Git |
| **CRLF** (Carriage Return + Line Feed) | `\r\n` | 2 bytes | Windows (legacy) |
| **CR** (Carriage Return) | `\r` | 1 byte | Old Mac OS (pre-OSX) |

## Why Always Use LF?

**Default Rule: Always use LF** unless you have a specific reason not to.

### Technical Benefits

✅ **Cross-Platform Compatibility**
- Works on Linux, macOS, and Windows
- All modern Windows editors support LF (VS Code, Notepad++, Sublime, etc.)
- Windows 10+ Notepad supports LF natively

✅ **Smaller Files**
- LF uses 1 byte vs CRLF's 2 bytes
- Can reduce file size by ~0.1-1% depending on line density

✅ **Git Standard**
- Git repositories use LF internally
- Prevents "LF will be replaced by CRLF" warnings
- Avoids unnecessary line ending changes in diffs
- Eliminates merge conflicts caused by line endings

✅ **Container/Cloud Compatibility**
- Docker containers run Linux - require LF
- CI/CD pipelines typically run on Linux
- Cloud functions and serverless platforms expect LF

✅ **Shell Scripts**
- Bash scripts (`.sh`) **require** LF to work properly
- CRLF in shell scripts causes `\r: command not found` errors
- Python, Ruby, Perl scripts also expect LF

## When to Use CRLF?

Only use CRLF for Windows-specific files:

| File Type | Line Ending | Why? |
|-----------|-------------|------|
| `.bat`, `.cmd` | CRLF | Required by Windows Command Prompt |
| `.ps1` | CRLF | PowerShell scripts (though PS supports LF too) |
| `.reg` | CRLF | Windows Registry files require CRLF |
| Everything else | **LF** | Cross-platform compatibility |

**Note:** Modern PowerShell (v6+) works fine with LF, but older versions may have issues.

## How to Configure Git for LF

### 1. Global Git Configuration

Set Git to always use LF in the repository:

```bash
git config --global core.autocrlf input
```

**What this does:**
- On **Windows**: Converts CRLF to LF when committing, keeps LF when checking out
- On **Linux/Mac**: Keeps LF everywhere (no conversion)
- **Result**: Repository always contains LF, no matter which OS you use

This is the best setting for cross-platform development. Alternative values:
- `true` - Converts to CRLF on checkout (❌ bad for cross-platform)
- `false` - No conversion (can lead to mixed line endings)
- `input` - ✅ **Recommended**: Converts to LF on commit, no conversion on checkout

### 2. Create `.gitattributes` File

Every project should have a `.gitattributes` file in the repository root:

```gitattributes
# Set default behavior - normalize all files to LF
* text=auto eol=lf

# Explicitly declare text files
*.js text eol=lf
*.jsx text eol=lf
*.ts text eol=lf
*.tsx text eol=lf
*.json text eol=lf
*.md text eol=lf
*.html text eol=lf
*.css text eol=lf
*.scss text eol=lf
*.yml text eol=lf
*.yaml text eol=lf
*.xml text eol=lf
*.txt text eol=lf
*.sh text eol=lf
*.bash text eol=lf
*.py text eol=lf
*.rb text eol=lf
*.php text eol=lf
*.java text eol=lf
*.c text eol=lf
*.cpp text eol=lf
*.h text eol=lf

# Windows-specific files - use CRLF
*.bat text eol=crlf
*.cmd text eol=crlf
*.ps1 text eol=crlf
*.reg text eol=crlf

# Binary files - don't touch
*.png binary
*.jpg binary
*.jpeg binary
*.gif binary
*.ico binary
*.pdf binary
*.zip binary
*.tar binary
*.gz binary
*.exe binary
*.dll binary
*.woff binary
*.woff2 binary
*.ttf binary
*.eot binary
```

### 3. Normalize Existing Repository

If you're fixing an existing project with mixed line endings:

```bash
# Add .gitattributes file first
git add .gitattributes

# Remove all files from Git's index (not from disk!)
git rm --cached -r .

# Re-add all files - Git will normalize line endings
git add .

# Verify changes
git status

# Commit the normalization
git commit -m "chore: normalize line endings to LF"
```

## Common Problems and Solutions

### Problem: "LF will be replaced by CRLF" Warnings

**Symptoms:**
```
warning: LF will be replaced by CRLF in src/App.tsx
The file will have its original line endings in your working directory
```

**What's Happening:**
- Your file has LF line endings
- Git is configured to convert LF → CRLF when checking out files (`core.autocrlf = true`)
- Git warns you that it will change your file

**Why This Is Bad:**
- Creates fake changes in `git diff`
- Causes merge conflicts
- Breaks shell scripts and Python scripts
- Wastes time with pointless line ending conversions

**Solution:**

```bash
# Step 1: Configure Git to NOT convert LF to CRLF
git config core.autocrlf input

# Step 2: Re-normalize all files according to .gitattributes
git add --renormalize .

# Step 3: Verify - you should see no warnings now
git status
```

**What `git add --renormalize .` does:**
1. Reads `.gitattributes` rules
2. Removes all files from Git index (staging area)
3. Re-adds them with correct line endings
4. Normalizes everything according to your rules
5. Shows which files were changed

After this, Git will:
- ✅ Keep LF in repository
- ✅ Keep LF in your working directory
- ✅ Never warn about line endings again
- ✅ Work the same on Windows, Linux, and Mac

### Problem: Shell Script Fails with `\r: command not found`

**Cause:** Shell script has CRLF line endings.

**Solution:**
```bash
# Convert CRLF to LF
dos2unix script.sh

# Or using sed
sed -i 's/\r$//' script.sh

# Or using tr
tr -d '\r' < script.sh > script_fixed.sh
```

### Problem: `.bat` File Doesn't Work on Windows

**Cause:** Batch file has LF instead of CRLF.

**Solution:** Add to `.gitattributes`:
```gitattributes
*.bat text eol=crlf
*.cmd text eol=crlf
```

Then re-normalize:
```bash
git add --renormalize .
```

### Problem: WSL Scripts Fail When Called from Windows

**Symptoms:**
```bash
./script.sh
bash: ./script.sh: /bin/bash^M: bad interpreter: No such file or directory
```

**Cause:** Script has CRLF line endings (the `^M` is the visible carriage return `\r`).

**Solution:**
```bash
# In WSL, convert the script
dos2unix script.sh

# Or ensure .gitattributes has:
*.sh text eol=lf
```

## Checking Line Endings

### Check a Single File

**Linux/Mac/Git Bash:**
```bash
file script.sh
# Output: script.sh: ASCII text

# or
cat -A script.sh
# LF shows as: $
# CRLF shows as: ^M$
```

**Windows PowerShell:**
```powershell
# Show hex dump
Format-Hex script.sh | Select-String "0D 0A|0A"
# 0D 0A = CRLF
# 0A = LF
```

**VS Code:**
- Look at bottom-right corner
- Shows "LF" or "CRLF"
- Click to change

### Check Entire Repository

```bash
# Find files with CRLF
git ls-files --eol | grep crlf

# Count files by line ending type
git ls-files --eol | cut -d' ' -f1 | sort | uniq -c
```

## IDE Configuration

### VS Code

Add to `.vscode/settings.json`:
```json
{
  "files.eol": "\n",
  "files.insertFinalNewline": true,
  "files.trimTrailingWhitespace": true
}
```

Or set globally:
- File → Preferences → Settings
- Search "eol"
- Set "Files: Eol" to `\n`

### JetBrains IDEs (WebStorm, IntelliJ, PyCharm)

Settings → Editor → Code Style → Line separator → Unix and macOS (\n)

### Sublime Text

Preferences → Settings:
```json
{
  "default_line_ending": "unix"
}
```

## Docker and Containers

**Critical:** Docker containers run Linux and require LF line endings.

If you copy a script with CRLF into a Docker container:
```dockerfile
COPY script.sh /app/
RUN chmod +x /app/script.sh
RUN /app/script.sh  # ❌ FAILS with "command not found"
```

**Solution:** Ensure LF before building:
```dockerfile
# Convert line endings in Dockerfile
COPY script.sh /app/
RUN dos2unix /app/script.sh && chmod +x /app/script.sh
```

Or better - fix line endings in repository using `.gitattributes`.

## Best Practices Summary

✅ **DO:**
- Use LF for all text files by default
- Set `git config core.autocrlf input` (or globally with `--global`)
- Create `.gitattributes` in every project
- Use CRLF only for Windows-specific files (`.bat`, `.cmd`, `.reg`)
- Test shell scripts on Linux/Mac or in Docker
- Check line endings in VS Code status bar
- Add `.gitattributes` to new projects immediately

❌ **DON'T:**
- Use `core.autocrlf true` (converts to CRLF on checkout - causes problems)
- Mix line endings in the same file
- Forget to normalize when fixing line endings
- Assume CRLF works everywhere because you're on Windows
- Ignore "LF will be replaced by CRLF" warnings

## Quick Setup for New Project

```bash
# Set Git config (one time, global)
git config --global core.autocrlf input

# Create .gitattributes
cat > .gitattributes << 'EOF'
* text=auto eol=lf
*.bat text eol=crlf
*.cmd text eol=crlf
*.ps1 text eol=crlf
*.png binary
*.jpg binary
*.jpeg binary
*.gif binary
*.pdf binary
EOF

# Commit
git add .gitattributes
git commit -m "chore: configure line endings"
```

## Resources

* [Git Documentation: gitattributes](https://git-scm.com/docs/gitattributes)
* [GitHub: Configuring Git to handle line endings](https://docs.github.com/en/get-started/getting-started-with-git/configuring-git-to-handle-line-endings)
* [EditorConfig](https://editorconfig.org/) - Cross-editor configuration for line endings
* [dos2unix](https://waterlan.home.xs4all.nl/dos2unix.html) - Tool for converting line endings
* [Git core.autocrlf documentation](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration)
