##  What is Shodan?

**[Shodan.io](https://www.shodan.io/)** is a search engine that indexes internet-connected devices and web servers. Unlike Google, which indexes websites, Shodan indexes **open ports, services, headers, and banners** — e.g., Apache servers, webcams, firewalls, databases, etc.



##  1. **Set Up Your Environment**

* **Create a Shodan account**: [https://account.shodan.io/register](https://account.shodan.io/register)
* Get a **Shodan API key** (optional but helpful for automation).
* Set up your tools:

  * **Burp Suite** (for manual testing)
  * **Nmap**, **Nikto**, **WhatWeb**, **Dirb**, **Gobuster**
  * **Python** for scripting
  * **Virtual Machine** or **Kali Linux** for isolation



##  2. **Understand What’s Legal**

Before doing anything, understand:

| Legal                                                                 | Illegal                                                  |
| --------------------------------------------------------------------- | -------------------------------------------------------- |
| Scanning public ports (passive info)                                  | Exploiting a live system                                 |
| Reporting bugs via **bug bounty** or **Vuln disclosure** programs     | Gaining unauthorized access or causing denial of service |
| Testing **your own lab**, CTFs, or vulnerable targets like HackTheBox | Running scans against random targets without permission  |

>  Always test systems you **own**, have **explicit permission**, or that offer a **vulnerability disclosure policy**.



##  3. **Start with Shodan Searches (Passive Recon)**

Shodan lets you search by:

* Banner content
* Ports
* Products
* Location
* SSL certs
* Operating system

###  Examples of useful Shodan filters:

```sh
http.title:"Login"
http.favicon.hash:-247388890
product:"Apache httpd"
product:"PHPMyAdmin"
ssl.cert.subject.CN:"example.com"
org:"Example ISP"
country:"US"
```

### Useful queries:

* `product:"Apache httpd"` – find Apache servers
* `title:"index of /"` – find open directories
* `http.html:"phpMyAdmin"` – look for exposed phpMyAdmin
* `port:22 product:"OpenSSH"` – find exposed SSH servers
* `has_screenshot:true` – show UIs with screenshot previews
* `vuln:CVE-2021-44228` – known vulnerable to Log4Shell



##  4. **Pick a Target with a Disclosure Policy**

Once you find a host of interest, check:

* Does the company have a **Bug Bounty** program (e.g., HackerOne, Bugcrowd)?
* Does the site have a **Responsible Disclosure Policy**?
* If no policy exists — **do not test actively** unless you’re allowed.

You can check:

* [https://hackerone.com/directory/programs](https://hackerone.com/directory/programs)
* [https://bugcrowd.com/programs](https://bugcrowd.com/programs)
* Or search: `site:example.com responsible disclosure`



##  5. **Enumerate & Test in Burp Suite (Ethically)**

Once you find a target with permission:

* Enumerate subdomains (using tools like **Amass** or **Subfinder**)
* Use **Burp Suite** to:

  * Intercept requests
  * Test for common web vulns (XSS, SQLi, etc.)
  * Use **Burp Active Scanner** (only if allowed!)
* Run **Dirb/Gobuster** to discover hidden endpoints
* Check for headers like:

  * `X-Powered-By`
  * `Server`
  * `Access-Control-*`
* Test login forms for weak credentials (again, **only with permission**)



##  6. **Report Responsibly**

If you find something:

* Document clearly: affected endpoint, parameters, payloads, reproduction steps, screenshots.
* Submit it via the company’s:

  * Bug bounty platform
  * Disclosure email (e.g., `security@example.com`)
  * Contact form

>  Use CVSS or OWASP to explain severity.



##  7. **Build a Lab to Practice Safely**

While you’re learning:

* Use platforms like:

  * [Hack The Box](https://www.hackthebox.com/)
  * [TryHackMe](https://tryhackme.com/)
  * [VulnHub](https://www.vulnhub.com/)
  * [PortSwigger Web Security Academy](https://portswigger.net/web-security)

Or build your own vulnerable apps:

* DVWA
* Juice Shop
* BWAPP
* WebGoat



##  Next Steps

1. Create a Shodan account & explore basic searches.
2. Learn and practice OWASP Top 10 vulnerabilities.
3. Understand and follow **ethical hacking rules**.
4. Build a **portfolio of your reports** (sanitize sensitive data).
5. Contribute to open bug bounty platforms.

