# iSeeU

![Version](https://img.shields.io/badge/version-3.1.0-blue)
![Obsidian](https://img.shields.io/badge/Obsidian-1.0+-purple)
![License](https://img.shields.io/badge/license-MIT-green)

iSeeU is an Obsidian plugin designed to help security professionals manage penetration testing notes and scan results. It parses YAML frontmatter from specific note types and provides an organized dashboard to visualize your engagement data.

## Features

- **Note Management**: Specialized templates for hosts, services, vulnerabilities, and credentials.
- **Automated Imports**: Import results from 11 scanner formats directly into your vault.
- **Unified Dashboard**: View your data through four distinct lenses: IP, Port, Timeline, and Vulnerability.
- **Real-time Updates**: The dashboard automatically refreshes when you modify note frontmatter.
- **Zero Dependencies**: Works standalone without requiring Dataview or other external plugins.
- **Nmap Import**: Supports all three Nmap output formats: XML (`-oX`), Grepable (`-oG`), and Normal Text (`-oN`).
- **Masscan Import**: Import Masscan results in XML or JSON format.
- **OpenVAS / Greenbone Import**: Import OpenVAS/Greenbone vulnerability reports (XML).
- **Nuclei Import**: Import Nuclei JSONL scan results as vulnerability notes.
- **WhatWeb Import**: Import WhatWeb JSON technology fingerprinting results as service notes.
- **Nikto Import**: Import Nikto text scan output as vulnerability notes.
- **Burp Suite Import**: Import Burp Suite Scanner Issues XML into vulnerability notes.
- **iSeeU Custom Scanner**: Import output from your own nc+curl scanner tool (JSONL format).
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

iSeeU supports importing results from 11 scanning tools. Use the **Import scan files...** command to open a file picker (supports multi-select). The format is auto-detected from file content — no need to choose the format manually.

### Nmap XML
1. Run your Nmap scan and save the output as XML: `nmap -oX scan.xml <target>`.
2. Use the **Import scan files...** command in Obsidian.
3. Select your `.xml` file.
4. The plugin will create **Host** and **Service** notes in a `Pentest/` folder.

### Nmap Grepable
1. Run your Nmap scan with grepable output: `nmap -oG scan.gnmap <target>`.
2. Use the **Import scan files...** command in Obsidian.
3. Select your `.gnmap` file.
4. The plugin will create **Host** and **Service** notes (open ports only) in a `Pentest/` folder.

### Nmap Normal Text
1. Run your Nmap scan with normal text output: `nmap -oN scan.nmap <target>`.
2. Use the **Import scan files...** command in Obsidian.
3. Select your `.nmap` or `.txt` file.
4. The plugin creates **Host** and **Service** notes (open ports only). NSE script output is appended to each service note.

### Nessus CSV
1. Export your Nessus scan results as a CSV file.
2. Use the **Import scan files...** command in Obsidian.
3. Select your `.csv` file.
4. The plugin will create **Vulnerability** notes for each finding in a `Pentest/` folder.

### Burp Suite XML
1. In Burp Suite, go to **Scanner > Issues** and export as XML.
2. Use the **Import scan files...** command in Obsidian.
3. Select your `.xml` file.
4. The plugin will create **Vulnerability** notes for each issue in a `Pentest/` folder.

### Masscan
1. Run Masscan and save output as XML or JSON:
   - XML: `masscan <target> -p1-65535 --rate=500 -oX masscan.xml`
   - JSON: `masscan <target> -p1-65535 --rate=500 -oJ masscan.json`
2. Use the **Import scan files...** command in Obsidian.
3. Select your `.xml` or `.json` file.
4. The plugin creates **Host** and **Service** notes. Port numbers only — Masscan does not perform banner grabbing.

### OpenVAS / Greenbone
1. In Greenbone Security Manager (GSM), export your report as XML.
2. Use the **Import scan files...** command in Obsidian.
3. Select your `.xml` file.
4. The plugin creates **Vulnerability** notes with severity mapped from CVSS scores.

### Nuclei
1. Run Nuclei with JSONL output: `nuclei -u http://<target> -jsonl -o nuclei.jsonl`.
2. Use the **Import scan files...** command in Obsidian.
3. Select your `.jsonl` file.
4. The plugin creates **Vulnerability** notes with severity from the template metadata.

### WhatWeb
1. Run WhatWeb with JSON logging: `whatweb -a3 http://<target> --log-json=whatweb.json`.
2. Use the **Import scan files...** command in Obsidian.
3. Select your `.json` file.
4. The plugin creates **Service** notes listing detected technologies (CMS, frameworks, server info).

### Nikto
1. Run Nikto and save text output: `nikto -h http://<target> -o nikto.txt`.
2. Use the **Import scan files...** command in Obsidian.
3. Select your `.txt` file.
4. The plugin creates **Vulnerability** notes (info severity) for each finding.

### iSeeU Custom Scanner

Build your own lightweight scanner using `nc` and `curl`. Output one JSON object per line (JSONL format) with the `"scanner":"iseeu"` field — the plugin auto-detects it.

**Record format** — one line per open port:

```json
{"scanner":"iseeu","ip":"192.168.1.10","port":80,"protocol":"tcp","state":"open","service":"http","version":"Apache 2.4.29","banner":"","http_status":200,"http_title":"My App","found":"2026-03-16 10:30"}
```

**Field reference:**

| Field | Type | Required | Description |
|---|---|---|---|
| `scanner` | string | **YES** | Always `"iseeu"` — used for auto-detection |
| `ip` | string | **YES** | Target IP address |
| `port` | number | **YES** | Port number |
| `protocol` | string | yes | `"tcp"` or `"udp"` (default: `"tcp"`) |
| `state` | string | yes | `"open"` — non-open records are skipped |
| `service` | string | no | Service name (http, ssh, ftp...) |
| `version` | string | no | Version string |
| `banner` | string | no | Raw banner text from nc |
| `http_status` | number | no | HTTP status code from curl (0 if not HTTP) |
| `http_title` | string | no | Page `<title>` from curl |
| `found` | string | no | Timestamp `YYYY-MM-DD HH:MM` |

**Shell script template:**

```bash
#!/bin/bash
# iseeu-scan.sh — nc + curl scanner → iSeeU custom JSONL
TARGET=$1
PORTS="${2:-22,80,443,8080,8443}"
OUTFILE="iseeu-scan-$(date +%Y%m%d-%H%M%S).json"
FOUND=$(date +"%Y-%m-%d %H:%M")

for PORT in $(echo $PORTS | tr ',' ' '); do
  echo "" | nc -z -w 2 "$TARGET" "$PORT" 2>/dev/null || continue
  BANNER=$(echo "" | nc -w 2 "$TARGET" "$PORT" 2>/dev/null | head -1 | tr -d '\r\n')
  HTTP_STATUS=0; HTTP_TITLE=""
  SCHEME="http"; [ "$PORT" = "443" ] || [ "$PORT" = "8443" ] && SCHEME="https"
  if [[ "$PORT" =~ ^(80|443|8080|8443|8000|3000)$ ]]; then
    HTTP_STATUS=$(curl -sk -o /dev/null -w "%{http_code}" --max-time 5 "${SCHEME}://${TARGET}:${PORT}/")
    HTTP_TITLE=$(curl -sk --max-time 5 "${SCHEME}://${TARGET}:${PORT}/" \
      | grep -i '<title>' | sed 's/.*<title>//I;s/<\/title>.*//I' | head -1 | tr -d '\r\n')
  fi
  printf '{"scanner":"iseeu","ip":"%s","port":%s,"protocol":"tcp","state":"open","service":"","version":"","banner":"%s","http_status":%s,"http_title":"%s","found":"%s"}\n' \
    "$TARGET" "$PORT" "$BANNER" "${HTTP_STATUS:-0}" "$HTTP_TITLE" "$FOUND" >> "$OUTFILE"
done
echo "Saved to $OUTFILE — drop it in your iSeeU watched folder or use Import scan files..."
```

Usage: `bash iseeu-scan.sh 192.168.1.10 22,80,443,8080`

The plugin creates **Host** and **Service** notes. Service notes include the raw nc banner and HTTP status/title when available.

### Watched Folder (Auto-Import)
1. In the plugin **Settings**, set a **Watched Folder** path (relative to vault root).
2. Any `.xml`, `.gnmap`, `.csv`, `.json`, `.jsonl`, `.nmap`, or `.txt` file created in that folder will be automatically imported.
3. The format is auto-detected from file content. XML files are checked for Nmap, Masscan, Burp Suite, and OpenVAS signatures.
4. Nessus CSV (`.csv`) files are imported automatically on creation.

## Generating Test Data

Use the following commands to generate scan outputs for import. Save the files and use **Import scan files...** to ingest them.

### Nmap
```bash
# XML output
sudo nmap -sV -sC -oX nmap-output.xml <target>
# Normal text output
sudo nmap -sV -sC -oN nmap-output.nmap <target>
# Grepable output
sudo nmap -sV -sC -oG nmap-output.gnmap <target>
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

### Nikto
```bash
nikto -h http://<target> -o nikto-output.txt
```

### iSeeU Custom Scanner
```bash
bash iseeu-scan.sh <target> 22,80,443,8080,8443
```

### ffuf / dirsearch (directory fuzzing)
```bash
ffuf -u http://<target>/FUZZ -w /path/to/wordlist.txt -o ffuf-output.json -of json -mc 200,301,302
dirsearch -u http://<target> -o dirsearch-output.json --format=json
```

> **Note**: ffuf and dirsearch outputs are **not** imported by iSeeU. Use them for manual review only.

## Commands

| Command | Description |
| --- | --- |
| `Open iSeeU in sidebar` | Opens the dashboard in the right sidebar. |
| `Open iSeeU in main panel` | Opens the dashboard as a main workspace tab. |
| `Create Host Note` | Generates a new host note from a template. |
| `Create Service Note` | Generates a new service note from a template. |
| `Create Vulnerability Note` | Generates a new vulnerability note from a template. |
| `Create Credential Note` | Generates a new credential note from a template. |
| `Import scan files...` | Opens a file picker (supports multi-select). Auto-detects format from content. Supports: Nmap XML, Nmap Grepable, Nmap Normal Text, Masscan XML/JSON, Burp Suite XML, OpenVAS XML, Nessus CSV, Nuclei JSONL, WhatWeb JSON, Nikto text, iSeeU Custom JSONL. |
| `Import Folder (Recursive)` | Recursively imports all importable scan files from a selected vault folder. |

## Limitations

- **Settings UI**: A settings panel is available to configure the Watched Folder path.
- **No Search/Filter**: Use Obsidian's native search or the dashboard tabs to find data.
- **No Report Export**: The plugin is for viewing and managing data within Obsidian.
- **Standalone**: Does not integrate with Dataview or other third-party plugins.
- **Plain DOM**: Built using Obsidian's native `createEl` API without React or Svelte.
- **Supported Formats**: Nmap XML (`-oX`), Nmap Grepable (`-oG`), Nmap Normal Text (`-oN`), Masscan (XML/JSON), Burp Suite XML, OpenVAS/Greenbone XML, Nessus CSV, Nuclei JSONL, WhatWeb JSON, Nikto text, iSeeU Custom JSONL.

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
