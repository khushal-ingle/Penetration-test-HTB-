
---

# SNMP Enumeration Walkthrough

---

## Task Goal

The objective of this task is to enumerate the SNMP service running on the target host in order to:

1. Identify the **administrator email address**
2. Determine the **customized SNMP server version**
3. Enumerate any **custom scripts or OIDs** and extract their output

---

## Question.1

> Enumerate the SNMP service and obtain the email address of the admin. Submit it as the answer.

### Command executed

```bash
snmpwalk -v2c -c public 10.129.41.85
```
<img width="575" height="150" alt="image" src="https://github.com/user-attachments/assets/d8c4431c-cfc8-4302-952f-de8264ead210" />

---

### What is happening in this screenshot

An SNMP walk is performed using:

* SNMP version `2c`
* Community string `public` (default and commonly misconfigured)
* Target host `10.129.41.85`

`snmpwalk` recursively queries all accessible SNMP Object Identifiers (OIDs) exposed by the target.

---

### Highlighted result

```text
iso.3.6.1.2.1.1.4.0 = STRING: "devadmin <devadmin@inlanefreight.htb>"
```

---

### Explanation

The OID `1.3.6.1.2.1.1.4.0` corresponds to **sysContact**, which often stores administrator contact information.

In this case, the SNMP service leaks the full administrator email address.

---

### Answer

```
devadmin@inlanefreight.htb
```

---

## Question.2

> What is the customized version of the SNMP server?

### Command executed
```
snmpwalk -v2c -c public 10.129.41.85
```
<img width="532" height="179" alt="image" src="https://github.com/user-attachments/assets/51e8e365-0cc0-41f2-a88b-77c8bdc43338" />


### Highlighted result

```text
iso.3.6.1.2.1.1.6.0 = STRING: "InFreight SNMP v0.91"
```

---

### What is happening in this screenshot

During the same SNMP walk, the **sysDescr/sysLocation–related fields** expose a custom SNMP service banner.

---

### Explanation

The SNMP service is not using a default implementation banner.
Instead, it exposes a customized version string:

```
InFreight SNMP v0.91
```

This information can help identify custom tooling or internally developed services.

---

### Answer

```
InFreight SNMP v0.91
```

---

## Question.3

> Enumerate the custom script that is running on the system and submit its output as the answer.

### Command executed
```
snmpwalk -v2c -c public 10.129.41.85
```
<img width="857" height="111" alt="image" src="https://github.com/user-attachments/assets/6e88c797-fd59-446e-a2a1-2903f3e9b45d" />

### Highlighted result

```text
iso.3.6.1.2.1.25.1.7.1.1.4.70.76.65.71 = STRING: "HTB{5nMp_fl4g_uidhfljnsldiuhbfsdij44738b2u763g}"
```

---

### What is happening in this screenshot

The SNMP walk reveals **host-resources OIDs** related to running processes or custom scripts.

One of the enumerated OIDs returns a string value instead of normal system metadata, indicating the presence of a **custom SNMP-exposed script**.

---

### Explanation

The custom script is misconfigured to expose its output directly via SNMP.
This output contains a flag, confirming sensitive data leakage through SNMP.

---

### Answer

```
HTB{5nMp_fl4g_uidhfljnsldiuhbfsdij44738b2u763g}
```

---

## Final Answers Summary

| Question             | Answer                                                          |
| -------------------- | --------------------------------------------------------------- |
| Admin Email Address  | [devadmin@inlanefreight.htb](mailto:devadmin@inlanefreight.htb) |
| SNMP Custom Version  | InFreight SNMP v0.91                                            |
| Custom Script Output | HTB{5nMp_fl4g_uidhfljnsldiuhbfsdij44738b2u763g}                 |


