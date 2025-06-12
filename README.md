<div align="center">  
  <h1>WhatsApp Baileys</h1>
  <img src="https://scontent.fcbn3-1.fna.fbcdn.net/v/t39.8562-6/452878089_796193915665714_3806248071587628833_n.png?_nc_cat=103&ccb=1-7&_nc_sid=f537c7&_nc_ohc=CAPU1tY4MikQ7kNvwHxEd0E&_nc_oc=AdlGnQ-FLrdA6yhuhZkLRk39gRJVkSVKZYy3BfPrLroAir1Kku5hdBXcuowteYWaBtw&_nc_zt=14&_nc_ht=scontent.fcbn3-1.fna&_nc_gid=wOz5wqoBWm7sLPBhYHUUdQ&oh=00_AfPIJz04K7vx7KumOI_HEb9XadrwnmnFk8nbTgEyX5zYFA&oe=68509A22" alt="WhatsApp Baileys" width="600"/>  
  <p><strong>Lightweight, Full-Featured WhatsApp Web for Node.js</strong></p>
  
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
- 🛠️ **Group & Channel Management** (create, modify, invite)  
- 🔒 **End-to-End Encryption**  
- 📦 **Session Persistence**  

---

## 📥 Installation
```bash
npm install @vreden/meta
# or
yarn add @vreden/meta
```

---

## 🚀 Quick Start
```javascript
const {
  default: makeWASocket,
  useMultiFileAuthState,
} = require('@vreden/meta');

const {
	state,
	saveCreds
} = await useMultiFileAuthState("./path/to/sessions/folder")

/*
 * const sock = makeWASocket({ printQRInTerminal: true });
 * code to get WhatsApp web connection
 * QR code or pairing code type available
 */

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
const sock = makeWASocket({
  printQRInTerminal: true, // true to display QR Code
  auth: state
})
```
</details>

<details>
<summary><strong>🔢 Connect with Pairing Code</strong></summary>

```javascript
const sock = makeWASocket({
  printQRInTerminal: false, // false so that the pairing code is not disturbed
  auth: state
})

if (!sock.authState.creds.registered) {
  const number = "62xxxx"

  // use default pairing code (default 1-8)
  const code = await sock.requestPairingCode(number)

  // use customer code pairing (8 digit)
  const customCode = "ABCD4321"
  const code = await sock.requestPairingCode(number, customCode)
  console.log(code)
}
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
<summary><strong>📝 Text Message (Basic + Link Preview)</strong></summary>

```javascript
// Simple text
await sock.sendMessage(jid, { text: 'Hello!' });

// Text with link preview
await sock.sendMessage(jid, {
  text: 'Visit https://example.com',
  linkPreview: {
    'canonical-url': 'https://example.com',
    title: 'Example Domain',
    description: 'A demo website',
    jpegThumbnail: fs.readFileSync('preview.jpg')
  }
});
```
</details>

---

<details>
<summary><strong>🖼️ Image Message</strong></summary>

```javascript
// With local file buffer
await sock.sendMessage(jid, { 
  image: fs.readFileSync('image.jpg'),
  caption: 'My cat!',
  mentions: ['1234567890@s.whatsapp.net'] // Tag users
});

// With URL
await sock.sendMessage(jid, { 
  image: { url: 'https://example.com/image.jpg' },
  caption: 'Downloaded image'
});
```
</details>

---

<details>
<summary><strong>🎥 Video Message</strong></summary>

```javascript
// With local file
await sock.sendMessage(jid, { 
  video: fs.readFileSync('video.mp4'),
  caption: 'Funny clip!'
});

// With URL
await sock.sendMessage(jid, { 
  video: { url: 'https://example.com/video.mp4' },
  caption: 'Streamed video'
});

// With view-once
await sock.sendMessage(jid, {
  video: fs.readFileSync('secret.mp4'),
  viewOnce: true // Disappears after viewing
});
```
</details>

---

<details>
<summary><strong>🎵 Audio/PTT Message</strong></summary>

```javascript
// Regular audio
await sock.sendMessage(jid, { 
  audio: fs.readFileSync('audio.mp3'),
  ptt: false // For music
});

// Push-to-talk (PTT)
await sock.sendMessage(jid, { 
  audio: fs.readFileSync('voice.ogg'),
  ptt: true, // WhatsApp voice note
  waveform: [0, 1, 0, 1, 0] // Optional waveform
});
```
</details>

---

<details>
<summary><strong>📍 Location Message</strong></summary>

```javascript
// Static location
await sock.sendMessage(jid, {
  location: {
    degreesLatitude: 37.422,
    degreesLongitude: -122.084,
    name: 'Google HQ'
  }
});

// Live location (updates in real-time)
await sock.sendMessage(jid, {
  location: {
    degreesLatitude: 37.422,
    degreesLongitude: -122.084,
    accuracyInMeters: 10
  },
  live: true, // Enable live tracking
  caption: 'I’m here!'
});
```
</details>

---

<details>
<summary><strong>📊 Poll Message</strong></summary>

```javascript
// Create a poll
await sock.sendMessage(jid, {
  poll: {
    name: 'Favorite color?',
    values: ['Red', 'Blue', 'Green'],
    selectableCount: 1 // Single-choice
  }
});

// Poll results
await sock.sendMessage(jid, {
  pollResult: {
    name: 'Favorite color?',
    values: [['Red', 10], ['Blue', 20]] // [option, votes]
  }
});
```
</details>

---

<details>
<summary><strong>🛒 Product Message</strong></summary>

```javascript
// Product listing
await sock.sendMessage(jid, {
  product: {
    productId: '123',
    title: 'Cool T-Shirt',
    description: '100% cotton',
    price: 1999, // In cents (e.g., $19.99)
    currencyCode: 'USD',
    productImage: fs.readFileSync('shirt.jpg')
  }
});
```
</details>

---

<details>
<summary><strong>🎭 Buttons Messages</strong></summary>

```javascript
// Button message (Basic)
await sock.sendMessage(jid, {
  text: 'Choose an option:',
  buttons: [
    { buttonId: 'id1', buttonText: { displayText: 'Option 1' } },
    { buttonId: 'id2', buttonText: { displayText: 'Option 2' } }
  ],
  footer: '© WhatsApp Baileys'
});
```

<details>
<summary><strong>🎭 List Messages </strong></summary>

```javascript
// List message
await sock.sendMessage(jid, {
  text: 'Menu:',
  sections: [
    { title: 'Food', rows: [
      { title: 'Pizza', rowId: 'pizza' },
      { title: 'Burger', rowId: 'burger' }
    ]}
  ],
  buttonText: 'Browse'
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