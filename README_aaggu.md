# aaggu.com

A real-time web application built with Node.js, featuring WebSocket communication, a RESTful routing layer, and a SQLite-backed user database.

---

## Tech Stack

- **Runtime:** Node.js
- **Server:** Express.js (`server.js`)
- **Real-time:** WebSocket (`/websocket`)
- **Database:** SQLite (`users.db`)
- **Frontend:** HTML, CSS, JavaScript (`/public`)

---

## Project Structure

```
aaggu.com/
├── public/          # Static frontend assets (HTML, CSS, JS)
├── routes/          # Express route handlers
├── websocket/       # WebSocket server logic
├── utils/           # Helper/utility functions
├── db/              # Database access layer
├── server.js        # App entry point
├── users.db         # SQLite database
└── package.json
```

---

## Getting Started

### Prerequisites

- Node.js v18+
- npm

### Installation

```bash
git clone https://github.com/sskuntal29/aaggu.com.git
cd aaggu.com
npm install
```

### Run the server

```bash
node server.js
```

The app will start on `http://localhost:<PORT>` (check `server.js` for the configured port).

---

## Features

- Real-time communication via WebSocket
- User data persistence with SQLite
- Modular route structure with Express
- Static frontend served from `/public`

---

## Contributing

Pull requests are welcome. For major changes, please open an issue first.

---

## License

[MIT](https://choosealicense.com/licenses/mit/)
