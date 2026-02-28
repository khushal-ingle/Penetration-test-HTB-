
# Spawning Interactive Shells

In many real-world exploits and Hack The Box labs, the first shell we obtain is **limited**.
This is often called a **non-interactive**, **non-TTY**, or **restricted (jail) shell**.

Typical symptoms:

* No prompt or broken prompt
* `sudo`, `su`, `clear`, `nano`, `vim` don’t work properly
* Ctrl+C / Ctrl+Z doesn’t behave correctly
* Commands appear to hang or return nothing

This usually happens because:

* The shell was spawned by a **web server user** (e.g., `apache`, `www-data`)
* No terminal (TTY) is allocated
* The process was executed non-interactively

To work effectively, we must **upgrade** the shell.

---

## Why Interactive (TTY) Shells Matter

A proper interactive shell gives us:

* Job control
* Stable command execution
* Ability to run `sudo -l`, `su`, text editors
* Cleaner output
* Easier privilege escalation

This step is **mandatory** before serious post-exploitation.

---

## Basic Interactive Shell

If `/bin/sh` exists (almost always true):

```bash
/bin/sh -i
```

What this does:

* Launches the shell in **interactive mode** (`-i`)
* Gives basic command interaction

Typical output:

```
sh: no job control in this shell
sh-4.2$
```

This is better than nothing, but still **not a full TTY**.

---

## Python (Most Common and Best)

If Python exists (very common on Linux):

```bash
python -c 'import pty; pty.spawn("/bin/sh")'
```

Why this works:

* Python allocates a **pseudo-terminal (PTY)**
* `/bin/sh` runs inside it
* You now have a **real interactive shell**

Result:

```
sh-4.2$ whoami
apache
```

This is the **preferred method**.

If Python3:

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

---

## When Python Is NOT Installed

In restricted environments, Python may be missing.
You should then try alternative interpreters.

---

## Perl

If Perl is present:

```bash
perl -e 'exec "/bin/sh";'
```

Or inside a script:

```perl
exec "/bin/sh";
```

Perl can directly execute shell binaries.

---

## Ruby

If Ruby exists:

```ruby
exec "/bin/sh"
```

Must be run inside a Ruby context or script.

---

## Lua

If Lua is available:

```lua
os.execute('/bin/sh')
```

Uses Lua’s OS execution functions.

---

## AWK (Very Common)

AWK is installed on almost all Unix/Linux systems.

```bash
awk 'BEGIN {system("/bin/sh")}'
```

This spawns a shell using AWK’s `system()` call.

---

## Using `find` to Spawn a Shell

### Using AWK via find

```bash
find / -name anything -exec /bin/awk 'BEGIN {system("/bin/sh")}' \;
```

### Direct shell execution

```bash
find . -exec /bin/sh \; -quit
```

Why this works:

* `find` can execute arbitrary commands
* Useful when direct shell execution is restricted

---

## VIM Shell Escape (Rare but Powerful)

If you can run `vim`:

### One-liner

```bash
vim -c ':!/bin/sh'
```

### Inside vim

```vim
:set shell=/bin/sh
:shell
```

This is especially useful when:

* `vim` is allowed via `sudo`
* Used in privilege escalation scenarios

---

## Execution Permissions Awareness

Always verify permissions before assuming something will work.

### Check file permissions

```bash
ls -la /path/to/binary
```

Look for:

* Execute (`x`) permission
* Owner/group permissions

---

## Checking Sudo Permissions (Critical Step)

Once you have a **stable interactive shell**:

```bash
sudo -l
```

Example output:

```
User apache may run the following commands on ILF-WebSrv:
    (ALL : ALL) NOPASSWD: ALL
```

This means:

* You can run **any command as root**
* Privilege escalation is trivial

⚠️ Important:

* `sudo -l` **will not work properly** in unstable shells
* Always upgrade your shell first

---

## Key Takeaways

* Initial shells are often **non-interactive**
* Always upgrade to a **TTY shell**
* Python is the best option, but not the only one
* Know **multiple methods** for real-world reliability
* A stable shell is required for:

  * `sudo -l`
  * privilege escalation
  * serious post-exploitation


