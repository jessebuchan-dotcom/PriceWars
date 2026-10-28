# PRICE WARS — The Supermarket Showdown

**A classroom multiplayer business simulation for Cambridge International AS & A2 Business (9609).**

Students compete as supermarkets for a common pool of virtual customers. Prices, advertising, staffing and stock-management decisions influence market share, cash flow and operating profit. A teacher can start, pause and end trading, broadcast announcements, and introduce industry-wide market shocks. Students join through a six-character room code or a QR code; no personal accounts are required.

## What is included

- **Live multiplayer:** Socket.IO sends synchronised, server-authoritative market updates to every device.
- **Three responsive views:** teacher dashboard, student supermarket console and projector screen.
- **Timed trading periods:** choose 5–30-minute games and 5-, 10- or 15-second trading periods.
- **Teacher market shocks:** demand boom, recession, wholesale cost shock, delivery delay, wage increase, enhanced advertising, cost-of-living squeeze and labour shortage.
- **Business arithmetic:** revenue, cost of goods sold, wages, advertising, shipping, cash, inventory assets, customer reputation and cumulative operating profit.
- **Classroom features:** QR joining, automatic team allocation, roster reassignment, optional computer-controlled rivals, reconnectable student sessions, protected teacher access, CSV/JSON exports and a projection display.
- **Persistence:** local JSON file during development or Firestore when deployed.
- **Standalone game engine:** separated from the interface so additional business simulations can reuse the classroom architecture.

## Quick local start

Requirements: Node.js 22 or later and npm.

```bash
npm install
cp .env.example .env
npm run dev
```

Open **http://localhost:5173**. Choose **Teacher setup**, enter your PIN from `.env`, and create a classroom. Join from a second tab or another browser at `/join/ROOMCODE`.

For a single-process built version: run `npm run build`, then `npm start`, and open **http://localhost:3001**. This works on Windows, macOS and Linux, using the local persistence backend for testing.

**Note:** A QR code pointing to `localhost` works only on the same device. For other devices, use a publicly deployed address, or configure a local network hostname/IP and allow classroom Wi-Fi access to the development service.

## Main game mechanics

- **Pricing:** $5.00 to $20.00 per unit. Base wholesale cost is $6.00 per unit. A lower price increases shoppers' propensity to visit, but may reduce the seller's operating margin.
- **Advertising:** $0–$45 per trading period. It increases relative market attraction (with diminishing returns) and is fully expensed each trading period.
- **Staffing:** 1–6 staff. More staff increase the number of customers who can be served; wages are charged each period.
- **Inventory:** 600 initial units. Additional orders of 200, 400 or 700 units cost the current wholesale price plus a $45 shipping fee, subject to available cash and a 2,500-unit total warehouse capacity. Standard lead time is three market periods and ordering has a three-period cooldown.
- **Demand:** simulated customer visits are distributed across supermarkets based on retail prices, advertising and customer reputation. Sales are limited by stock and staffing capacity. Market-wide demand varies with average price and teacher events.
- **Accounting:** operating profit is *revenue minus cost of goods sold, wages, advertising and shipping*, **not** cash on hand. Unused inventory and in-transit purchases remain inventory assets. Stock-purchase payments reduce cash immediately.
- **End results:** the main leaderboard ranks supermarkets by profit; market share and satisfaction are separate awards. Computer-controlled supermarkets play with imperfect strategies.

A market period is a **compressed trading period**, not necessarily a literal real-world hour. This is a classroom economic model: the algorithms illustrate trade-offs rather than claim empirically calibrated price elasticity or predict actual consumer behaviour.

## Roles and room joining

- Teachers create a room using a server-side PIN (`TEACHER_SETUP_PIN`). The API returns a high-entropy private teacher access token saved in the teacher's browser. Keep a private backup of this token if you need to switch devices. The student joining URL does not contain teacher credentials.
- Students join with a nickname and receive a random session token, saved locally so they can refresh and reconnect. The server stores only a SHA-256 digest of student and teacher tokens.
- Students can propose and apply team-wide changes. **The last submitted decision wins**, so members of the same team should agree before pressing *Apply strategy*.
- The public projector screen requires only the room code; it shows the standings, totals and market history but cannot control the game.
- The teacher can reassign or remove players in the lobby. Joining is closed once the teacher starts, but existing student sessions can reconnect.
- Empty supermarkets are represented by computer-controlled competitors once the game starts. A class can therefore use fewer players than supermarkets.

## Cloud Run + Firestore deployment

This application runs as **one authoritative Node.js/Socket.IO process**. A single Cloud Run instance can handle a normal classroom, provided it is configured appropriately.

1. Create a Google Cloud project with billing and APIs required for Cloud Run, Cloud Build, Artifact Registry and Firestore. Create a **Firestore database in Native mode**. Set up a billing budget and alerts.
2. Ensure your Cloud Run service identity has a Firestore-access role appropriate for application reads/writes (e.g. Cloud Datastore User). Use default application credentials through the service account; **do not check service-account JSON or API keys into the project**.
3. Choose a strong `TEACHER_SETUP_PIN` (six characters minimum, ideally a randomly generated longer secret). Configure it as a **Cloud Run Secret Manager secret or protected environment variable**, not in frontend source.
4. Deploy from the repository root using the included `Dockerfile` and Google Cloud Run's source-based deployment. Configure `NODE_ENV=production`, `DATA_BACKEND=firestore` and `TEACHER_SETUP_PIN` through Cloud Run environment/secrets. Cloud Run automatically exposes its assigned `PORT` (the server listens on that port).
5. **For v1, configure Cloud Run maximum instances = 1**, with minimum instances = 1 if you want the room to remain responsive between teacher actions. Set request timeout to 3600 seconds and concurrency to at least 100. Use session affinity where available. Keep deployment revisions and Cloud Run service scaling to one active instance during a class.
6. Copy the Cloud Run URL into your browser. The student QR code will automatically use the hosted URL.
7. Test joining with several independent devices, starting/pausing, session recovery, browser refreshes and exports **before** introducing the game to a class.

### Important cloud limitations

- This v1 relies on **one authoritative Node process** for active market state. Its Firestore snapshot is persistent, but **it is not a multi-instance distributed engine**. Do not increase Cloud Run maximum instances above one without introducing a distributed game coordinator/Redis and a cross-instance Socket.IO adapter. During a revision rollout, route the active class to a single revision and do not deploy while the lesson is running.
- Cloud Run WebSocket connections are subject to request timeouts. Socket.IO automatically reconnects, including after a 60-minute timeout, and retrieves fresh state. In the event of a server restart, rooms that were trading are deliberately recovered as **paused** so the teacher can resume, rather than applying erroneous offline ticks.
- Cloud Run and Firestore incur usage-based charges. A minimum warm instance may incur idle charges; set a cloud budget. For demonstrations, you may set minimum instances to zero, though cold starts and sleeping processes may affect responsiveness.
- A locked-down school network may block WebSockets; Socket.IO has long-polling fallback. The QR code still requires devices to be able to reach the application URL.
- Store only classroom nicknames, and do not ask students to enter sensitive personal information. For school use, review data retention, safeguarding and your school's privacy requirements. Classroom room data persists until manually deleted from Firestore/local storage. Do not retain it indefinitely.

## Google AI Studio

AI Studio Build mode supports **Import from GitHub**. Upload these project files to a private GitHub repository and import it through **Build → Add files (+) → Import from GitHub**. Request changes to the UI and content while preserving the Node server, Socket.IO protocol and server-side market engine.

**Suggested AI Studio instruction after importing:**

> This is an existing, working full-stack React/TypeScript and Node/Socket.IO project called Price Wars. Preserve its game mechanics, authoritative server, teacher/student role security, Firestore persistence, responsive design and all three views. Install the npm dependencies, run the project's TypeScript checks and tests, and address any integration problems. Do not replace the simulation with static sample data, a client-only demo or AI-generated financial results. Keep market calculations on the server. When ready, help me deploy to Cloud Run with a single instance, Firestore and a securely stored teacher PIN. Do not expose private tokens to students or display pages.

A full Firebase web SDK integration is not required for the client: Firestore is used securely by the server, while Socket.IO handles real-time browser connections.

## Testing

```bash
npm run check
npm test
npm run build
```

See `tests/` for deterministic financial and behavioural unit tests. Before classroom launch, carry out a real multi-browser end-to-end session from at least two devices.

## Extensions

The project intentionally separates the common classroom and teacher management from the business engine. Next steps could include sector choices, student portfolios, teacher-assigned roles, AI rival difficulty settings, timed market reports, and additional games using the same joining and teacher dashboard infrastructure.
