<div align="center">
  <img src="https://scontent.fcbn3-1.fna.fbcdn.net/v/t39.8562-6/452878089_796193915665714_3806248071587628833_n.png?_nc_cat=103&ccb=1-7&_nc_sid=f537c7&_nc_ohc=CAPU1tY4MikQ7kNvwHxEd0E&_nc_oc=AdlGnQ-FLrdA6yhuhZkLRk39gRJVkSVKZYy3BfPrLroAir1Kku5hdBXcuowteYWaBtw&_nc_zt=14&_nc_ht=scontent.fcbn3-1.fna&_nc_gid=wOz5wqoBWm7sLPBhYHUUdQ&oh=00_AfPIJz04K7vx7KumOI_HEb9XadrwnmnFk8nbTgEyX5zYFA&oe=68509A22" alt="WhatsApp Baileys" width="600"/>  

  <h1>WhatsApp Baileys</h1>
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

<br>

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

<br>

## 🌟 Features
- ✅ **Multi-Device Support**  
- 🔄 **Real-Time Messaging** (text, media, polls, buttons)  
- 🛠️ **Group & Channel Management** (create, modify, invite)  
- 🔒 **End-to-End Encryption**  
- 📦 **Session Persistence**  

<br>

## 📥 Installation
```bash
npm install @vreden/meta
# or
yarn add @vreden/meta
```

<br>

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

<br>

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

<br>

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

<br>

### 📨 Sending Messages

```javascript
const jid: string
const content: AnyMessageContent
const options: MiscMessageGenerationOptions

sock.sendMessage(jid, content, options)
```

<details>
<summary><strong>📝 Text Message</strong></summary>

```javascript
// Simple Text
await sock.sendMessage(jid, { text: 'Hello!' });
```

```javascript
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

```javascript
// With Quoted Reply
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
```

```javascript
// With URL
await sock.sendMessage(jid, { 
  image: { url: 'https://example.com/image.jpg' },
  caption: 'Downloaded image'
});
```
</details>

<details>
<summary><strong>🎥 Video Message</strong></summary>

```javascript
// With Local File
await sock.sendMessage(jid, { 
  video: fs.readFileSync('video.mp4'),
  caption: 'Funny clip!'
});
```

```javascript
// With URL File
await sock.sendMessage(jid, { 
  video: { url: 'https://example.com/video.mp4' },
  caption: 'Streamed video'
});
```

```javascript
// View Once Message
await sock.sendMessage(jid, {
  video: fs.readFileSync('secret.mp4'),
  viewOnce: true // Disappears after viewing
});
```
</details>

<details>
<summary><strong>🎵 Audio/PTT Message</strong></summary>

```javascript
// Regular audio
await sock.sendMessage(jid, { 
  audio: fs.readFileSync('audio.mp3'),
  ptt: false // For music
});
```

```javascript
// Push-to-talk (PTT)
await sock.sendMessage(jid, { 
  audio: fs.readFileSync('voice.ogg'),
  ptt: true, // WhatsApp voice note
  waveform: [0, 1, 0, 1, 0] // Optional waveform
});
```
</details>

<details>
<summary><strong>👤 Contact Message</strong></summary>

```javascript
const vcard = 'BEGIN:VCARD\n' // metadata of the contact card
  + 'VERSION:3.0\n' 
  + 'FN:Jeff Singh\n' // full name
  + 'ORG:Ashoka Uni\n' // the organization of the contact
  + 'TELtype=CELLtype=VOICEwaid=911234567890:+91 12345 67890\n' // WhatsApp ID + phone number
  + 'END:VCARD'

await sock.sendMessage(jid, { 
  contacts: { 
    displayName: 'Your Name', 
    contacts: [{ vcard }] 
  }
})
```
</details>

<details>
<summary><strong>💥 React Message</strong></summary>

```javascript
await sock.sendMessage(jid, {
  react: {
    text: '👍', // use an empty string to remove the reaction
    key: message.key
  }
})
```
</details>

<details>
<summary><strong>📌 Pin & Keep Message</strong></summary>

| Time  | Seconds        |
|-------|----------------|
| 24h    | 86.400        |
| 7d     | 604.800       |
| 30d    | 2.592.000     |

```javascript
// Pin Message
await sock.sendMessage(jid, {
  pin: {
    type: 1, // 2 to remove
    time: 86400,
    key: message.key
  }
})
```

```javascript
// Keep message
await sock.sendMessage(jid, {
  keep: {
    key: message.key,
    type: 1 // or 2 to remove
  }
})
```
</details>

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
```

```javascript
// Thumbnail location
await sock.sendMessage(jid, {
  location: {
    degreesLatitude: 37.422,
    degreesLongitude: -122.084,
    name: 'Google HQ',
    jpegThumbnail: fs.readFileSync('preview.jpg')
  }
});
```

```javascript
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

<details>
<summary><strong>📞 Call Message</strong></summary>

```javascript
await sock.sendMessage(jid, {
  call: {
    name: 'Here is call message',
    type: 1 // 2 for video
  }
})
```
</details>

<details>
<summary><strong>🗓️ Event Message</strong></summary>

```javascript
await sock.sendMessage(jid, {
  event: {
    isCanceled: false, // or true
    name: 'Here is name event',
    description: 'Short description here',
    location: {
      degreesLatitude: 0,
      degreesLongitude: 0,
      name: 'Gedung Tikus Kantor'
    },
    startTime: 17..., // timestamp date
    endTime: 17..., // timestamp date
    extraGuestsAllowed: true // or false
  }
})
```
</details>

<details>
<summary><strong>🛒 Order Message</strong></summary>

```javascript
await sock.sendMessage(jid, {
  order: {
    orderId: '123xxx',
    thumbnail: fs.readFileSync('preview.jpg'),
    itemCount: '123',
    status: 'INQUIRY', // INQUIRY || ACCEPTED || DECLINED
    surface: 'CATALOG',
    message: 'Here is order message',
    orderTitle: 'Here is title order',
    sellerJid: '628xxx@s.whatsapp.net'',
    token: 'token_here',
    totalAmount1000: '300000',
    totalCurrencyCode: 'IDR'
  }
})
```
</details>

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
```

```javascript
// Poll results (snapshot)
await sock.sendMessage(jid, {
  pollResult: {
    name: 'Favorite color?',
    values: [['Red', 10], ['Blue', 20]] // [option, votes]
  }
});
```
</details>

<details>
<summary><strong>🛍️ Product Message</strong></summary>

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
<summary><strong>💳 Payment Message</strong></summary>

```javascript
await sock.sendMessage(jid, {
  payment: {
    note: 'Here is payment message',
    currency: 'USD', // optional 
    offset: 0, // optional
    amount: '100000', // optional
    expiry: 0, // optional
    from: '628xxx@s.whatsapp.net', // optional
    image: { // optional
      placeholderArgb: "your_background", // optional
      textArgb: "your_text",  // optional
      subtextArgb: "your_subtext" // optional
    }
  }
})
```
</details>


<details>
<summary><strong>📜 Payment Invite Message</strong></summary>

```javascript
await sock.sendMessage(jid, { 
  paymentInvite: {
    type: 1, // 1 || 2 || 3
    expiry: 0 
  }   
})
```
</details>


<details>
<summary><strong>👤 Channel Admin Invite</strong></summary>

```javascript
await sock.sendMessage(jid, {
  adminInvite: {
    jid: '172xxx@newsletter',
    name: 'Newsletter Title', 
    caption: 'Undangan admin channel saya',
    expiration: 86400,
    jpegThumbnail: fs.readFileSync('preview.jpg') // optional
  }
})
```
</details>


<details>
<summary><strong>👥 Group Invite Message</strong></summary>

```javascript
await sock.sendMessage(jid, {
  groupInvite: {
    jid: '123xxx@g.us',
    name: 'Group Name!', 
    caption: 'Invitation To Join My Whatsapp Group',
    code: 'xYz3yAtf...', // code invite link
    expiration: 86400,
    jpegThumbnail: fs.readFileSync('preview.jpg') // optional            
  }
})
```
</details>

<details>
<summary><strong>🔢 Phone Number Message</strong></summary>

```javascript
// Request phone number
await sock.sendMessage(jid, {
  requestPhoneNumber: {}
})
```
```javascript
// Share phone number
await sock.sendMessage(jid, {
  sharePhoneNumber: {}
})
```
</details>

<details>
<summary><strong>↪️  Reply Button Message</strong></summary>

```javascript
// Reply List Message
await sock.sendMessage(jid, {
  buttonReply: {
    name: 'Hii',
    description: 'description', 
    rowId: 'ID'
  }, 
  type: 'list'
})
```

```javascript
// Reply Button Message
await sock.sendMessage(jid, {
  buttonReply: {
    displayText: 'Hii', 
    id: 'ID'
  }, 
  type: 'plain'
})
```

```javascript
// Reply Template Message
await sock.sendMessage(jid, {
  buttonReply: {
    displayText: 'Hii',
    id: 'ID',
    index: 1 // number id button reply
  }, 
  type: 'template'
})
```

```javascript
// Reply Interactive Message
await sock.sendMessage(jid, {
  buttonReply: {
    body: 'Hii', 
    nativeFlows: {
      name: 'menu_options', 
      paramsJson: JSON.stringify({ id: 'ID', description: 'description' }) 
      version: 1 // 2 | 3
    }
  }, 
  type: 'interactive'
})
```
</details>

<details>
<summary><strong>#️⃣ Status Mentions Message</strong></summary>

```javascript
await sock.sendStatusMentions(jid, {
  image: {
    url: 'https://example.com/image.jpg'
  }, 
  caption: 'Nice day!'
})
```
</details>

<details>
<summary><strong>📸 Album Message</strong></summary>

```javascript
await sock.sendAlbumMessage(jid,
  [{
    image: { url: 'https://example.com/image.jpg' },
    caption: 'Hello World'
  },
  {
    image: fs.readFileSync('image.jpg'), 
    caption: 'Hello World'
  },
  {
    video: { url: 'https://example.com/video.mp4' },
    caption: 'Hello World'
  },
  {
    video: fs.readFileSync('video.mp4'),
    caption: 'Hello World'
  }],
{ quoted: message, delay: 3000 })
```
</details>

<details>
<summary><strong>🛍️ Product Message</strong></summary>

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

<br>

> This message button may not work if WhatsApp prohibits the free and open use of the message button. Use a WhatsApp partner if you still want to use the message button.

<details>
<summary>&nbsp;&nbsp;&nbsp;&nbsp;<strong>Headers Type</strong></summary>

```javascript
// Button Headers Text
await sock.sendMessage(jid, {
  text: 'Choose an option:',
  buttons: buttonParams,
  footer: '© WhatsApp Baileys'
});
```

```javascript
// Button Headers Image
await sock.sendMessage(jid, {
  image: fs.readFileSync('image.jpg'),
  caption: 'Choose an option:',
  buttons: buttonParams,
  footer: '© WhatsApp Baileys'
});
```

```javascript
// Button Headers Video
await sock.sendMessage(jid, {
  video: fs.readFileSync('video.mp4'),
  caption: 'Choose an option:',
  buttons: buttonParams,
  footer: '© WhatsApp Baileys'
});
```

```javascript
// Button Headers Location
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
</details>

<details>
<summary>&nbsp;&nbsp;&nbsp;&nbsp;<strong>Button Params</strong></summary>

```javascript
// Button Params Default
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

```javascript
// Button Params NativeFlow
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
</details>

<details>
<summary><strong>🎭 List Messages </strong></summary>

```javascript
// Single Select
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

```javascript
// Product List
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

<br>

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

<br>

### 🔒 Privacy
<details>
<summary><strong>🚫 Block/Unblock User</strong></summary>

```javascript
await sock.updateBlockStatus(jid, 'block'); // or 'unblock'
```
</details>

<br>

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

<br>

## ⚠️ Disclaimer
This project is **not affiliated** with WhatsApp/Meta. Use at your own risk.  
Refer to [WhatsApp's Terms](https://www.whatsapp.com/legal) for compliance.

<br>

### 🔗 Full Documentation
Explore all features in the **[Baileys GitHub Wiki](https://github.com/whiskeysockets/baileys/wiki)**