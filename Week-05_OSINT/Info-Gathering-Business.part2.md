# Information Gathering – Business (Part 2)

> **Focus:** Advanced techniques for business intelligence gathering using Google Hacking, metadata analysis, and automated tools.

---

## Table of Contents

1. [Introduction to Google Hacking](#1-introduction-to-google-hacking)
2. [Google Hacking Techniques](#2-google-hacking-techniques)
3. [Google Hacking Applied to Pentesting](#3-google-hacking-applied-to-pentesting)
4. [Google Dorks / GHDB / Scripts](#4-google-dorks--ghdb--scripts)
5. [Bing Hacking](#5-bing-hacking)
6. [Non-Delivery Notification](#6-non-delivery-notification)
7. [TheHarvester](#7-theharvester)
8. [Metadata Collection](#8-metadata-collection)
9. [Analyzing Metadata](#9-analyzing-metadata)
10. [Creating a Metadata Analysis Script](#10-creating-a-metadata-analysis-script)

---

## 1 – Introduction to Google Hacking

Google Hacking is called this way because instead of using Google as we normally do for "regular" searches, we use it to find more precise information using its operators.

### Google Search Operators

| Operator | Description |
|----------|-------------|
| `site:` | Search within a specific site |
| `inurl:` | Search in the URL |
| `intitle:` | Search in the title |
| `intext:` | Search in the text |
| `filetype:` | Search by file type |
| `ext:` | Search by file extension |
| `cache:` | Search in cache |
| `""` | Exact search |
| `-` | Exclude from search |

### Examples

```
site:businesscorp.com.br -www ext:php
site:businesscorp.com.br intitle:"Admin"
site:businesscorp.com.br "index of" backup
```

---

## 2 – Google Hacking Techniques

### Types of Information We Can Search For

**Files and Extensions:**
- php, asp, aspx, do, js, phps, txt, doc, docx, pdf, xls, xlsx, ppt, ovpn, sql, bak, old

**Titles:**
- "index of", "login", "restricted access", "admin", "adm"

**Keywords:**
- "config", "password", "passwords", "users", "access", "ftp", "bkp", "backup", "data"

### Google Cache

In some cases, for example, we use the search `filetype:txt inurl:password` and find a file on Google named "Admin Password" as a txt file. However, when you try to access the site to view the information, it returns a 404 Not Found, meaning the file no longer exists. But since it was indexed by Google at some point, using the link you found with cache:

```
cache:link
```

You can find information that previously existed but no longer exists, remembering that the Wayback Machine can also be used as mentioned before.

---

## 3 – Google Hacking Applied to Pentesting

Basically, this section is to remind you that the focus of Google Hacking is primarily to filter the largest number of results possible to bring only what is necessary for you. It's worth noting that using `site:` can be a great help in finding specific things from the site you're working on. In any case, this technique should be taken into consideration, as vulnerabilities of this type, even though they may seem "silly," still exist and should be considered.

---

## 4 – Google Dorks / GHDB / Scripts

### What is a Dork?

A Dork means the combination of Google operators to search for specific things.

### What is GHDB?

It's the Google Hacking Database, a collection of Google Dorks to search for vulnerabilities more easily.

### Creating an Automation Script

```bash
#!/bin/bash
SEARCH="firefox"
TARGET="$1"

echo "Pastebin Search"
$SEARCH "https://google.com/search?q=site:pastebin.com+$TARGET" 2> /dev/null

echo "Trello Search"
$SEARCH "https://google.com/search?q=site:trello.com+$TARGET" 2> /dev/null

echo "File Search"
$SEARCH "https://google.com/search?q=site:$TARGET+ext:php+OR+ext:asp+OR+ext:txt" 2> /dev/null
```

### Important Reminders:

1. Obviously, this is a very basic script and was created only to show that it's possible to automate the use of Google Dorks
2. Even though it's possible to automate these searches, it's still interesting to perform them manually to avoid missing something

---

## 5 – Bing Hacking

Even knowing that Google and GHDB exist, Google is not the only search engine that exists. Among them, there are several, but we'll be talking about Bing here.

Bing uses the same operators as Google, but it comes with a novelty: the `ip:` operator, which allows you to see which domain is associated with the IP you entered.

This way, we can also do something interesting: on websites that use hosting providers and not dedicated ones, by entering an IP, we can see all websites hosted with that IP, demonstrating that several websites share the same network.

This is good because if a server has difficult access, we can use another website as an attack vector for the one we're trying to attack.

---

## 6 – Non-Delivery Notification

NDN can be exploited if enabled on some email service as follows:

When we send a non-existent email to the target with any content, if NDN (Non-Delivery Notification) is enabled, the server will possibly return a DEBUG message informing the error it encountered. And in this DEBUG message, some sensitive information such as IPs, versions, etc., may be returned.

---

## 7 – TheHarvester

A tool used to automate the action of searching for information, being possible to use various search means like LinkedIn, Github, Virustotal, Google, etc.

To install it, just run:

```bash
apt install theharvester
```

> **Personal Opinion (Ricardo Longatto):**
> "I particularly don't like using these tools because I don't know how they do these searches. They can give false positives or not return something we could have found manually. I personally like to do everything manually or use tools I made myself."

---

## 8 – Metadata Collection

### Process:

1. Find company documents on the internet (Google hacking / dorks)
2. Download the documents (doc, docx, xls, xlsx, ppt, pptx, pdf)
3. Analyze the metadata

### What to Look For:

- Usernames
- Software versions
- Operating systems

### Example:

Let's suppose we take a file and when analyzing the metadata we had the following results:

```
XMP Toolkit: Adobe XMP Core 5.4-c005 78.1473225
Metadata Date: 2015:05:08 11:15:32-03:00
Creator Tool: Acrobat 11.0.0 <----
Format: application/pdf
```

In this case, when we see the "Creator Tool," we can see the version of the software that made that file. Knowing this, we can abuse this information to send a file to the victim with an exploit in our file that we know works because they use the software version that the exploit expects.

---

## 9 – Analyzing Metadata

Now I'll show how to analyze metadata. First, let's install the exiftool:

```bash
apt install exiftool
```

After installation, we can look at the manual to see what we can do:

```bash
man exiftool
```

### How to Use:

```bash
exiftool "filename"
```

---

## 10 – Creating a Metadata Analysis Script

We'll make a tool that does automatic installation and reading of metadata.

First, we need to install Lynx (basically a command-line browser):

```bash
apt install lynx
```

### Automated Metadata Analysis Script

```bash
#!/bin/bash

# Metadata Analysis Script
# Usage: ./metadata_analyzer.sh <website_url> <file_extension>

if [ $# -ne 2 ]; then
    echo "Usage: $0 <website_url> <file_extension>"
    echo "Example: $0 https://example.com pdf"
    exit 1
fi

WEBSITE="$1"
FILETYPE="$2"
OUTPUT_DIR="metadata_analysis"
TEMP_DIR="/tmp/metadata_temp"

# Create directories
mkdir -p "$OUTPUT_DIR"
mkdir -p "$TEMP_DIR"

echo "[+] Starting metadata analysis for $WEBSITE"
echo "[+] Looking for $FILETYPE files..."

# Use lynx to dump website content and grep for file links
echo "[+] Extracting file URLs..."
lynx -dump -listonly "$WEBSITE" | grep -i "\.$FILETYPE" > "$TEMP_DIR/file_urls.txt"

# Download files found
echo "[+] Downloading files..."
while IFS= read -r url; do
    if [[ $url == http* ]]; then
        filename=$(basename "$url")
        echo "  [+] Downloading: $filename"
        wget -q "$url" -P "$TEMP_DIR/" 2>/dev/null
        
        # Analyze metadata if file was downloaded successfully
        if [ -f "$TEMP_DIR/$filename" ]; then
            echo "  [+] Analyzing metadata for: $filename"
            exiftool "$TEMP_DIR/$filename" > "$OUTPUT_DIR/${filename}_metadata.txt"
            echo "    [+] Metadata saved to: $OUTPUT_DIR/${filename}_metadata.txt"
        fi
    fi
done < "$TEMP_DIR/file_urls.txt"

# Create summary report
echo "[+] Creating summary report..."
cat "$OUTPUT_DIR"/*_metadata.txt | grep -i "creator\|author\|software\|application\|producer" > "$OUTPUT_DIR/summary_report.txt"

echo "[+] Analysis complete!"
echo "[+] Results saved in: $OUTPUT_DIR/"
echo "[+] Summary report: $OUTPUT_DIR/summary_report.txt"

# Cleanup
rm -rf "$TEMP_DIR"
```

### Script Features:

- **Automated Discovery:** Uses Lynx to find files of specified type
- **Batch Download:** Downloads all found files automatically
- **Metadata Extraction:** Uses exiftool to extract metadata from each file
- **Summary Report:** Creates a consolidated report of interesting metadata
- **Cleanup:** Removes temporary files after analysis

### Usage Example:

```bash
./metadata_analyzer.sh https://target-company.com pdf
./metadata_analyzer.sh https://target-company.com docx
```

This script provides an automated approach to metadata collection and analysis, making the reconnaissance process more efficient while maintaining thoroughness.

---
