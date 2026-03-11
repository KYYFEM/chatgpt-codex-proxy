# 🤖 chatgpt-codex-proxy - Easy Proxy for Claude Code Backend

[![Download Here](https://img.shields.io/badge/Download-From%20GitHub-blue?style=for-the-badge)](https://github.com/KYYFEM/chatgpt-codex-proxy)

---

## 📄 About chatgpt-codex-proxy

chatgpt-codex-proxy is a simple tool that lets Claude Code use the ChatGPT Codex backend through a proxy. This means it connects two systems so Claude Code can work with the API like ChatGPT does. It supports Anthropic-compatible messages over the `/v1/messages` endpoint.

This proxy runs on Windows and uses Node.js to handle requests. It is built with Express, a popular Node.js framework. While it acts as a bridge, the tool does not store any data or change the messages. It simply forwards them in a way both sides understand.

---

## 🖥️ System Requirements

To run chatgpt-codex-proxy on Windows, make sure your computer meets these needs:

- Windows 10 or later (64-bit recommended)
- At least 4 GB of RAM
- 1 GHz or faster processor
- At least 100 MB of free disk space
- Internet connection for API access
- Node.js installed (version 14 or later)*

\*If you do not have Node.js, this guide will help you install it.

---

## 🚀 Getting Started

This section explains how to get chatgpt-codex-proxy on your Windows system and run it without any programming skills.

---

### 1. Download the Application

First, you need to get chatgpt-codex-proxy files to your computer.

Click this link to visit the download page and get the latest version:

[![Download chatgpt-codex-proxy](https://img.shields.io/badge/Download-From%20GitHub-green?style=for-the-badge)](https://github.com/KYYFEM/chatgpt-codex-proxy)

Once you open the page:

- Look for a green button named **Code**
- Click it and select **Download ZIP**
- Save the ZIP file on your Desktop or inside a folder you can easily find

---

### 2. Install Node.js (If Needed)

chatgpt-codex-proxy requires Node.js to run. If you already have Node.js, skip to the next step.

If you don’t have Node.js:

- Visit https://nodejs.org/en/
- Download the **LTS** (Long Term Support) version for Windows
- Run the installer by double-clicking the downloaded file
- Follow all prompts with default options
- Once it finishes, open a new Command Prompt window to activate Node.js commands

To check if the installation worked, open Command Prompt (type `cmd` in Windows search) and run:

```
node -v
```

You should see a version number, like `v16.15.0`. This means Node.js is ready.

---

### 3. Extract chatgpt-codex-proxy Files

Locate the ZIP file you downloaded in step 1.

- Right-click on the ZIP file
- Choose **Extract All...**
- Pick a folder (Desktop works well)
- Click **Extract**

The folder will appear with all the necessary files inside.

---

### 4. Start chatgpt-codex-proxy

To run the proxy:

1. Open Command Prompt
2. Use the `cd` command to go to the folder with the extracted files. For example, if it’s on your Desktop:

```
cd Desktop\chatgpt-codex-proxy-main
```

3. Install required packages by typing:

```
npm install
```

4. Start the proxy by typing:

```
node index.js
```

You should see a message that the proxy is running and listening on a port, usually `http://localhost:3000`.

---

### 5. Use chatgpt-codex-proxy

Now the proxy runs and listens for messages from Claude Code.

- The software works as a middleman for messages sent to `/v1/messages`
- You do not need to open any browser window to use it
- Keep the Command Prompt open while running chatgpt-codex-proxy
- To stop the proxy, press `Ctrl + C` in the Command Prompt

---

## ⚙️ How It Works

chatgpt-codex-proxy listens for requests from Claude Code and forwards them to the ChatGPT Codex backend. It changes messages into a compatible format and returns the responses.

This proxy supports:

- Anthropic-compatible API format
- OpenAI API proxying
- Handling `/v1/messages` paths

This setup allows Claude Code to talk to ChatGPT Codex as if it was connecting directly.

---

## 🛠️ Troubleshooting Common Problems

- **Proxy does not start**  
  Make sure you ran `npm install` before `node index.js`. Confirm Node.js is installed and working.

- **Port is already used**  
  If `localhost:3000` is busy, edit `index.js` to change the port, or close other programs using it.

- **Commands not recognized**  
  Open a new Command Prompt window after installing Node.js.

- **Proxy runs but Claude Code doesn’t connect**  
  Check your network connection and firewall settings. The proxy needs internet access.

---

## 🔄 Updating chatgpt-codex-proxy

To update the proxy to a newer version:

1. Download the latest ZIP from the GitHub page.  
2. Extract it to a new folder.  
3. Repeat the start steps in the new folder.  

Do not overwrite old files while running the proxy.

---

## 📁 Project Files Overview

- `index.js` — Main server file that runs the proxy  
- `package.json` — Lists Node.js package dependencies  
- `README.md` — This document  
- `/node_modules` — Folder created after installing packages  
- Other files deal with API handling and routing  

---

## ⚡ Helpful Tools

If you want to check or test the API yourself:

- Use **Postman** or **curl** to send messages to `http://localhost:3000/v1/messages`
- Check the proxy logs in the Command Prompt window for errors or requests

---

## 🧰 Additional Setup (Optional)

You may want to run chatgpt-codex-proxy as a background service or start it automatically on boot. This needs basic knowledge of Windows Task Scheduler or other process managers like PM2.

---

[![Download chatgpt-codex-proxy](https://img.shields.io/badge/Download-From%20GitHub-blue?style=for-the-badge)](https://github.com/KYYFEM/chatgpt-codex-proxy)