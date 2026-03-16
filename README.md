# PDF Link Extractor

A browser-based tool that crawls document directories and exports a structured Excel spreadsheet with all PDF download links — reducing a multi-day manual task to under a minute.

**[Live demo →](https://lpdfe.netlify.app)**

---

## The Problem

A national government institute publishes hundreds of official legal documents on its public website — resolutions, regulations, and other records organized by document type, each in its own directory. Over the years, this archive has grown to more than 500 files.

No internal document management system exists for this archive. The national government has its own platform where every document is accessible, so a dedicated tool was never built. The files live on a web server, organized in folders, publicly accessible — but with no structured index.

When a request came in to build an Excel spreadsheet mapping every document's title, year, and download URL — to feed a future national database — the task fell to a single person who would have had to open each directory, locate each file, and manually copy each link. One by one. For over 500 documents.

That's not a data problem. That's a workflow problem.

---

## The Approach

As the Product Designer assigned to find a solution, the constraint was clear: no backend, no access to modify the existing server infrastructure, no budget for new tooling, and no time to wait for an IT request.
The existing structure — public web directories with navigable file indexes — was itself the data source. The question was how to read it without manual effort.
The solution: a single HTML file that runs entirely in the browser, requires no installation, and can be used by anyone on the team by simply opening a link.

### Key product decisions:

* Single file, no setup — the tool had to be usable by non-technical staff immediately, without installing anything or running commands.
* Auto-detection of directory type — the tool automatically identifies whether a URL is an Apache/Nginx directory index or a regular website, and adjusts its crawling strategy. No configuration needed from the user.
* Year filtering before export — since the request was to eventually organize records by year, filtering was built into the export step rather than left as a manual post-process in Excel.
* Transparent progress log — a real-time terminal-style log shows exactly what the tool is scanning, which builds trust with users who aren't sure what's happening under the hood.

---

## Development Process

Given the urgency of the request, the tool was built using Claude (Anthropic) as an AI development agent — handling the implementation while I focused on product decisions, requirements, and iteration.
The workflow was conversational: I described the problem, evaluated each proposed solution against real constraints (no backend, no installation, non-technical users), identified gaps as they appeared during testing (CORS issues, sites with non-standard directory structures), and directed the next steps accordingly.
This approach compressed what could have been days of development into a single working session — without sacrificing the quality of the product decisions behind it.

---

## How It Works

The tool detects the target page type on first load and picks one of two strategies:

**Directory mode** — for Apache/Nginx file indexes, it recursively traverses the folder structure, collecting every `.pdf` link and extracting the year from the path or filename.

**Web crawler mode** — for standard HTML websites, it follows internal links across up to 120 pages within the same domain, capturing any link pointing to a `.pdf` file regardless of how it's structured in the markup.

Cross-origin requests are handled automatically via [corsproxy.io](https://corsproxy.io). For production use on a self-hosted intranet with proper CORS headers configured, the proxy dependency can be removed with a one-line change.

The final output is a `.xlsx` file with four columns: year, folder, filename, and download URL — ready to be handed off or imported directly.

---

## Stack

Single `.html` file — no framework, no build process, no backend.

- **SheetJS** for Excel export
- **Vanilla JS** for crawling and DOM parsing
- **IBM Plex Mono / Sans** via Google Fonts
- Deployable to any static host (Netlify, GitHub Pages, or a folder on a web server)

---

## Known Limitations

- PDF links rendered dynamically via JavaScript (loaded after page interaction) are not detected — the tool reads static HTML only.
- The web crawler is capped at 120 pages to avoid overloading the target server.
- Year detection relies on `YYYY` patterns in the URL path or filename. Non-standard naming may result in empty year values.
- Cross-origin requests are proxied through a third-party service (corsproxy.io). Not recommended for sensitive or confidential archives without a self-hosted proxy.

---

## Author

**[Francisco Fernández]**  
Product Designer / Staff Designer  
[Portfolio](https://franciscofernandez.netlify.app) · [LinkedIn](https://linkedin.com)

---

*This project started as a conversation about a manual task that was going to take days. It ended as a deployable tool that does the same work in seconds. Sometimes that's what product design looks like in a public sector context — not a new platform, just removing the friction from work that already needs to happen.*