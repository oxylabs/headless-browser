# headless-browser

[![Headless Browser](https://github.com/oxylabs/headless-browser/blob/main/Github-banner-HB-1532x354.png)](https://oxylabs.io/products/headless-browser?utm_source=877&utm_medium=affiliate&groupid=877&utm_content=headless-browser-github&transaction_id=102f49063ab94276ae8f116d224b67))

[![](https://dcbadge.limes.pink/api/server/Pds3gBmKMH?style=for-the-badge&theme=discord)](https://discord.gg/Pds3gBmKMH) [![YouTube](https://img.shields.io/badge/YouTube-Oxylabs-red?style=for-the-badge&logo=youtube&logoColor=white)](https://www.youtube.com/@oxylabs)

# Headless browser
- [What is Headless Browser?](#what-is-headless-browser)
- [Key Features](#key-features)
    + [Handling CAPTCHA Events](#handling-captcha-events)
    + [Dynamic CAPTCHA Solving](#dynamic-captcha-solving)
    + [Geolocation Targeting](#geolocation-targeting)
    + [Device Type](#device-type)
    + [Session Inspection](#session-inspection)
- [How does Headless Browser Work?](#how-does-headless-browser-work)
    + [Connection Details](#connection-details)
- [How to Optimize Headless Browser Traffic](#how-to-optimize-headless-browser-traffic)
- [Headless Broswer vs. Web Scraper API vs. Web Unblocker](#headless-browser-vs-web-scraper-api-vs-web-unblocker)
    + [When to Choose Each](#when-to-choose-each)
- [Common Use Cases](#common-use-cases)
- [Fair Usage Policy](#fair-usage-policy)
- [Learn More](#learn-more)
- [Contact Us](#contact-us)

Welcome to the official repository overview for Oxylabs' [Headless Browser](https://oxylabs.io/products/headless-browser). This guide provides a technical overview of how Headless Browser works, its key features, some common use cases, and how to use it to optimize your web scraping infrastructure.

## What is Headless Browser?
Headless Browser is a cloud-based solution from Oxylabs that lets you run and control remote browser sessions without the complexity of managing them locally or on your own infrastructure. It provides a seamless way to execute browser-based automation, testing, and web scraping without dealing with browser setup, resource constraints, or detection challenges.
Unlike running a local headless browser instance (such as a self-managed Puppeteer or Playwright setup), Oxylabs' Headless Browser runs on dedicated remote servers with integrated residential proxies and built-in stealth features. This means you get a production-ready browsing environment that handles anti-bot detection, CAPTCHA solving, and proxy rotation out of the box.


## Browser Environments
Headless Browser offers two specialized browser environments:
- [Chrome-based browser](https://developers.oxylabs.io/scraping-solutions/headless-browser/chrome) (`ubc.oxylabs.io`) – high-performance remote browsers with advanced stealth running on dedicated servers with integrated proxies.
- [Firefox-based browser (Legacy)](https://developers.oxylabs.io/scraping-solutions/headless-browser/firefox) (`ubs.oxylabs.io`) – Firefox implementation with built-in anti-detection features and proxy integration. (_**Note:** this environment will be deprecated soon._)

It works with any library that supports the [Chrome DevTools Protocol (CDP)](https://developer.chrome.com/docs/devtools), including:
- [Playwright](https://pypi.org/project/playwright/) (for Firefox, supported Playwright versions are 1.51 and 1.56)
- [Puppeteer](https://pypi.org/project/pyppeteer/)
- Other CDP-compatible automation frameworks

For more information, check out how to use Oxylabs Headless Browser for [Playwright web scraping](https://github.com/oxylabs/playwright-web-scraping), [Puppeteer web scraping](https://github.com/oxylabs/puppeteer-tutorial), and [MCP integration](https://github.com/oxylabs/oxylabs-hb-mcp).

## Key Features
Headless Browser comes packed with features designed to simplify large-scale web scraping and browser automation, such as –
### Handling CAPTCHA Events
One of the most valuable features of Headless Browser is its built-in CAPTCHA handling. By default, it automatically detects and solves CAPTCHAs when a page loads. However, for sites that present CAPTCHAs during later interactions (such as form submissions or popup windows), you can monitor the solving process using an event-based approach.
The browser extension sends messages to the `window` object that your script can listen for:
| Event | Description |
|--------|-------------|
| `oxylabs-captcha-solve-start` | The CAPTCHA is detected and solving has begun. |
| `oxylabs-captcha-solve-end` | The CAPTCHA was successfully solved. |
| `oxylabs-captcha-solve-error` | The auto-solver failed to bypass the CAPTCHA. |

You subscribe to these events before navigation, then pause your automation until the CAPTCHA is resolved. CAPTCHA solving typically takes up to 30 seconds, depending on the type and complexity. 
For more information about how to handle CAPTCHA events, see our [documentation](https://developers.oxylabs.io/scraping-solutions/headless-browser/features/handling-captcha-events).

### Dynamic CAPTCHA Solving
Some websites display CAPTCHAs not on page load but at later stages – for example, after clicking a submit button or during a specific user interaction. Headless Browser lets you trigger CAPTCHA detection and solving manually at any point during your session.
To manually trigger solving, send a message to the window object:
```bash
window.postMessage({action: 'solve_captcha', type: '<captcha_type>'}, '*')
```
Supported CAPTCHA types:
- `hcaptcha`
- `recaptcha`
- `turnstile` (Cloudflare CAPTCHA)
To trigger reCAPTCHA solving after a form submission, visit our [documentation](https://developers.oxylabs.io/scraping-solutions/headless-browser/features/dynamic-captcha-solving).

_**Important:** Cloudflare `turnstile` CAPTCHAs require a different approach. You must initiate CAPTCHA detection before performing the action that triggers it, since `turnstile` must be intercepted before it appears on screen._

### Geolocation Targeting
You can specify a geographic location for your Headless Browser session by adding parameters to your connection URL. This is essential for scraping location-specific content, verifying geo-targeted ads, or accessing region-locked pages.

Available parameters:
- `p_cc` – selects the country using a 2-letter ISO country code (e.g., `US`, `DE`, `FR`). If no country is specified, the system auto-assigns one based on availability.
- `p_city` – selects a specific city in lowercase (e.g., `berlin`, `los_angeles`). Requires `p_cc` or `p_state` to also be specified.
- `p_state` – selects a US state in lowercase (e.g., `texas`, `ohio`). If both `p_state` and `p_cc` are specified, `p_state` takes priority.

If you want to learn more information about Headless Browsers geolocation targeting, check out our [documentation](https://developers.oxylabs.io/scraping-solutions/headless-browser/features/geolocation-targeting) on Headless Browsers geolocation targeting.

### Device Type
Headless Browser can emulate different device types using the `p_device` parameter. This is useful for scraping responsive layouts, mobile-specific content, or device-dependent behavior such as different CAPTCHAs or UI elements.
Available values:
| Value | Description |
|--------|-------------|
| Desktop (default) | Emulates a standard desktop browser with a full-size viewport and desktop user-agent string. |
| Mobile | Simulates a smartphone with smaller screen resolution, touch capabilities, and a mobile user-agent. |
| Tablet | Emulates a tablet device for medium-sized layouts and hybrid interfaces (Chrome only). |

**Usage example:**
```bash
  # Connect with mobile device emulation.
	wss://username:password@ubc.oxylabs.io?p_device=mobile
```
You can combine device type with geolocation targeting:
```bash
 # Mobile device targeting Germany.
	wss://username:password@ubc.oxylabs.io?p_device=mobile&p_cc=DE
```
### Session Inspection
The Session Inspection tool is a powerful debugging feature that uses VNC (Virtual Network Computing) technology to provide real-time visual access to your browser automation sessions. It creates a secure bridge between your headless browser instance and a graphical interface you can observe and even control.

**When to use it:**
- Diagnose complex issues that aren't evident from logs or error messages.
- Understand page rendering exactly as the browser sees it.
- Verify proper execution of your automation workflows.
- Troubleshoot unexpected behavior in real time.

For more information, check out our [documentation](https://developers.oxylabs.io/scraping-solutions/headless-browser/features/session-inspection) about Session Inspection.

Some other features of Headless Browser include:
- **Integrated residential proxies –** every session routes through Oxylabs' residential proxy network, eliminating the need for separate proxy management.
- **Browser arguments (Chrome) –** customize browser behavior with arguments like `hide-scrollbars`, `disable-notifications`, `force-color-profile`, and more.
- **MCP integration –** connect AI systems like Claude Desktop or Cursor to Headless Browser via Model Context Protocol for AI-driven web automation.
- **Traffic optimization –** block unnecessary resources (images, stylesheets, fonts) to reduce bandwidth and speed up scraping.

## How does Headless Browser Work?
Headless Browser operates through a secure WebSocket (WSS) connection. Your automation script connects to Oxylabs' remote browser infrastructure using a standard WebSocket endpoint, just as you would connect to a local browser instance. The key difference is that the browser runs on Oxylabs' dedicated servers with residential proxies, stealth features, and CAPTCHA solving integrated at the infrastructure level.

The workflow is straightforward:
- **Connect –** your script establishes a WebSocket connection to Oxylabs' endpoint using your credentials.
- **Automate –** use Playwright, Puppeteer, or any CDP-compatible library to control the browser exactly as you would a local instance.
- **Collect –** extract HTML, take screenshots, capture network logs, or retrieve any data your automation script produces.
- **Disconnect –** close the browser session when done. Billing is based on traffic (GB) used.

### Connection details:
| Browser              | WebSocket Endpoint              | US-Optimized Endpoint |
|----------------------|---------------------------------|-----------------------|
| Chrome               | `wss://ubc.oxylabs.io`          | `wss://ubc-us.oxylabs.io`  |
| Fireforx (Legacy)    | `wss://ubs.oxylabs.io`          |  `wss://ubs-us.oxylabs.io` |

**Authentication:** Credentials are passed directly in the WebSocket URL in the format `wss://username:password@endpoint`.
**Rate limits:** Each account has `100` concurrent sessions and can launch up to 10 sessions per second per browser type. Contact [Oxylabs support](support@oxylabs.io) to request higher limits.

## How to Optimize Headless Browser Traffic
Since Headless Browser is billed based on traffic (GB), optimizing bandwidth usage directly reduces costs. Automation scripts often download unnecessary resources like images, stylesheets, fonts, and media files that you don't need for data extraction. The most effective optimization is intercepting network requests and blocking non-essential resource types before they download.

**Additional optimization tips:**
- Block third-party tracking scripts and analytics that don't contribute to your data extraction.
- Use the US-optimized endpoints (`ubc-us.oxylabs.io`) if you're based in the US to reduce latency.
- Keep sessions short – connect, scrape, disconnect. Idle sessions still consume resources.
- Combine geolocation targeting with resource blocking for maximum efficiency.

## Headless Browser vs. Web Scraper API vs. Web Unblocker
Oxylabs offers three distinct scraping products, each designed for different use cases. Choosing the right one depends on the level of control you need and the complexity of your target websites.
| Feature | Web Scraper API | Web Unblocker | Headless Browser |
|---------|----------------------|--------------------|-------------|
| **Main purpose** | Scrape and parse web data; get structured results (JSON, etc.) | Access and retrieve raw web content while handling anti-bot challenges | Full browser automation; interaction for the toughest targets |
| **Request input** | URL (with optional headers and parameters) | URL (with optional headers and parameters) | Automation script (Puppeteer/Playwright commands) |
| **Output** | Structured JSON, full HTML, XHR, or Markdown | Full HTML | HTML, screenshots, network logs, any script-extracted data |
| **JavaScript sendering** | Yes | Yes | Yes (native – runs JS as a real browser) |
| **Ease of use** | Easiest – minimal code, focus on results | Intermediate – you parse/handle HTML yourself | Advanced – you write full automation scripts |
| **CAPTCHA handling** | Automatic | Automatic | Automatic + manual trigger support |
| **Billing** | Per successful result | Per successful request traffic | Per traffic (GB) used |

### When to choose each:
- **[Web Scraper API](https://oxylabs.io/products/scraper-api/web) –** you want structured, ready-to-use data with a single API call. Best for e-commerce, search, and common targets.
- **[Web Unblocker](https://oxylabs.io/products/web-unblocker) –** you have an existing scraping pipeline and need a drop-in proxy replacement that handles unblocking automatically.
- **[Headless Browser](https://oxylabs.io/products/headless-browser) –** you need full browser control for JavaScript-heavy sites, complex interactions, AI-driven automation, or when you need to interact with dynamic page elements (clicks, form fills, scrolling).

## Common Use Cases 
Headless Browser is the right choice when your task requires real browser interaction rather than simple HTTP requests. Common scenarios include:
1. **JavaScript-heavy websites –** single-page applications (SPAs) and sites that load content dynamically via JavaScript require a real browser engine to render properly.
2. **Complex user interactions –** scraping workflows that involve clicking buttons, filling forms, navigating multi-step processes, or handling infinite scroll.
3. **CAPTCHA-protected sites –** targets that present CAPTCHAs on load or during interaction, including hCaptcha, reCAPTCHA, and Cloudflare Turnstile.
4. **AI-driven web automation –** connecting AI systems (Claude Desktop, Cursor) via MCP to perform web navigation and data retrieval tasks autonomously.
5. **Ad verification and brand safety –** monitoring how ads render across different geolocations and device types, verifying placement and content.
6. **Visual regression testing –** taking screenshots from different geographies and devices to ensure consistent rendering.
7. **Competitive intelligence –** monitoring competitor pricing, product listings, and content changes on dynamic websites.
8. **Session-based workflows –** tasks that require maintaining state across multiple pages (login flows, shopping carts, multi-page forms).

## Fair Usage Policy
To ensure service stability and consistent performance for all users, Oxylabs applies the following limits to Headless Browser:
- **Concurrent sessions:** `100` per account (per browser type).
- **Session launch rate:** Up to `10` sessions per second per browser type.
- **Billing:** Based on traffic (GB) consumed during your sessions.

To keep your usage efficient and your costs predictable, follow the traffic optimization techniques described earlier in this guide – blocking unnecessary resources alone can reduce bandwidth consumption significantly.

## Learn more
For detailed configuration, advanced usage, and multi-language code examples, check these official pages:
- [Get started with Headless Browser](https://oxylabs.io/products/headless-browser)
- [Headless Browser documentation](https://developers.oxylabs.io/scraping-solutions/headless-browser)
- [Troubleshooting guide](https://developers.oxylabs.io/scraping-solutions/headless-browser/troubleshooting-guide)

## Contact us
If you have questions or need support, reach out to us at [support@oxylabs.io](support@oxylabs.io), or through live chat, accessible via [Oxylabs Dashboard](https://dashboard.oxylabs.io/en/). For enterprise-related inquiries, contact your dedicated account manager.
