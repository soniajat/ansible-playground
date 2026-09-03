# YAML Basics

## What is YAML?

YAML stands for **YAML Ain't Markup Language**.

It is a human-readable data serialization language commonly used for configuration files.

Ansible uses YAML to define:

- Playbooks
- Variables
- Inventories
- Roles
- Configuration files

YAML files usually use the following extensions:

```text
.yml
.yaml
```

Example:

```yaml
name: Sonia
role: DevOps Engineer
experience: 2 years
```

---

# YAML Syntax Rules

## 1. Indentation

YAML uses indentation to define structure.

Spaces are used for indentation.

```yaml
server:
  name: web-server
  port: 80
```

Incorrect indentation can cause YAML parsing errors.

> Use spaces instead of tabs.

---

## 2. Key-Value Pairs

Data can be written using key-value pairs.

Syntax:

```yaml
key: value
```

Example:

```yaml
name: Ansible
type: Automation Tool
```

The key and value are separated using a colon (`:`).

---

## 3. Lists

Lists are represented using a hyphen (`-`).

Example:

```yaml
tools:
  - Linux
  - Docker
  - Ansible
```

Another example:

```yaml
servers:
  - web-server
  - database-server
  - application-server
```

---

## 4. Dictionaries

A dictionary contains multiple key-value pairs.

Example:

```yaml
server:
  name: web-server
  os: Ubuntu
  port: 80
```

Here, `server` contains multiple properties.

---

## 5. List of Dictionaries

YAML can also contain a list where each item is a dictionary.

Example:

```yaml
servers:
  - name: web-server
    ip: 192.168.1.10

  - name: database-server
    ip: 192.168.1.20
```

This structure is commonly useful when defining multiple objects with multiple properties.

---

## 6. Strings

Strings can be written with or without quotes.

```yaml
name: Ansible
```

or:

```yaml
name: "Ansible"
```

Quotes are useful when special characters are involved.

Example:

```yaml
message: "Hello: Welcome to Ansible"
```

---

## 7. Boolean Values

Boolean values represent true or false.

```yaml
enabled: true
debug: false
```

---

## 8. Numbers

Numbers can be written directly.

```yaml
port: 8080
replicas: 3
```

---

## 9. Comments

Comments begin with `#`.

```yaml
# This is a comment

name: Ansible
```

Comments are ignored when the YAML file is processed.

---

## 10. Multi-Line Strings

The pipe symbol (`|`) can be used for multi-line strings.

```yaml
message: |
  Hello,
  Welcome to Ansible.
  This is a multi-line message.
```

---

# Example YAML Structure

```yaml
application:
  name: demo-app
  environment: development
  port: 8080

servers:
  - name: web-server-01
    os: Ubuntu

  - name: web-server-02
    os: Ubuntu

features:
  monitoring: true
  logging: true
```

---

# Common YAML Mistakes

## Incorrect Indentation

Incorrect:

```yaml
server:
name: web-server
port: 80
```

Correct:

```yaml
server:
  name: web-server
  port: 80
```

---

## Using Tabs

YAML does not allow tabs for indentation.

Incorrect:

```text
	server:
		name: web-server
```

Correct:

```yaml
server:
  name: web-server
```

---

## Missing Space After Colon

Incorrect:

```yaml
name:Ansible
```

Correct:

```yaml
name: Ansible
```

---

# YAML Quick Reference

| Syntax | Purpose |
|---|---|
| `key: value` | Key-value pair |
| `- item` | List item |
| `# comment` | Comment |
| `true / false` | Boolean values |
| `\|` | Multi-line string |
| Spaces | Define hierarchy |

---

# My Understanding

YAML is designed to be easy for humans to read and write.

Instead of using complex syntax, YAML mainly relies on indentation, key-value pairs, and lists to represent structured data.

In Ansible, understanding YAML syntax is important because Playbooks are written using YAML.

---

# Key Takeaways

- YAML is used to define Ansible Playbooks.
- Indentation is important in YAML.
- Spaces should be used instead of tabs.
- Key-value pairs use `key: value`.
- Lists use `-`.
- Dictionaries contain multiple key-value pairs.
- YAML supports strings, numbers, booleans, comments, and multi-line values.