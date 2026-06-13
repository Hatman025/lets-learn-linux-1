# 🛡️ DevSecOps Fundamentals — Parocyber Assignment 1

A hands-on Linux command-line assignment covering file system navigation, directory management, I/O redirection, links, and log handling from a DevSecOps perspective.

---

## 📑 Table of Contents

- [Section 1 — System Orientation](#section-1--system-orientation)
- [Section 2 — Directory & File Management](#section-2--directory--file-management)
- [Section 3 — File I/O, Links & Redirection](#section-3--file-io-links--redirection)
- [Key Takeaways](#key-takeaways)

---

## Section 1 — System Orientation

### Q1 — First Commands on an Unknown Server
```bash
pwd       # Current working directory
whoami    # Current logged-in user
uname -r  # OS and kernel details
```
> When SSHing into an unknown server, these three commands establish *who* you are, *where* you landed, and *what* the system is running — foundational for any investigation.

---

### Q2 — The Linux Root Filesystem (`/`)
The root directory is the absolute base of every Linux system, following the **Filesystem Hierarchy Standard (FHS)**.

| Directory | Purpose |
|-----------|---------|
| `/bin` | Essential binaries (`ls`, `cd`, `pwd`) required at boot |
| `/dev` | Virtual device files representing hardware |
| `/home` | Personal storage for regular users |
| `/tmp` | Temporary session files (cache, locks, installers) |
| `/usr` | User utilities, libraries, and applications |

---

### Q3 — Hidden Files in the Home Directory
```bash
ls          # Default listing
ls -la      # Reveals hidden files with full details
```
- Files prefixed with `.` are hidden by default
- `.bash_logout` — runs automatically on logout from a login shell
- `.face` — profile picture used by some desktop environments

---

## Section 2 — Directory & File Management

### Q4 — Creating Nested Directories in One Command
```bash
mkdir -p projects/cyphercore/{configs,logs/{access,errors},reports,archive}
```
**Brace expansion** (`{}`) generates multiple path strings from a single pattern, making complex structures instant to scaffold.

---

### Q5 — Creating Multiple Empty Files
```bash
touch configs/app.conf configs/db.conf logs/access/access.log \
      logs/errors/error.log reports/weekly_report.txt
```
> `touch` creates empty files and updates timestamps. Accidentally modifying timestamps in the wrong place can cause unexpected rebuilds, skipped syncs, or broken caches.

---

### Q6 — Listing Files in Human-Readable Format
```bash
ls -lh ~/projects/configs/
```
- `-l` — long format (permissions, owner, size, timestamp)
- `-h` — human-readable sizes (KB, MB, GB)
- New files show `664` permissions: owner/group get read+write, others get read-only

---

### Q7 — Visualising the Directory Tree
```bash
tree ~/projects/cyphercore     # Hierarchical tree view
ls -R ~/projects/cyphercore    # Recursive listing
```

---

## Section 3 — File I/O, Links & Redirection

### Q8 — Appending to Log Files
```bash
echo "line" >> logs/access/access.log   # Append
echo "line" >  logs/access/access.log   # Overwrite (destructive!)
```
> ⚠️ Confusing `>` and `>>` in a production log rotation script can silently destroy logs. Once overwritten with `>`, the data is gone without a backup.

---

### Q9 — Reading Files Top-to-Bottom and Bottom-to-Top
```bash
cat logs/access/access.log    # Top → bottom
tac logs/access/access.log    # Bottom → top
tail logs/access/access.log   # Last 10 lines
```
> During a live incident with 80,000-line logs, `tac` or `tail` gets you to the most recent events immediately.

---

### Q10 — Heredoc for Multi-line File Writes
```bash
cat >> logs/errors/error.log << EOF
ERROR 2025-06-02 ...
EOF
```
- **Unquoted `EOF`** — variables and commands expand inside the block
- **Quoted `'EOF'`** — text is written literally (useful for scripts and config files)

> Heredoc is preferred for scripted, multi-line writes. It appears only in shell history and requires no interactive editor keystrokes.

---

### Q11 — Pagers: `more` vs `less`

| Feature | `more` | `less` |
|---------|--------|--------|
| Direction | Forward only | Forward & backward |
| Search | Limited | `/pattern` (forward), `?pattern` (backward) |
| Memory use | Minimal | Buffered |
| Jump to end | No | `G` |

> On a server with 512 MB RAM reading a 3 GB log — always use `less`.

---

### Q12 — Copying and Moving Files
```bash
cp logs/errors/error.log archive/error_2025-06-02.log        # Duplicate
mv logs/access/access.log logs/access/access_2025-06-02.log  # Rename/move
```
- `cp` leaves the original in place; `mv` removes it from the source

---

### Q13 — Removing Directories Safely
```bash
rmdir reports/temp_drafts    # Removes empty directories only
rm -rf logs/                 # Recursively removes everything — no warning
```
> ⚠️ `rm -rf` is irreversible. Always `ls` the target first and be absolutely certain before running it. Misuse can wipe an entire OS.

---

### Q15 & Q16 — Hard Links
```bash
ln configs/db.conf configs/db_hardlink.conf
ls -li configs/   # Same inode number confirms the hard link
rm configs/db.conf  # Data persists in the hard link
```
- Hard links share the same **inode** — data survives deletion of the original
- Link count drops to `1` after the original is removed; data is only gone when all links are deleted

> 🔴 **Security Risk:** An attacker can create a hard link in an obscure directory, delete the obvious file, and the data remains — hidden under a different name.

---

### Q17 — Symbolic (Soft) Links
```bash
ln -s configs/app.conf app_config_link
```
- Identified by `->` in `ls -la` output
- Deleting the original creates a **dangling symlink** — reads fail with `No such file or directory`

> 🔴 Dangling symlinks in deployment pipelines cause failures that are difficult to debug under pressure — a symlink may work in dev but break silently in prod if the target was never deployed.

---

### Q18 — Hard Link vs Soft Link Comparison

| Property | Hard Link | Soft Link |
|----------|-----------|-----------|
| Shares inode with original | ✅ Yes | ❌ No |
| Works across filesystems | ❌ No | ✅ Yes |
| Survives deletion of original | ✅ Yes | ❌ No |
| Can link to a directory | ❌ No | ✅ Yes |
| Shows as `l` in `ls -la` | ❌ No | ✅ Yes |

**Real-world use cases:**
- **Hard links** — immutable audit logs; security teams link critical logs to a protected directory so they survive deletion attempts
- **Soft links** — versioned config management; deployment pipelines use symlinks to point to the current active config or binary

---

### Q19 — stdout, stdin & stderr Redirection
```bash
ls logs/ nonexistent/ 1> reports/stdout_output.txt 2> reports/stderr_output.txt
```

| Stream | Descriptor | Direction |
|--------|-----------|-----------|
| `stdin` | `0` | Input |
| `stdout` | `1` | Normal output |
| `stderr` | `2` | Error output |

---

### Q20 — Heredoc with Variable Expansion
```bash
# Unquoted EOF — variables expand
cat >> reports/weekly_report.txt << EOF
Report Date: $DATE
EOF

# Quoted 'EOF' — written literally, no expansion
cat >> reports/weekly_report.txt << 'EOF'
Cost: $TOTAL
EOF
```

---

## Key Takeaways

1. **Think like an attacker** — the same commands analysts use to investigate are the same ones intruders use to hide activity. Never assume a familiar-looking filename is safe.

2. **Hard links and soft links can conceal malicious files** — always verify inodes and symlink targets, not just filenames.

3. **`mkdir -p` and brace expansion** show the real power of CLI over GUI — entire directory structures created in a single, scriptable, reproducible command.

4. **`>` vs `>>` is a critical distinction** — one wrong operator in a log rotation script silently destroys data.

5. **`rm -rf` has no undo** — always verify before executing; it can remove an entire operating system with no warning.

---

*DevSecOps Parocyber — Assignment 1*
