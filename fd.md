# fd Command Cheat Sheet 🚀
*Find files the modern way - faster and friendlier*

## Basic Structure

```bash
fd [pattern] [where-to-look] [options]
```

> **Key Difference**: `fd` uses regex patterns by default and respects `.gitignore` automatically!

## Most Useful Search Options

### By Name

```bash
# Basic search (regex, case-insensitive by default for lowercase)
fd report

# Exact match
fd '^report\.txt$'

# Glob pattern (more like traditional find)
fd -g "*.jpg"
fd -g "file*.txt"

# Case sensitive search
fd -s Report

# Case insensitive (force)
fd -i REPORT
```

### By Type

```bash
# Files only
fd -tf report

# Directories only  
fd -td node_modules

# Symlinks only
fd -tl

# Executables only
fd -tx

# Empty files/directories
fd -te
```

### By Time

```bash
# Modified in last 24 hours
fd --changed-within 24h

# Modified more than 30 days ago
fd --changed-before 30d

# Flexible units: s, min, h, d, w, m, y
fd --changed-within 2h
fd --changed-before 1w
```

### By Size

```bash
# Larger than 100MB
fd --size +100m

# Smaller than 1KB
fd --size -1k

# Between 10KB and 1MB
fd --size +10k --size -1m

# Units: b, k, m, g (auto-lowercase)
```

### Depth Control

```bash
# Only current directory (no subdirectories)
fd -d 1 report

# Go 2 levels deep max
fd -d 2 '\.js$'

# Minimum depth (skip current dir)
fd --min-depth 2 --max-depth 4 config
```

## Ignoring & Including

### Exclude Patterns

```bash
# Ignore specific directories
fd --exclude node_modules --exclude .git

# Ignore patterns
fd -E '*.tmp' -E 'test_*'

# Show hidden files (. prefix)
fd -H config

# Show ignored files (.gitignore)
fd -I

# Show EVERYTHING (hidden + ignored)
fd -HI
```

### Include Only Specific Extensions

```bash
# JavaScript and TypeScript files
fd -e js -e ts

# Just PHP files
fd -e php
```

## Combining Conditions

### AND (use multiple options)

```bash
# Large JS files modified recently
fd -e js --size +100k --changed-within 1d

# Hidden config files in home directory
fd -H -tf config ~
```

### OR (use regex or multiple fd calls)

```bash
# JS or TS files (regex)
fd '\.(js|ts)$'

# Or with glob
fd -g '*.{js,ts}'
```

### NOT (exclude patterns)

```bash
# All files except in node_modules
fd -E node_modules

# Files not matching pattern
fd --exclude '*.log' --exclude '*.tmp'
```

## Useful Actions

### Just List Files (default)

```bash
fd report
```

### Execute Commands

```bash
# Run command on each file (like -exec {} \;)
fd -e tmp -x rm {}

# Batch files together (like -exec {} +)
fd -e tmp -X rm

# With placeholders
fd -e jpg -x convert {} {.}.png
# {}: full path
# {.}: without extension  
# {/}: basename
# {//}: parent directory
# {/.}: basename without extension
```

### Delete Files (⚠️ Careful!)

```bash
# Safe approach - test first!
fd '\.tmp$'
fd '\.tmp$' -X rm

# Or use -x for one-by-one
fd '\.tmp$' -x rm
```

## Real World Examples

### Cleanup Operations

```bash
# Find node_modules directories
fd -td '^node_modules$'

# Find and delete old log files
fd -e log --changed-before 30d -X rm

# Find large files
fd --size +100m

# Find empty directories
fd -td -te
```

### Development Workflow

```bash
# Find all test files
fd test -e js src/

# Find files with specific content (with grep)
fd -e py -x grep -l "import pandas"

# Count lines in all source files
fd -e js -X wc -l

# Find recently modified PHP files
fd -e php --changed-within 1d
```

### System Maintenance

```bash
# Find broken symlinks
fd -tl -x test ! -e {} \; -print

# Find files by owner (combine with find if needed)
find . -user $(whoami) -name "*.txt"  # fd doesn't have -user

# Search in specific directory only
fd config /etc -d 1
```

## Excluding Directories (Your Question!)

```bash
# Exclude Library and other dirs from home search
fd . ~ -E Library -E .Trash -E Downloads

# Exclude all hidden directories
fd -E '/\.' 

# Exclude multiple patterns
fd -e php -E vendor -E node_modules -E .git

# Create an alias for common exclusions
alias fdd='fd -E Library -E .Trash -E .cache -E node_modules'
```

## Advanced Features

### Full Path Search

```bash
# Search in full path (not just filename)
fd -p src/.*test

# Match directory and filename
fd -p 'controllers/.*\.php$'
```

### Parallel Execution

```bash
# Process files in parallel (uses all CPU cores)
fd -e jpg -x convert {} {.}.webp

# Control thread count
fd -e jpg -j 4 -x convert {} {.}.webp
```

### Output Formats

```bash
# Absolute paths
fd -a report

# Just print0 for piping to xargs
fd -0 '\.tmp$' | xargs -0 rm

# List format with details
fd | xargs ls -lh
```

## Configuration File

Create `~/.config/fd/ignore` to globally exclude patterns:

```
# ~/.config/fd/ignore
node_modules/
.git/
*.tmp
.DS_Store
```

Or use `~/.fdignore` in your home directory!

## Pro Tips 💡

1. `fd` respects `.gitignore` by default - use `-I` to override
2. Hidden files need `-H` to show up
3. Use `-tf` to search only files, `-td` for only directories
4. Glob patterns need `-g`, otherwise it's regex
5. `-x` runs per file, `-X` batches them (more efficient)
6. Smart case: lowercase = case-insensitive, any uppercase = case-sensitive

## Quick Reference

| Option | What it does |
|---:|:---|
| `fd pattern` | Search for pattern (regex) |
| `-g "*.ext"` | Glob pattern (like shell) |
| `-e ext` | Filter by extension |
| `-tf/-td/-tl` | Type: file/directory/symlink |
| `-H` | Include hidden files |
| `-I` | Include ignored files (.gitignore) |
| `-E pattern` | Exclude pattern |
| `-d N` | Max depth |
| `--size +/-N` | Size filter |
| `--changed-within` | Time filter |
| `-x cmd` | Execute command per file |
| `-X cmd` | Execute command batched |

## fd vs find Quick Translation

| find | fd |
|:---|:---|
| `find . -name "*.js"` | `fd -g "*.js"` or `fd '\.js$'` |
| `find . -type f` | `fd -tf` |
| `find . -type d` | `fd -td` |
| `find . -maxdepth 2` | `fd -d 2` |
| `find . -size +100M` | `fd --size +100m` |
| `find . -mtime -1` | `fd --changed-within 1d` |
| `find . -not -path "*/node_modules/*"` | `fd -E node_modules` |
| `find . -exec rm {} \;` | `fd -x rm` |
| `find . -exec rm {} +` | `fd -X rm` |

> Remember: `fd` is fast, respects `.gitignore`, and has sane defaults. When in doubt, just type `fd pattern`! 🎯