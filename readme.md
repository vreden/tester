<div align="center">
  <img src="https://i.imgur.com/XYZ1234.png" alt="Baileys WhatsApp API" width="600"/>  
  <!-- Replace with actual banner image URL -->
  
  <h1>✨ Baileys WhatsApp API</h1>
  <p><strong>Lightweight, Full-Featured WhatsApp Web API for Node.js</strong></p>
  
  <!-- Badges -->
  <p>
    <a href="https://npmjs.com/package/@whiskeysockets/baileys">
      <img src="https://img.shields.io/npm/v/@whiskeysockets/baileys?color=blue&logo=npm" alt="npm version">
    </a>
    <a href="https://github.com/whiskeysockets/baileys/blob/main/LICENSE">
      <img src="https://img.shields.io/github/license/whiskeysockets/baileys?color=green" alt="License">
    </a>
    <a href="https://github.com/whiskeysockets/baileys/stargazers">
      <img src="https://img.shields.io/github/stars/whiskeysockets/baileys?color=yellow&logo=github" alt="GitHub stars">
    </a>
  </p>
</div>

---

## 📚 Table of Contents  
- [Features](#-features)  
- [Installation](#-installation)  
- [Quick Start](#-quick-start)  
- [Documentation](#-documentation)  
  - [Connecting Account](#-connecting-account)  
  - [Handling Events](#-handling-events)  
  - [Sending Messages](#-sending-messages)  
  - [Groups](#-groups)  
  - [Privacy](#-privacy)  
  - [Advanced](#-advanced)  
- [Disclaimer](#-disclaimer)  

---

## 🌟 Features
- ✅ **Multi-Device Support**  
- 🔄 **Real-Time Messaging** (text, media, polls, buttons)  
- 🛠️ **Group Management** (create, modify, invite)  
- 🔒 **End-to-End Encryption**  
- 📦 **Session Persistence**  

---

## 📥 Installation
```bash
npm install @whiskeysockets/baileys
# or
yarn add @whiskeysockets/baileys
```

---

## 🚀 Quick Start
```javascript
const { makeWASocket } = require('@whiskeysockets/baileys');

const sock = makeWASocket({ printQRInTerminal: true });

sock.ev.on('messages.upsert', ({ messages }) => {
  console.log('New message:', messages[0].message);
});
```

---

## 📖 Documentation

### 🔌 Connecting Account
<details>
<summary><strong>🔗 Connect with QR Code</strong></summary>

```javascript
const sock = makeWASocket({ printQRInTerminal: true });
```
</details>

<details>
<summary><strong>🔢 Connect with Pairing Code</strong></summary>

```javascript
const sock = makeWASocket({ 
  auth: { creds: {}, keys: {} }, // Pairing code flow
  mobile: true 
});
```
</details>

---

### 📡 Handling Events
<details>
<summary><strong>📌 Example to Start</strong></summary>

```javascript
sock.ev.on('messages.upsert', ({ messages }) => {
  console.log('New message:', messages[0].message);
});
```
</details>

<details>
<summary><strong>🗳️ Decrypt Poll Votes</strong></summary>

```javascript
sock.ev.on('messages.update', (m) => {
  if (m.pollUpdates) console.log('Poll vote:', m.pollUpdates);
});
```
</details>

---

### 📨 Sending Messages
<details>
<summary><strong>📝 Text Message</strong></summary>

```javascript
await sock.sendMessage(jid, { text: 'Hello!' });
```
</details>

<details>
<summary><strong>🖼️ Image Message</strong></summary>

```javascript
await sock.sendMessage(jid, { 
  image: fs.readFileSync('image.jpg'),
  caption: 'Check this out!'
});
```
</details>

---

### 🛠️ Groups
<details>
<summary><strong>🔄 Create Group</strong></summary>

```javascript
await sock.groupCreate('New Group', [jid1, jid2]);
```
</details>

<details>
<summary><strong>⚙️ Change Group Settings</strong></summary>

```javascript
await sock.groupSettingUpdate(jid, 'announcement'); // Admins only
```
</details>

---

### 🔒 Privacy
<details>
<summary><strong>🚫 Block/Unblock User</strong></summary>

```javascript
await sock.updateBlockStatus(jid, 'block'); // or 'unblock'
```
</details>

---

### ⚙️ Advanced
<details>
<summary><strong>🔧 Debug Logs</strong></summary>

```javascript
const sock = makeWASocket({ logger: { level: 'debug' } });
```
</details>

<details>
<summary><strong>📡 Raw WebSocket Events</strong></summary>

```javascript
sock.ws.on('CB:presence', (json) => console.log('Presence update:', json));
```
</details>

---

## ⚠️ Disclaimer
This project is **not affiliated** with WhatsApp/Meta. Use at your own risk.  
Refer to [WhatsApp's Terms](https://www.whatsapp.com/legal) for compliance.

---

### 🔗 Full Documentation
Explore all features in the **[Baileys GitHub Wiki](https://github.com/whiskeysockets/baileys/wiki)**
