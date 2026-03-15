# iSeeU

![Version](https://img.shields.io/badge/version-3.0.0-blue)
![Obsidian](https://img.shields.io/badge/Obsidian-1.0+-purple)
![License](https://img.shields.io/badge/license-MIT-green)

iSeeU is an Obsidian plugin designed to help security professionals manage penetration testing notes and scan results. It parses YAML frontmatter from specific note types and provides an organized dashboard to visualize your engagement data.

## Features

- **Note Management**: Specialized templates for hosts, services, vulnerabilities, and credentials.
- **Automated Imports**: Import Nmap XML and Nessus CSV scan results directly into your vault.
- **Unified Dashboard**: View your data through four distinct lenses: IP, Port, Timeline, and Vulnerability.
- **Real-time Updates**: The dashboard automatically refreshes when you modify note frontmatter.
- **Zero Dependencies**: Works standalone without requiring Dataview or other external plugins.
- **Nmap Grepable Import**: Import Nmap grepable scan output (`-oG`) directly into structured notes.
- **Burp Suite Import**: Import Burp Suite Scanner Issues XML into vulnerability notes.
- **Watched Folder**: Automatically import scan files dropped into a configured vault folder.
- **Timeline Filter**: Filter the Timeline view by date range using from/to date pickers.

## Screenshots

*Screenshots coming soon*

## Installation

### Manual Installation

You need to copy **exactly 3 files** into your Obsidian vault:

| File | What it is |
|------|-----------|
| `main.js` | The compiled plugin code |
| `manifest.json` | Plugin metadata (name, version, ID) |
| `styles.css` | Dashboard UI styles |

**Step-by-step:**

1. Find your Obsidian vault folder on disk (e.g. `/Users/you/Documents/MyVault/`).
2. Inside it, create this folder path (the `.obsidian` folder may already exist but be hidden):
   ```
    YourVault/.obsidian/plugins/iseeu/
   ```
3. Copy the 3 files (`main.js`, `manifest.json`, `styles.css`) into that folder. Your result should look like:
   ```
    YourVault/
    └── .obsidian/
        └── plugins/
            └── iseeu/
                ├── main.js
                ├── manifest.json
                └── styles.css
   ```
4. Open Obsidian → **Settings** → **Community plugins**.
5. Turn off **Safe mode** if it is on.
6. Click **Reload plugins** (or fully restart Obsidian).
7. Find **iSeeU** in the list and toggle it **ON**.

> **Tip**: On macOS, `.obsidian` is a hidden folder. Press `Cmd+Shift+.` in Finder to reveal hidden files.

## Usage

You can open the iSeeU dashboard in two ways:
- Click the **shield icon** in the left ribbon.
- Use the **Command Palette** (`Ctrl/Cmd + P`) and search for "Open iSeeU".

## Note Types

The plugin tracks four types of notes based on the `type` field in their YAML frontmatter.

| Note Type | Required Fields | Optional Fields |
| --- | --- | --- |
| **Host** | `type: host`, `ip` | `hostname`, `os`, `status` |
| **Service** | `type: service`, `ip`, `port` | `protocol`, `service`, `version`, `status` |
| **Vulnerability** | `type: vuln`, `ip`, `severity` | `port`, `service`, `found`, `status`, `tags` |
| **Credential** | `type: credential`, `ip`, `username` | `domain`, `found` |

### Severity Levels
Vulnerability severity must be one of: `critical`, `high`, `medium`, `low`, or `info`.

## Dashboard Views

The dashboard features four tabs to help you navigate your data:

- **IP View**: Groups all services, vulnerabilities, and credentials by their host IP address.
- **Port View**: Lists all discovered ports and the services running on them.
- **Timeline View**: Displays vulnerabilities and credentials chronologically based on the `found` field.
- **Vuln View**: Aggregates all vulnerabilities, grouped by severity level.

## Importing Scans

Pentest Viewer supports importing results from common scanning tools.

### Nmap XML
1. Run your Nmap scan and save the output as XML: `nmap -oX scan.xml <target>`.
2. Use the **Import Nmap Scan** command in Obsidian.
3. Select your `.xml` file.
4. The plugin will create **Host** and **Service** notes in a `Pentest/` folder.

### Nessus CSV
1. Export your Nessus scan results as a CSV file.
2. Use the **Import Nessus Scan** command in Obsidian.
3. Select your `.csv` file.
4. The plugin will create **Vulnerability** notes for each finding in a `Pentest/` folder.

### Nmap Grepable
1. Run your Nmap scan with grepable output: `nmap -oG scan.gnmap <target>`.
2. Use the **Import Nmap Grepable Scan** command in Obsidian.
3. Select your `.gnmap` file.
4. The plugin will create **Host** and **Service** notes (open ports only) in a `Pentest/` folder.

### Burp Suite XML
1. In Burp Suite, go to **Scanner > Issues** and export as XML.
2. Use the **Import Burp Suite Scan** command in Obsidian.
3. Select your `.xml` file.
4. The plugin will create **Vulnerability** notes for each issue in a `Pentest/` folder.

### Watched Folder (Auto-Import)
1. In the plugin **Settings**, set a **Watched Folder** path (relative to vault root).
2. Any `.xml`, `.gnmap`, or `.csv` file created in that folder will be automatically imported.
3. Nmap XML (`.xml`) is attempted first; if that yields no results, Burp Suite XML is tried.
4. Nessus CSV (`.csv`) files are imported automatically on creation.

## Generating Test Data

Use the following commands to generate scan outputs for import. Save the files and use **iSeeU → Import Files** to ingest them.

### nmap
```bash
sudo nmap -sV -sC -oX nmap-output.xml <target>
```

### Masscan
```bash
sudo masscan <target-cidr> -p1-65535 --rate=500 -oJ masscan-output.json
```

### Nuclei
```bash
nuclei -u http://<target> -t http/technologies/ -t network/ -jsonl -o nuclei-output.jsonl
# Broader scan:
nuclei -u http://<target> -t http/ -severity critical,high,medium -jsonl -o nuclei-output.jsonl
```

### WhatWeb
```bash
whatweb -a3 http://<target> --log-json=whatweb-output.json
```

### ffuf (directory fuzzing)
```bash
ffuf -u http://<target>/FUZZ -w /path/to/wordlist.txt -o ffuf-output.json -of json -mc 200,301,302
```

### dirsearch (directory enumeration)
```bash
dirsearch -u http://<target> -o dirsearch-output.json --format=json
```

> **Note**: ffuf and dirsearch outputs are not currently imported by iSeeU but can be used for manual review.

## Commands

| Command | Description |
| --- | --- |
| `Open Pentest Viewer in sidebar` | Opens the dashboard in the right sidebar. |
| `Open Pentest Viewer in main panel` | Opens the dashboard as a main workspace tab. |
| `Create Host Note` | Generates a new host note from a template. |
| `Create Service Note` | Generates a new service note from a template. |
| `Create Vulnerability Note` | Generates a new vulnerability note from a template. |
| `Create Credential Note` | Generates a new credential note from a template. |
| `Import Nmap Scan` | Opens a file picker to import Nmap XML results. |
| `Import Nessus Scan` | Opens a file picker to import Nessus CSV results. |
| `Import Nmap Grepable Scan` | Opens a file picker to import Nmap grepable (`-oG`) results. |
| `Import Burp Suite Scan` | Opens a file picker to import Burp Suite Scanner Issues XML. |

## Limitations

- **Settings UI**: A settings panel is available to configure the Watched Folder path.
- **No Search/Filter**: Use Obsidian's native search or the dashboard tabs to find data.
- **No Report Export**: The plugin is for viewing and managing data within Obsidian.
- **Standalone**: Does not integrate with Dataview or other third-party plugins.
- **Plain DOM**: Built using Obsidian's native `createEl` API without React or Svelte.
- **Nmap Format**: Only Nmap XML (-oX) and Nmap grepable (-oG) formats are supported. Nmap normal text (-oN) is not supported.

## Development

To build the plugin from source:

1. Clone the repository.
2. Install dependencies:
   ```bash
   npm install
   ```
3. Build the plugin:
   ```bash
   node esbuild.config.mjs production
   ```

## License

This project is licensed under the MIT License.
