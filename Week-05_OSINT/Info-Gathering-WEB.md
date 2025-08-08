# Information Gathering – Web

> **Focus:** Web reconnaissance techniques for identifying technologies, directories, files, and potential vulnerabilities through comprehensive website analysis and enumeration.

---

## Table of Contents

1. [Introduction - Web Reconnaissance](#1-introduction---web-reconnaissance)
2. [Robots.txt and Sitemap Analysis](#2-robotstxt-and-sitemap-analysis)
3. [Directory Listing Analysis](#3-directory-listing-analysis)
4. [Website Mirroring](#4-website-mirroring)
5. [Error Analysis, Status Codes and Extensions](#5-error-analysis-status-codes-and-extensions)
6. [HTTP Request Research](#6-http-request-research)
7. [Brute Force - Files and Directories](#7-brute-force---files-and-directories)
8. [Understanding Program Logic](#8-understanding-program-logic)
9. [Working with Curl](#9-working-with-curl)
10. [Building a Web Reconnaissance Script](#10-building-a-web-reconnaissance-script)
11. [WhatWeb Analysis](#11-whatweb-analysis)
12. [Wappalyzer Technology Detection](#12-wappalyzer-technology-detection)
13. [Script for Identifying Pages on the Internet](#13-script-for-identifying-pages-on-the-internet)

---

## 1 – Introduction - Web Reconnaissance

Web reconnaissance is a crucial phase in penetration testing that involves comprehensive analysis of web applications and their underlying infrastructure.

### Core Web Reconnaissance Tasks:

- **Web Server Identification** - Determine the web server software and version
- **Technology Stack Detection** - Identify frameworks, languages, and libraries
- **Directory Discovery** - Find hidden directories and endpoints
- **File Enumeration** - Locate sensitive files and configurations
- **Security Control Analysis** - Identify firewalls, WAFs, and blocking mechanisms
- **HTTP Method Testing** - Determine allowed HTTP methods
- **Directory Listing Detection** - Find exposed directory structures
- **Page Structure Analysis** - Understand application architecture
- **Source Code Analysis** - Review client-side code for vulnerabilities
- **JavaScript File Analysis** - Examine scripts for sensitive information

### Web Reconnaissance Techniques:

- **HTML/JS Parsing** - Extract information from markup and scripts
- **Website Mirroring** - Clone entire sites for offline analysis
- **Robots.txt/Sitemap.xml** - Discover intended hidden paths
- **Directory/File Brute Force** - Systematically enumerate resources
- **HTTP Response Analysis** - Analyze status codes and headers
- **User-Agent Bypass** - Evade basic filtering mechanisms
- **Proxy Tools (Burp Suite)** - Intercept and modify requests
- **Technology Fingerprinting** - Use WhatWeb and Wappalyzer
- **Extension Analysis** - Identify file types and technologies
- **Error and Banner Analysis** - Extract version and configuration data

### Website Mapping Framework:

**Authentication Systems:**
- Login mechanisms and security controls
- Registration processes and validation

**Functionality Assessment:**
- Search capabilities and input handling
- Content management (posts, comments)
- File operations (upload/download)
- Data submission forms

**Infrastructure Analysis:**
- Database interactions and error messages
- Redirection patterns and URL structures
- Error handling and information disclosure

---

## 2 – Robots.txt and Sitemap Analysis

### Understanding Robots.txt

**Robots.txt** is a standard file that websites use to communicate with web crawlers and search engines about which parts of their site should not be crawled or indexed.

**Key Characteristics:**
- Located at the root directory: `domain.com/robots.txt`
- Contains directives for web crawlers (User-agent, Disallow, Allow)
- Often reveals sensitive directories that administrators want to hide
- Not a security mechanism - purely advisory for legitimate crawlers

**Penetration Testing Value:**
- **Directory Discovery:** Reveals paths administrators consider sensitive
- **Hidden Resources:** Shows areas not intended for public access
- **Infrastructure Mapping:** Provides insight into site structure
- **Administrative Priorities:** Indicates what owners want to protect

### Sitemap.xml Analysis

**Sitemap.xml** is a file that helps search engines understand the structure and content of a website.

**Intelligence Gathering:**
- **Complete URL Listing:** All pages the site wants indexed
- **Content Organization:** How the site is structured
- **Update Frequencies:** How often content changes
- **Priority Mapping:** Which pages are considered most important

### Practical Application

```bash
# Check robots.txt
curl http://target.com/robots.txt

# Check sitemap
curl http://target.com/sitemap.xml
curl http://target.com/sitemap_index.xml
```

Both files provide valuable reconnaissance data without requiring aggressive scanning techniques.

---

## 3 – Directory Listing Analysis

### Understanding Directory Listing

**Directory listing** occurs when a web server displays the contents of a directory instead of serving a default index file (like index.html).

### How Directory Listing Works

**Normal Behavior:**
- Web server looks for default files (index.html, index.php, default.aspx)
- If found, serves the default file
- If not found, either shows an error or lists directory contents

**Configuration Issues:**
- **Apache:** `Options +Indexes` enables directory listing
- **Nginx:** `autoindex on` enables directory listing
- **IIS:** Directory browsing enabled in configuration

### Why Directory Listing is Valuable

**Information Disclosure:**
- **File Structure:** Complete view of directory organization
- **Sensitive Files:** Configuration files, backups, logs
- **Hidden Resources:** Files not linked from the main site
- **Development Artifacts:** Test files, documentation, code

**Security Implications:**
- Exposes files that should remain private
- Reveals internal application structure
- May contain backup files with sensitive data
- Shows temporary or development files

### Detection Methods

```bash
# Manual check
curl http://target.com/uploads/
curl http://target.com/admin/
curl http://target.com/backup/

# Look for "Index of" in response
curl -s http://target.com/directory/ | grep -i "index of"
```

Directory listing vulnerabilities often provide direct access to sensitive information without requiring complex exploitation techniques.

---

## 4 – Website Mirroring

### Understanding Website Mirroring

**Website mirroring** involves creating a complete local copy of a website for offline analysis, allowing comprehensive examination of structure, content, and potential vulnerabilities.

### Using wget for Mirroring

**Basic Mirroring Command:**
```bash
wget -m http://target.com
```

**Advanced Mirroring Options:**
```bash
# Complete mirror with all files
wget -m -p -E -k -K -np http://target.com

# Options explanation:
# -m : mirror (recursive with infinite depth)
# -p : download all page requisites (images, CSS, JS)
# -E : adjust extension (save .html for dynamic pages)
# -k : convert links for local viewing
# -K : backup original files
# -np : don't ascend to parent directories
```

### Bypassing Robots.txt Restrictions

When websites block mirroring through robots.txt:

```bash
# Ignore robots.txt restrictions
wget -m -e robots=off http://target.com
```

**Why This Works:**
- wget normally respects robots.txt directives
- The `-e robots=off` flag disables this behavior
- Allows access to directories marked as "Disallow" in robots.txt

### Applications for Mirrored Sites

**Penetration Testing:**
- **Offline Analysis:** Examine code without generating additional traffic
- **Structure Understanding:** Map complete application architecture
- **Sensitive File Discovery:** Find files not linked from main navigation

**Social Engineering:**
- **Phishing Infrastructure:** Create convincing replicas for attacks
- **Content Analysis:** Study branding and communication patterns
- **Credential Harvesting:** Build authentic-looking login pages

### Best Practices

```bash
# Limit bandwidth to avoid detection
wget -m --limit-rate=200k http://target.com

# Add delays between requests
wget -m --wait=2 http://target.com

# Use custom user-agent
wget -m --user-agent="Mozilla/5.0 (compatible; crawler)" http://target.com
```

Website mirroring provides comprehensive access to all publicly available resources while maintaining stealth and enabling detailed offline analysis.

---

## 5 – Error Analysis, Status Codes and Extensions

### Importance of Error Analysis

Error messages and HTTP status codes provide valuable intelligence about the underlying technology stack, configuration issues, and potential attack vectors.

### Types of Valuable Errors

**Application Errors:**
- **Database Errors:** Reveal database type, structure, and query information
- **Path Disclosure:** Show internal server paths and directory structures
- **Stack Traces:** Expose programming language, frameworks, and code logic
- **Configuration Errors:** Reveal version numbers and security settings

**HTTP Status Code Intelligence:**
- **403 Forbidden:** Resource exists but access is denied
- **404 Not Found:** Resource doesn't exist (distinguish from 403)
- **500 Internal Error:** Server-side issues that may indicate vulnerabilities
- **502/503/504:** Gateway and service errors revealing infrastructure

### Extension Analysis

**Technology Fingerprinting:**
- **.php:** PHP-based applications
- **.aspx/.asp:** Microsoft ASP.NET/Classic ASP
- **.jsp:** Java Server Pages
- **.py:** Python web applications
- **.rb:** Ruby applications

**File Type Intelligence:**
- **.bak/.backup:** Backup files with sensitive data
- **.conf/.config:** Configuration files
- **.log:** Log files with system information
- **.txt:** Documentation or sensitive text files

### Practical Examples

**Database Error Example:**
```
MySQL Error: Table 'users' doesn't exist in database 'webapp_prod'
```
**Intelligence Value:** Database type (MySQL), table structure, database name

**Path Disclosure Example:**
```
Fatal error in /var/www/html/admin/config.php on line 23
```
**Intelligence Value:** Server path structure, PHP technology, admin directory

**Stack Trace Analysis:**
```
java.lang.NullPointerException at com.company.webapp.UserController.login()
```
**Intelligence Value:** Java technology, application structure, method names

### Systematic Error Discovery

```bash
# Test for different file extensions
curl http://target.com/admin.php
curl http://target.com/admin.aspx
curl http://target.com/admin.jsp

# Test for backup files
curl http://target.com/config.php.bak
curl http://target.com/web.config.old

# Force errors with invalid input
curl http://target.com/search.php?id=invalid
curl http://target.com/login.asp?user=test'
```

Error analysis often provides the most direct path to understanding application internals and identifying potential vulnerabilities.

---

## 6 – HTTP Request Research

### Using Netcat for HTTP Analysis

**Netcat** provides low-level HTTP communication capabilities, allowing manual construction of requests and detailed analysis of server responses.

### HTTP Method Enumeration

**OPTIONS Method:**
```bash
# Discover allowed HTTP methods
echo -e "OPTIONS / HTTP/1.1\r\nHost: target.com\r\n\r\n" | nc target.com 80
```

**HEAD Method:**
```bash
# Get headers without body content
echo -e "HEAD / HTTP/1.1\r\nHost: target.com\r\n\r\n" | nc target.com 80
```

**Custom Method Testing:**
```bash
# Test for dangerous methods
echo -e "PUT /test.txt HTTP/1.1\r\nHost: target.com\r\n\r\n" | nc target.com 80
echo -e "DELETE /admin HTTP/1.1\r\nHost: target.com\r\n\r\n" | nc target.com 80
```

### Banner Grabbing and Version Detection

**Web Server Identification:**
```bash
# Basic banner grab
echo -e "HEAD / HTTP/1.1\r\nHost: target.com\r\n\r\n" | nc target.com 80

# Look for Server header:
# Server: Apache/2.4.41 (Ubuntu)
# Server: nginx/1.18.0
# Server: Microsoft-IIS/10.0
```

### Virtual Host Enumeration

When multiple applications run on the same server, the Host header becomes crucial:

**Without Host Header:**
```bash
echo -e "GET / HTTP/1.1\r\n\r\n" | nc 192.168.1.100 80
# May return default site or error
```

**With Specific Host:**
```bash
echo -e "GET / HTTP/1.1\r\nHost: admin.target.com\r\n\r\n" | nc 192.168.1.100 80
# Returns content for specific virtual host
```

### Advanced HTTP Analysis

**Security Header Detection:**
```bash
# Check for security headers
echo -e "GET / HTTP/1.1\r\nHost: target.com\r\n\r\n" | nc target.com 80 | grep -i "security\|x-frame\|content-security"
```

**Authentication Method Discovery:**
```bash
# Check authentication requirements
echo -e "GET /admin HTTP/1.1\r\nHost: target.com\r\n\r\n" | nc target.com 80
# Look for WWW-Authenticate header
```

### Practical Intelligence Gathering

**Response Analysis:**
- **Status Codes:** Understand access controls and resource availability
- **Headers:** Identify technologies, security measures, and configurations
- **Content-Type:** Determine response formats and supported media types
- **Set-Cookie:** Analyze session management and tracking mechanisms

This manual approach provides granular control over requests and detailed insight into server behavior that automated tools might miss.

---

## 7 – Brute Force - Files and Directories

### Understanding Directory Brute Force

**Directory brute force** is a technique that systematically attempts to access common directory and file names to discover hidden resources on web servers.

### Using Dirb for Enumeration

**Basic Dirb Usage:**
```bash
# Standard directory scan
dirb http://target.com

# Custom wordlist
dirb http://target.com /usr/share/wordlists/custom.txt

# Specific file extensions
dirb http://target.com -X .php,.html,.txt,.bak
```

**Advanced Dirb Options:**
```bash
# Silent mode (less output)
dirb http://target.com -S

# Save results to file
dirb http://target.com -o results.txt

# Use custom User-Agent
dirb http://target.com -a "Mozilla/5.0 Custom Agent"

# Ignore specific status codes
dirb http://target.com -N 404,403
```

### Limitations of Automated Tools

**Firewall and WAF Evasion:**
- Modern Web Application Firewalls (WAFs) can detect brute force patterns
- Rate limiting may block or slow down automated scans
- IP-based blocking can prevent completion of scans

**False Positives:**
- Default configurations may redirect all 404s to custom pages
- Dynamic content generation can create misleading responses
- Load balancers may return inconsistent results

### Alternative Approaches

**Manual Verification:**
```bash
# Verify dirb findings manually
curl -I http://target.com/admin/
curl -I http://target.com/backup/
```

**Custom Timing:**
```bash
# Slower scan to avoid detection
dirb http://target.com -z 1000  # 1 second delay
```

**Distributed Scanning:**
```bash
# Split wordlists across multiple sources
dirb http://target.com wordlist_part1.txt
# From different IP: dirb http://target.com wordlist_part2.txt
```

### Best Practices

**Stealth Considerations:**
- Use realistic User-Agent strings
- Implement delays between requests
- Monitor for blocking or rate limiting
- Verify results through multiple methods

**Comprehensive Coverage:**
- Test multiple file extensions
- Try common backup patterns (.bak, .old, .tmp)
- Check for development files (.dev, .test, .staging)
- Look for configuration files (.conf, .config, .ini)

Directory brute force remains effective but requires careful execution to avoid detection and ensure accurate results.

---

## 8 – Understanding Program Logic

### How Directory Brute Force Tools Work

Directory and file brute force tools operate on a simple but effective principle: systematic enumeration with response analysis.

### Core Algorithm Logic

**Step-by-Step Process:**

1. **Wordlist Loading:** Read predefined list of common directory/file names
2. **URL Construction:** Combine base URL with each wordlist entry
3. **HTTP Request:** Send GET/HEAD request to constructed URL
4. **Response Analysis:** Examine HTTP status code and content
5. **Result Classification:** Determine if resource exists based on response

### Response Analysis Logic

**Status Code Interpretation:**
```
200 OK          → Resource exists and is accessible
301/302 Redirect → Resource exists but redirects elsewhere
403 Forbidden   → Resource exists but access is denied
404 Not Found   → Resource does not exist
500 Server Error → Server-side issue (may indicate vulnerability)
```

### Practical Example

**Manual Implementation Logic:**
```bash
#!/bin/bash
# Simple directory brute force logic

BASE_URL="http://target.com"
WORDLIST="common_dirs.txt"

for word in $(cat $WORDLIST); do
    # Construct URL
    url="$BASE_URL/$word/"
    
    # Send request and capture status code
    status=$(curl -s -o /dev/null -w "%{http_code}" "$url")
    
    # Analyze response
    if [ "$status" == "200" ]; then
        echo "Found: $url (Status: $status)"
    elif [ "$status" == "403" ]; then
        echo "Forbidden: $url (Status: $status)"
    elif [ "$status" == "301" ] || [ "$status" == "302" ]; then
        echo "Redirect: $url (Status: $status)"
    fi
done
```

### Advanced Response Analysis

**Content-Based Detection:**
- **Content Length:** Different lengths may indicate different responses
- **Response Time:** Timing differences can reveal processing variations
- **Content Patterns:** Look for specific strings in responses

**False Positive Handling:**
- **Custom 404 Pages:** May return 200 status with "not found" content
- **Wildcard DNS:** Some servers respond to any subdirectory request
- **Load Balancer Behavior:** Different servers may respond differently

### Optimization Strategies

**Efficiency Improvements:**
- **Parallel Requests:** Multiple simultaneous connections
- **HEAD vs GET:** Use HEAD requests to reduce bandwidth
- **Smart Filtering:** Learn from initial responses to adjust detection logic

**Evasion Techniques:**
- **Request Variation:** Randomize User-Agent, headers, timing
- **Proxy Rotation:** Distribute requests across multiple IP addresses
- **Pattern Breaking:** Avoid predictable request sequences

Understanding these fundamentals allows for better tool selection and custom solution development when standard tools prove insufficient.

---

## 9 – Working with Curl

### Curl Command Fundamentals

**Curl** is a versatile command-line tool for transferring data with URLs, essential for web reconnaissance and HTTP analysis.

### Basic Curl Operations

**Simple GET Request:**
```bash
curl http://target.com
curl -i http://target.com  # Include response headers
curl -I http://target.com  # HEAD request only
```

**Save Output:**
```bash
curl -o output.html http://target.com
curl -O http://target.com/file.pdf  # Save with original filename
```

### User-Agent Manipulation

**Why User-Agent Matters:**
- **WAF Evasion:** Many firewalls block suspicious or automated user agents
- **Access Control:** Some sites restrict based on browser type
- **Stealth Operations:** Blend in with legitimate traffic patterns

**Custom User-Agent Examples:**
```bash
# Standard browsers
curl -H "User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36" http://target.com

# Mobile devices
curl -H "User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 14_0 like Mac OS X)" http://target.com

# Search engine bots (often get special treatment)
curl -H "User-Agent: Googlebot/2.1 (+http://www.google.com/bot.html)" http://target.com

# Custom reconnaissance agent
curl -H "User-Agent: DesecTool/1.0" http://target.com
```

### Advanced Curl Features

**HTTP Methods:**
```bash
curl -X POST http://target.com/api
curl -X PUT http://target.com/upload
curl -X DELETE http://target.com/resource
curl -X OPTIONS http://target.com  # Discover allowed methods
```

**Headers and Authentication:**
```bash
# Custom headers
curl -H "Authorization: Bearer token123" http://target.com/api
curl -H "X-Forwarded-For: 192.168.1.1" http://target.com

# Basic authentication
curl -u username:password http://target.com/admin
```

**Data Submission:**
```bash
# Form data
curl -d "username=admin&password=test" http://target.com/login

# JSON data
curl -H "Content-Type: application/json" -d '{"user":"admin"}' http://target.com/api

# File upload
curl -F "file=@document.pdf" http://target.com/upload
```

### Response Analysis

**Status Code Only:**
```bash
curl -s -o /dev/null -w "%{http_code}" http://target.com
```

**Detailed Response Information:**
```bash
curl -w "Status: %{http_code}\nTime: %{time_total}\nSize: %{size_download}\n" http://target.com
```

**Follow Redirects:**
```bash
curl -L http://target.com  # Follow redirects
curl -L -w "%{url_effective}" http://target.com  # Show final URL
```

### Stealth and Evasion

**Timing Control:**
```bash
curl --limit-rate 1k http://target.com  # Limit bandwidth
curl --max-time 30 http://target.com    # Timeout after 30 seconds
```

**Proxy Usage:**
```bash
curl --proxy socks5://127.0.0.1:9050 http://target.com  # Tor proxy
curl --proxy http://proxy:8080 http://target.com        # HTTP proxy
```

**Certificate Handling:**
```bash
curl -k http://target.com          # Ignore SSL certificate errors
curl --cert client.crt http://target.com  # Client certificate authentication
```

Curl's flexibility makes it an indispensable tool for manual web reconnaissance and custom automation scripts.

---

## 10 – Building a Web Reconnaissance Script

### Custom Directory Discovery Script

```bash
#!/bin/bash

# Web Directory Discovery Script
# Usage: ./web_recon.sh <target_url>

if [ $# -ne 1 ]; then
    echo "Usage: $0 <target_url>"
    echo "Example: $0 http://target.com"
    exit 1
fi

TARGET="$1"
WORDLIST="lista2.txt"

# Check if wordlist exists
if [ ! -f "$WORDLIST" ]; then
    echo "[-] Wordlist file '$WORDLIST' not found!"
    exit 1
fi

echo "[+] Starting web reconnaissance for: $TARGET"
echo "[+] Using wordlist: $WORDLIST"
echo "[+] Found directories:"
echo "================================"

for palavra in $(cat $WORDLIST); do
    resposta=$(curl -s -H "User-Agent: DesecTool" -o /dev/null -w "%{http_code}" $TARGET/$palavra/)
    
    if [ $resposta == "200" ]; then
        echo "Directory found: $TARGET/$palavra/"
    fi
done

echo "================================"
echo "[+] Web reconnaissance complete"
```

### Script Enhancement Options

**Extended Status Code Handling:**
```bash
#!/bin/bash

for palavra in $(cat lista2.txt); do
    resposta=$(curl -s -H "User-Agent: DesecTool" -o /dev/null -w "%{http_code}" $1/$palavra/)
    
    case $resposta in
        200)
            echo "[+] Found: $1/$palavra/ (Status: 200)"
            ;;
        301|302)
            echo "[R] Redirect: $1/$palavra/ (Status: $resposta)"
            ;;
        403)
            echo "[F] Forbidden: $1/$palavra/ (Status: 403)"
            ;;
        401)
            echo "[A] Auth Required: $1/$palavra/ (Status: 401)"
            ;;
    esac
done
```

**File Extension Testing:**
```bash
#!/bin/bash

EXTENSIONS=("php" "html" "txt" "bak" "conf" "log")

for palavra in $(cat lista2.txt); do
    # Test directory
    resposta=$(curl -s -H "User-Agent: DesecTool" -o /dev/null -w "%{http_code}" $1/$palavra/)
    if [ $resposta == "200" ]; then
        echo "Directory: $1/$palavra/"
    fi
    
    # Test files with extensions
    for ext in "${EXTENSIONS[@]}"; do
        resposta=$(curl -s -H "User-Agent: DesecTool" -o /dev/null -w "%{http_code}" $1/$palavra.$ext)
        if [ $resposta == "200" ]; then
            echo "File: $1/$palavra.$ext"
        fi
    done
done
```

**Stealth Features:**
```bash
#!/bin/bash

# Add random delays
sleep_time=$(shuf -i 1-3 -n 1)
sleep $sleep_time

# Rotate User-Agents
USER_AGENTS=(
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36"
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36"
    "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36"
)

UA=${USER_AGENTS[$RANDOM % ${#USER_AGENTS[@]}]}

resposta=$(curl -s -H "User-Agent: $UA" -o /dev/null -w "%{http_code}" $1/$palavra/)
```

### Output Enhancement

**Detailed Logging:**
```bash
#!/bin/bash

LOG_FILE="recon_$(date +%Y%m%d_%H%M%S).log"

echo "[$(date)] Starting scan of $1" >> $LOG_FILE

for palavra in $(cat lista2.txt); do
    resposta=$(curl -s -H "User-Agent: DesecTool" -o /dev/null -w "%{http_code}" $1/$palavra/)
    
    if [ $resposta == "200" ]; then
        result="Directory found: $1/$palavra/"
        echo "$result"
        echo "[$(date)] $result" >> $LOG_FILE
    fi
done
```

This custom approach provides flexibility and control that generic tools may lack, allowing adaptation to specific target behaviors and security measures.

---

## 11 – WhatWeb Analysis

### Understanding WhatWeb

**WhatWeb** is a web application fingerprinting tool that identifies technologies used by websites, including web servers, content management systems, programming languages, and analytics packages.

### Basic WhatWeb Usage

**Simple Scan:**
```bash
whatweb http://target.com
```

**Verbose Output:**
```bash
whatweb -v http://target.com  # Verbose mode
whatweb -a 3 http://target.com  # Aggression level 3 (more thorough)
```

**Multiple Targets:**
```bash
whatweb -i targets.txt  # Scan from file
whatweb http://target1.com http://target2.com  # Multiple URLs
```

### WhatWeb Aggression Levels

**Level 1 (Stealthy):**
- Single HTTP GET request
- Basic header analysis
- Minimal detection capabilities

**Level 2 (Polite):**
- Additional HTTP headers
- More thorough analysis
- Still maintains stealth

**Level 3 (Aggressive):**
- Multiple requests to different URLs
- Tests for specific files and directories
- More comprehensive but detectable

**Level 4 (Heavy):**
- Extensive testing
- May trigger security alerts
- Maximum detection capability

### Advanced WhatWeb Features

**Custom Output Formats:**
```bash
whatweb --log-json=results.json http://target.com
whatweb --log-xml=results.xml http://target.com
whatweb --log-csv=results.csv http://target.com
```

**Plugin Management:**
```bash
whatweb --list-plugins  # Show all available plugins
whatweb --info-plugins=WordPress  # Info about specific plugin
whatweb --use-plugins=Apache,PHP http://target.com  # Use specific plugins
```

**Proxy and Authentication:**
```bash
whatweb --proxy http://proxy:8080 http://target.com
whatweb --user-agent="Custom Agent" http://target.com
whatweb --basic-auth=user:pass http://target.com/admin
```

### Interpreting WhatWeb Results

**Technology Stack Identification:**
```
HTTP Server: Apache/2.4.41
Programming Language: PHP/7.4.3
Content Management: WordPress 5.8
Web Framework: Bootstrap 4.6.0
Analytics: Google Analytics
```

**Security Information:**
```
WAF: Cloudflare
SSL Certificate: Let's Encrypt
Security Headers: X-Frame-Options, X-XSS-Protection
```

**Version Information:**
- Specific software versions help identify known vulnerabilities
- Outdated components suggest potential security weaknesses
- Plugin versions may have disclosed security advisories

### Custom Scans

**CMS-Focused Scan:**
```bash
whatweb --use-plugins=WordPress,Drupal,Joomla http://target.com
```

**Security-Focused Scan:**
```bash
whatweb --use-plugins=WAF,Firewall,SecurityHeaders http://target.com
```

**Infrastructure Scan:**
```bash
whatweb --use-plugins=Apache,Nginx,IIS,Cloudflare http://target.com
```

WhatWeb provides essential intelligence for understanding the technology landscape of target applications, enabling more focused and effective security assessments.

---

## 12 – Wappalyzer Technology Detection

### Understanding Wappalyzer

**Wappalyzer** is a technology profiler that identifies software used by websites, including web servers, content management systems, ecommerce platforms, JavaScript frameworks, analytics tools, and more.

### Wappalyzer Access Methods

**Browser Extension:**
- **Chrome/Firefox Extension:** Real-time analysis while browsing
- **Automatic Detection:** Identifies technologies as you visit pages
- **Visual Interface:** Icons and categories for easy interpretation
- **Detailed Information:** Version numbers and confidence levels

**Command Line Interface:**
```bash
# Install Wappalyzer CLI
npm install -g wappalyzer

# Basic usage
wappalyzer http://target.com

# JSON output
wappalyzer --output=json http://target.com

# Multiple URLs
wappalyzer http://target1.com http://target2.com
```

**API Access:**
```bash
# Wappalyzer API (requires subscription)
curl "https://api.wappalyzer.com/lookup/v2/?urls=http://target.com" \
  -H "x-api-key: YOUR_API_KEY"
```

### Technology Categories

**Web Servers:**
- Apache, Nginx, IIS, LiteSpeed
- Version information and configuration details

**Programming Languages:**
- PHP, Python, Ruby, Java, ASP.NET
- Framework identification (Laravel, Django, Rails)

**Content Management Systems:**
- WordPress, Drupal, Joomla, Magento
- Theme and plugin detection

**Frontend Technologies:**
- JavaScript frameworks (React, Angular, Vue.js)
- CSS frameworks (Bootstrap, Foundation)
- UI libraries and components

**Analytics & Marketing:**
- Google Analytics, Adobe Analytics
- Marketing automation tools
- Social media integrations

**Security & Infrastructure:**
- CDNs (Cloudflare, AWS CloudFront)
- Web Application Firewalls
- SSL certificate providers

### Detection Methodology

**Pattern Recognition:**
- **HTTP Headers:** Server signatures and custom headers
- **HTML Patterns:** Meta tags, comments, specific markup
- **JavaScript Analysis:** Library signatures and global variables
- **CSS Fingerprinting:** Framework-specific stylesheets
- **URL Patterns:** Technology-specific URL structures

**File Detection:**
- **Specific Files:** robots.txt, favicon.ico, specific paths
- **Directory Structures:** Framework-typical layouts
- **Error Pages:** Technology-specific error messages

### Practical Applications

**Vulnerability Assessment:**
```
WordPress 5.0 → Known vulnerabilities in specific version
jQuery 1.8.3 → Outdated library with security issues
Apache 2.2.15 → Version with known exploits
```

**Attack Vector Identification:**
```
Admin Panel: /wp-admin/ (WordPress)
API Endpoints: /api/v1/ (REST API)
Upload Directory: /uploads/ (File upload functionality)
```

**Technology Stack Mapping:**
```
Frontend: React.js + Bootstrap
Backend: Node.js + Express
Database: Likely MongoDB (MEAN stack)
Hosting: AWS (CloudFront CDN detected)
```

### Advanced Usage

**Bulk Analysis:**
```bash
# Analyze multiple subdomains
echo "admin.target.com\napi.target.com\nshop.target.com" | \
  xargs -I {} wappalyzer {}
```

**Integration with Other Tools:**
```bash
# Combine with subdomain enumeration
subfinder -d target.com | wappalyzer --input-file=-
```

Wappalyzer provides comprehensive technology intelligence that guides both automated tools and manual testing approaches for maximum effectiveness.

---

## 13 – Script for Identifying Pages on the Internet

### Google Dorking Automation Script

```bash
#!/bin/bash

# Google Search Script for File Discovery
# Usage: ./google_search.sh <domain> <extension>
# Example: ./google_search.sh target.com pdf

if [ $# -ne 2 ]; then
    echo "Usage: $0 <domain> <file_extension>"
    echo "Example: $0 target.com pdf"
    exit 1
fi

DOMAIN="$1"
EXTENSION="$2"

echo "[+] Searching for .$EXTENSION files on $DOMAIN"
echo "================================"

lynx -dump "http://google.com/search?num=500&q=site:$DOMAIN+ext:$EXTENSION" | \
    cut -d "=" -f2 | \
    grep ".$EXTENSION" | \
    egrep -v "site|google" | \
    sed 's/...$//'g

echo "================================"
echo "[+] Search complete"
```

### Script Enhancement and Variations

**Multiple Extensions Search:**
```bash
#!/bin/bash

DOMAIN="$1"
EXTENSIONS=("pdf" "doc" "xls" "txt" "bak" "conf" "log")

for ext in "${EXTENSIONS[@]}"; do
    echo "[+] Searching for .$ext files..."
    lynx -dump "http://google.com/search?num=500&q=site:$DOMAIN+ext:$ext" | \
        cut -d "=" -f2 | \
        grep ".$ext" | \
        egrep -v "site|google" | \
        sed 's/...$//'g
    echo "---"
done
```

**Specific File Types with Context:**
```bash
#!/bin/bash

# Search for sensitive file types
search_sensitive_files() {
    local domain=$1
    local extensions=("config" "conf" "bak" "backup" "old" "log" "sql" "env")
    
    echo "[!] Searching for potentially sensitive files on $domain"
    
    for ext in "${extensions[@]}"; do
        echo "[+] Extension: .$ext"
        lynx -dump "http://google.com/search?num=100&q=site:$domain+ext:$ext" | \
            cut -d "=" -f2 | \
            grep ".$ext" | \
            egrep -v "site|google|translate" | \
            sed 's/...$//'g | \
            head -10
        echo ""
    done
}

search_sensitive_files "$1"
```

### Advanced Google Dorking

**Login Pages Discovery:**
```bash
#!/bin/bash

search_login_pages() {
    local domain=$1
    
    queries=(
        "site:$domain+inurl:login"
        "site:$domain+inurl:admin"
        "site:$domain+inurl:signin"
        "site:$domain+inurl:auth"
        "site:$domain+intitle:login"
    )
    
    for query in "${queries[@]}"; do
        echo "[+] Query: $query"
        lynx -dump "http://google.com/search?num=50&q=$query" | \
            grep "http" | \
            grep "$domain" | \
            head -5
        echo ""
    done
}
```

**Database and Configuration Files:**
```bash
#!/bin/bash

search_database_files() {
    local domain=$1
    
    # Database dumps and exports
    lynx -dump "http://google.com/search?num=100&q=site:$domain+(ext:sql+OR+ext:db+OR+ext:mdb)" | \
        cut -d "=" -f2 | \
        grep -E "\.(sql|db|mdb)" | \
        egrep -v "site|google"
    
    # Configuration files
    lynx -dump "http://google.com/search?num=100&q=site:$domain+(ext:ini+OR+ext:cfg+OR+ext:conf)" | \
        cut -d "=" -f2 | \
        grep -E "\.(ini|cfg|conf)" | \
        egrep -v "site|google"
}
```

### Output Processing and Analysis

**Clean Results:**
```bash
#!/bin/bash

clean_google_results() {
    local raw_results="$1"
    
    echo "$raw_results" | \
        grep -v "^$" | \
        grep -v "cached" | \
        grep -v "translate.google" | \
        sort -u | \
        while read url; do
            if [ ! -z "$url" ]; then
                echo "[+] Found: $url"
            fi
        done
}
```

**Verify Results:**
```bash
#!/bin/bash

verify_files() {
    while read url; do
        if [ ! -z "$url" ]; then
            status=$(curl -s -o /dev/null -w "%{http_code}" "$url")
            if [ "$status" == "200" ]; then
                echo "[ACTIVE] $url"
            else
                echo "[INACTIVE] $url (Status: $status)"
            fi
        fi
    done
}
```
