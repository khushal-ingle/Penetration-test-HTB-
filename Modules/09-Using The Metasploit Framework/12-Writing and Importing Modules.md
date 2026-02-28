
# Writing & Importing Metasploit Modules

Metasploit allows penetration testers to **extend the framework** by importing third-party modules or by **writing custom modules**. This is essential when a vulnerability exists but is not yet included in the official Metasploit Framework.

---

## Updating vs Manually Installing Modules

### 1. Updating Metasploit (Automatic Method)

Running an update ensures:

* Latest exploits
* New auxiliary modules
* Updated payloads

This works **only if the module has already been merged** into the official Metasploit GitHub repository.

---

### 2. Manual Module Installation (Selective Method)

If a specific exploit:

* Is not included in the current Metasploit release
* Exists as a Metasploit-compatible script elsewhere

You can manually download and install it.

The most common source for such modules is **ExploitDB**.

---

## Finding Metasploit Modules on ExploitDB

ExploitDB supports filtering exploits by **Metasploit Framework (MSF)** tag.

* Modules tagged with **MSF** are usually written in **Ruby (.rb)**
* These are often ready to be imported into Metasploit

You can search via:

* Web interface
* CLI tool: `searchsploit`

---

## Using searchsploit to Find MSF Modules

Example:

```
searchsploit nagios3
```

To filter only Ruby-based Metasploit modules:

```
searchsploit -t Nagios3 --exclude=".py"
```

Important notes:

* `.rb` does not always mean “Metasploit module”
* Some Ruby exploits are standalone scripts
* Metasploit modules must follow a specific structure

---

## Metasploit Directory Structure

### System-wide installation:

```
/usr/share/metasploit-framework/
```

Important directories:

* `modules/` → exploits, auxiliaries, post modules
* `plugins/`
* `tools/`
* `scripts/`

---

### User-specific directory:

```
~/.msf4/
```

Contains:

* Custom modules
* Loot
* Logs
* Plugins

If required folders do not exist inside `.msf4`, you must create them manually.

---

## Installing a Custom Exploit Module

### Step 1: Rename the Module Properly

Metasploit naming rules:

* Snake_case only
* No hyphens
* Alphanumeric + underscores

Examples:

```
nagios3_command_injection.rb
custom_http_rce.rb
```

---

### Step 2: Copy the Module to the Correct Path

Example:

```
cp 9861.rb /usr/share/metasploit-framework/modules/exploits/unix/webapp/nagios3_command_injection.rb
```

---

### Step 3: Reload Metasploit Modules

Option 1:

```
msfconsole -m /usr/share/metasploit-framework/modules/
```

Option 2 (inside msfconsole):

```
reload_all
```

---

### Step 4: Verify Module Installation

```
search nagios3
use exploit/unix/webapp/nagios3_command_injection
show options
```

If options appear correctly, the module is loaded successfully.

---

## Porting Scripts into Metasploit Modules

Sometimes exploits exist only as:

* Python
* PHP
* Standalone Ruby scripts

To integrate them into Metasploit:

* They must be rewritten as **Ruby Metasploit modules**
* Requires **basic Ruby knowledge**

Important rules:

* Metasploit modules use **hard tabs**
* Follow existing module templates
* Do not start from scratch

---

## Choosing Boilerplate Code

Best practice:

* Find an existing Metasploit module with similar behavior
* Copy its structure
* Modify logic and metadata

Example:

* Porting a Bludit exploit
* Use an existing Bludit Metasploit module as reference

---

## Understanding Mixins (include Statements)

At the top of a module, `include` statements define capabilities.

Common mixins:

* `Msf::Exploit::Remote::HttpClient`
  → HTTP communication
* `Msf::Exploit::PhpEXE`
  → PHP payload generation
* `Msf::Exploit::FileDropper`
  → Upload + cleanup files
* `Msf::Auxiliary::Report`
  → Store data in MSF database

Only include what your exploit actually needs.

---

## Module Metadata (Info Section)

Every module must define:

* Name
* Description
* Author(s)
* CVE / References
* Platform
* Architecture
* Targets
* Disclosure date
* Privilege requirement

This information appears when running:

```
info
```

---

## Defining Module Options

Options are variables configurable by the user.

Common option types:

* `OptString` → strings
* `OptInt` → integers
* `OptBool` → booleans
* `OptPath` → file paths

Example:

```
OptPath.new('PASSWORDS', [true, 'Password wordlist',
  File.join(Msf::Config.data_directory, 'wordlists', 'passwords.txt')])
```

---

## Adapting Exploit Logic

When porting:

* Replace CLI arguments with MSF options
* Replace print statements with `print_status`, `print_good`
* Use Metasploit HTTP client methods
* Ensure proper error handling

---

## Final Testing Checklist

After writing or porting a module:

1. Reload modules
2. Run `info`
3. Check `show options`
4. Execute against a test target
5. Verify database reporting
6. Ensure no runtime errors

---

## Key Takeaways

* ExploitDB is a major source of Metasploit-compatible modules
* Not all `.rb` exploits are Metasploit modules
* Proper naming and directory placement are critical
* Reloading modules is required after installation
* Porting exploits requires Ruby and Metasploit API knowledge
* Reusing boilerplate modules saves time and avoids errors

---
