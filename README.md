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

```javascript
const jid: string
const content: AnyMessageContent
const options: MiscMessageGenerationOptions

sock.sendMessage(jid, content, options)
```

<details>
<summary><strong>📝 Text Message (Basic + Link Preview)</strong></summary>

<small>Simple Text</small>
```javascript
await sock.sendMessage(jid, { text: 'Hello!' });
```

<small>Text with link preview</small>
```javascript
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

<small>With Quoted Reply</small>
```javascript
await sock.sendMessage(jid, { text: 'Hello!' }, { quoted: message });
```
</details>


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

<small>With Local File</small>
```javascript
await sock.sendMessage(jid, { 
  video: fs.readFileSync('video.mp4'),
  caption: 'Funny clip!'
});
```

<small>With URL File</small>
```javascript
await sock.sendMessage(jid, { 
  video: { url: 'https://example.com/video.mp4' },
  caption: 'Streamed video'
});
```

<small>View Once Message</small>
```javascript
await sock.sendMessage(jid, {
  video: fs.readFileSync('secret.mp4'),
  viewOnce: true // Disappears after viewing
});
```
</details>

<details>
<summary><strong>🎵 Audio/PTT Message</strong></summary>

<small>Regular audio</small>
```javascript
await sock.sendMessage(jid, { 
  audio: fs.readFileSync('audio.mp3'),
  ptt: false // For music
});

<small>Push-to-talk (PTT)</small>
```javascript
await sock.sendMessage(jid, { 
  audio: fs.readFileSync('voice.ogg'),
  ptt: true, // WhatsApp voice note
  waveform: [0, 1, 0, 1, 0] // Optional waveform
});
```
</details>

<details>
<summary><strong>📍 Location Message</strong></summary>

<small>Static location</small>
```javascript
await sock.sendMessage(jid, {
  location: {
    degreesLatitude: 37.422,
    degreesLongitude: -122.084,
    name: 'Google HQ'
  }
});
```

<small>Thumbnail location</small>
```javascript
await sock.sendMessage(jid, {
  location: {
    degreesLatitude: 37.422,
    degreesLongitude: -122.084,
    name: 'Google HQ',
    jpegThumbnail: fs.readFileSync('preview.jpg')
  }
});
```

<small>Live location (updates in real-time)</small>
```javascript
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

<details>
<summary><strong>📊 Poll Message</strong></summary>

<small>Create a poll</small>
```javascript
await sock.sendMessage(jid, {
  poll: {
    name: 'Favorite color?',
    values: ['Red', 'Blue', 'Green'],
    selectableCount: 1 // Single-choice
  }
});
```

<small>Poll results</small>
```javascript
await sock.sendMessage(jid, {
  pollResult: {
    name: 'Favorite color?',
    values: [['Red', 10], ['Blue', 20]] // [option, votes]
  }
});
```
</details>

<details>
<summary><strong>🛒 Product Message</strong></summary>

```javascript
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

<details>
<summary><strong>🎭 Buttons Messages</strong></summary>

<small>Button Text</small>
```javascript
await sock.sendMessage(jid, {
  text: 'Choose an option:',
  buttons: buttonParams,
  footer: '© WhatsApp Baileys'
});
```

<small>Button Image</small>
```javascript
await sock.sendMessage(jid, {
  image: fs.readFileSync('image.jpg'),
  caption: 'Choose an option:',
  buttons: buttonParams,
  footer: '© WhatsApp Baileys'
});
```

<small>Button Video</small>
```javascript
await sock.sendMessage(jid, {
  video: fs.readFileSync('video.mp4'),
  caption: 'Choose an option:',
  buttons: buttonParams,
  footer: '© WhatsApp Baileys'
});
```

<small>Button Location</small>
```javascript
await sock.sendMessage(jid, {
  location: {
    degreesLatitude: 37.422,
    degreesLongitude: -122.084
  },
  caption: 'Choose an option:',
  buttons: buttonParams,
  footer: '© WhatsApp Baileys'
});
```

<small>Button Params Default</small>
```javascript
const buttonParams = [{
  buttonId: 'id1',
  buttonText: {
    displayText: 'Option 1'
  },
  type: 1
},{
  buttonId: 'id2',
  buttonText: {
    displayText: 'Option 2'
  },
  type: 1
}]
```

<small>Button Params NativeFlow</small>
```javascript
const buttonParams = [{
  buttonId: 'id1',
  buttonText: {
    displayText: 'Option 1'
  },
  type: 1
},{
  buttonId: 'flow',
  buttonText: {
    displayText: 'flow'
  },
  nativeFlowInfo: {
    name: 'cta_url',
    buttonParamsJson: JSON.stringify({
      display_text: 'Visit URL',
      url: 'https://web.whatsapp.com',
      merchant_url: 'https://web.whatsapp.com'
    })
  },
  type: 2
}]
```
</details>

<details>
<summary><strong>🎭 List Messages </strong></summary>

<small>Single Select</small>
```javascript
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

<small>Product List</small>
```javascript
await sock.sendMessage(jid, {
  title: 'Here is title product',
  text: 'Text message',
  footer: '© WhatsApp Baileys',
  buttonText: 'Select Menu', 
  productList: [{
    title: 'Product Collection', 
    products: [{
      productId: '23942543532047956' // catalog business ID
    }]
  }], 
  businessOwnerJid: '6285643115199@s.whatsapp.net',
  thumbnail: { url: 'https://www.example.com/file' }
})
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