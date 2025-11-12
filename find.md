
# Find Command Cheat Sheet 🕵️
*A friendly guide to finding files without the headache*

## Basic Structure

```bash
find [where-to-look] [what-to-look-for] [what-to-do]
```

## Most Useful Search Options

### By Name

```bash
# Exact name (case sensitive)
find . -name "report.txt"

# Case insensitive name
find . -iname "REPORT.TXT"

# Wildcards (quotes prevent shell expansion)
find . -name "*.jpg"
find . -name "file*.txt"
```

### By Type

```bash
# Files only
find . -type f -name "*.js"

# Directories only  
find . -type d -name "node_modules"

# Common types: f=file, d=directory, l=symlink
```

### By Time

```bash
# Modified in last 24 hours
find . -mtime -1

# Modified more than 30 days ago
find . -mtime +30

# Accessed in last 10 minutes
find . -amin -10

# Changed in last 2 hours
find . -cmin -120
```

### By Size

```bash
# Larger than 100MB
find . -size +100M

# Smaller than 1KB
find . -size -1k

# Exactly 50 bytes
find . -size 50c

# Units: c=bytes, k=KB, M=MB, G=GB
```

### Depth Control (Super Useful!)

```bash
# Only current directory (no subdirectories)
find . -maxdepth 1 -name "*.txt"

# Go 2 levels deep max
find . -maxdepth 2 -name "*.js"

# Skip current directory, start from subdirectories
find . -mindepth 1 -maxdepth 3 -name "*.log"
```

## Combining Conditions

### AND (all must be true)

```bash
# JavaScript files modified in last day
find . -name "*.js" -mtime -1

# Large log files
find . -name "*.log" -size +10M
```

### OR (any can be true)

```bash
# Find both JS and TS files
find . -name "*.js" -o -name "*.ts"

# Files accessed OR modified today
find . -atime -1 -o -mtime -1
```

### NOT (exclude)

```bash
# All files except .git directories
find . -not -name ".git"

# Files that aren't directories
find . -not -type d
```

### Regex Patterns

```bash
# Basic regex matching
find . -regex ".*/test[0-9]+\.txt"

# Case insensitive regex
find . -iregex ".*/BACKUP.*\.(txt|log)"
```

## Useful Actions

### Just List Files (default)

```bash
find . -name "*.tmp" -print
```

### Delete Files (⚠️  Careful!)

```bash
# Safe approach - test first!
find . -name "*.tmp" -print
find . -name "*.tmp" -delete

# Interactive deletion (safer)
find . -name "*.tmp" -ok rm {} \;
```

### Execute Commands

#### 3 Ways To Execute:
```bash
# 1. Interactive (prompts before each action) - SAFEST!
find . -name "*.tmp" -ok rm {} \;

# 2. One command per file
find . -name "*.tmp" -exec rm {} \;
# Result: rm file1, then rm file2, then rm file3

# 3. Batch files together - MORE EFFICIENT!
find . -name "*.tmp" -exec rm {} +
# Result: rm file1 file2 file3
```

#### Other Useful Examples:
```bash
# Count lines (wc -l = word count, lines)
find . -name "*.js" -exec wc -l {} +

# Make scripts executable
find . -name "*.sh" -exec chmod +x {} \;
```

## Real World Examples

### Cleanup Operations

```bash
# Find node_modules directories to clean up
find . -type d -name "node_modules" -print

# Find and delete old log files
find . -name "*.log" -mtime +30 -delete

# Find large files taking up space
find . -size +100M -print
```

### Development Workflow

```bash
# Find all test files
find src -name "*test*.js" -print

# Find files with specific content (combined with grep)
find . -name "*.py" -exec grep -l "import pandas" {} \;

# Count lines in all source files
find src -name "*.js" -exec wc -l {} +
```

### System Maintenance

```bash
# Find empty directories
find . -type d -empty

# Find broken symlinks
find . -type l -exec test ! -e {} \; -print

# Find files you own
find . -user $(whoami) -name "*.txt"
```

## Pro Tips 💡

1. Test first! Always use -print before -delete
2. Use quotes around patterns with wildcards
3. Combine carefully - AND is implicit between conditions
4. Depth limits prevent runaway searches in deep directories
5. The -exec semicolon must be escaped: \; or ';'

## Quick Reference

| Option|	What it does|
|---:|:---|
|-name "pattern"|	Find by filename|
|-type f/d/l|	File, directory, or symlink|
|-mtime -n/+n|	Modified less/more than n days ago|
|-size +n/-n|	Larger/smaller than n|
|-maxdepth n|	Don't search deeper than n levels|
|-exec cmd {} \;|	Run command on each file|
|-delete|	Delete found files (careful!)|

>Remember: With great power comes great responsibility. Always double-check your find commands! 😊

This guide focuses on the most commonly used `find` options with practical examples and safety warnings where appropriate. It should be much more approachable than the man page while covering 95% of everyday use cases!
