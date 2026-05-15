# VSCode Codex Login 403 Forbidden Fix

## Problem

When logging into Codex/OpenAI inside VSCode:

```text
403 Forbidden
Country, region, or territory not supported
```

Even though:
- ChatGPT webpage works
- VPN/Clash is enabled
- Browser can access OpenAI normally

---

# Root Cause

Usually:

```text
Browser traffic → uses proxy correctly
VSCode Extension Host traffic → does not use proxy correctly
```

Result:

```text
Browser IP = supported
VSCode OAuth IP = blocked/risk-controlled
```

---

# Applicable Platforms

- Windows
- macOS
- Ubuntu / Linux
- WSL
- Remote SSH

---

# Step 1 — Verify Proxy

## Windows (PowerShell)

```powershell
Get-ChildItem Env: | findstr -i proxy
```

## macOS / Linux

```bash
env | grep -i proxy
```

Expected:

```text
HTTP_PROXY=http://127.0.0.1:7890/
HTTPS_PROXY=http://127.0.0.1:7890/
```

---

# Step 2 — Check Current IP

```bash
curl ipwho.is
```

Avoid:
- HK datacenter nodes
- shared VPN exits
- heavily abused proxy IPs

Recommended regions:
- JP
- SG
- US

Recommended node type:
- Residential / Home Broadband

---

# Step 3 — Launch VSCode With Proxy (Core Fix)

## Windows

```powershell
code --proxy-server="http://127.0.0.1:7890" --proxy-bypass-list="localhost;127.0.0.1"
```

## macOS / Linux

```bash
code --proxy-server="http://127.0.0.1:7890"
```

> Replace `7890` with your actual proxy port.

---

# Step 4 — Restart VSCode

## Windows

```powershell
taskkill /F /IM Code.exe
```

## macOS / Linux

```bash
killall code
```

Then relaunch VSCode using the proxy command above.

---

# Step 5 — Login Again

Inside VSCode:

```text
Sign in with ChatGPT
```

Complete OAuth login.

---

# Why This Works

```text
--proxy-server
```

forces VSCode Electron network traffic through the correct VPN/proxy route.

```text
--proxy-bypass-list
```

prevents localhost OAuth callbacks from being incorrectly proxied.

---

# Summary

The issue is usually caused by:

```text
VSCode OAuth traffic bypassing proxy
+
Current VPN/IP being risk-controlled by OpenAI
```

Explicitly forcing VSCode to use the proxy resolves the issue in most cases.
