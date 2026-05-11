# ♻️ Kasa |Smart Recycling Ecosystem

![Kasa Architecture](./KasaArch.png) 
> **Architecture Overview:** A decentralized cloud-native system bridging physical edge interfaces with real-time logic and financial services.

### 🚀 Transforming Bottle Recycling into a Seamless Digital Experience
[cite_start] Kasa is a distributed, cloud-based system designed to revolutionize the bottle recycling industry in Israel[cite: 5]. [cite_start]By integrating advanced edge interfaces with real-time communication, the project replaces outdated paper vouchers with secure, instant, and transparent digital credits.

## 💡 The Vision
[cite_start]The recycling process should be as easy as "Scan & Go"[cite: 8]. [cite_start]Kasa creates a continuous, secure connection (Handshake) between the user and the recycling machine interface, ensuring instant gratification through immediate digital credit[cite: 8].

## ✨ Core Capabilities
* [cite_start]**Real-time Handshake:** Establishes a continuous and secure WebSocket connection between the User App and the Machine Interface for interactive session management[cite: 8, 13].
* [cite_start]**Atomic Financial Integrity:** All transactions are processed as **Atomic Transactions**, ensuring that wallet updates and ledger logging occur as a single, indivisible unit[cite: 24].
* [cite_start]**Hardware-Agnostic Architecture:** Designed as a flexible **Logic Layer**, the system is capable of receiving signals from various sensors, such as weight or volume, without requiring core backend modifications[cite: 25, 26].

## 💡 Implementation Spotlight
While the full source code is private, here are key architectural patterns used to ensure system reliability:

### 1. Atomic Financial Transactions (Firestore)
[cite_start]To prevent race conditions and ensure 100% balance accuracy, Kasa utilizes Firestore's atomic transactions with integer-based calculations to avoid floating-point errors[cite: 10, 24]:

```typescript
[cite_start]// Ensuring financial integrity with atomic updates [cite: 24]
await db.runTransaction(async (transaction) => {
  const userRef = db.collection('users').doc(userId);
  const userDoc = await transaction.get(userRef);
  
  if (!userDoc.exists) throw new Error("User not found");

  [cite_start]// Using integer cents to prevent precision issues [cite: 10, 24]
  const newBalanceCents = userDoc.data().balanceCents + amountCents;
  transaction.update(userRef, { balanceCents: newBalanceCents });
  
  [cite_start]// Log entry in the ledger [cite: 24]
  const logRef = db.collection('ledger').doc();
  transaction.set(logRef, { userId, amountCents, timestamp: FieldValue.serverTimestamp() });
});

```

### 2. Real-time Session Synchronization (Socket.io)

Managing isolated rooms for secure, low-latency communication between users and machines:

```typescript
io.on("connection", (socket) => {
  socket.on("user:handshake", ({ machineId }) => {
    const roomId = `session_${machineId}`;
    [cite_start]socket.join(roomId); // Isolated session rooms [cite: 13]
    socket.to(roomId).emit("machine:start_session", { userId: socket.userId });
  });
});

```

## 🛠 Tech Stack

| Layer | Technologies |
| --- | --- |
| **Frontend** | React Native (Expo), TypeScript, Context API, Tanstack Query 

 |
| **Backend** | Node.js, Express, Socket.io, JWT Authentication |
| **Cloud & DB** | GCP (Cloud Run, Secret Manager), Firestore, Firebase RTDB 

 |
| **DevOps** | GitHub Actions, Docker, Google Cloud Build |

## 📊 Engineering Excellence & Optimization

* 
**Cold Start Mitigation:** Implemented a **Warm-up Call** mechanism to wake the Cloud Run instance upon app launch, ensuring sub-second response times for the initial scan.


* 
**System Resilience:** Developed a **60-second Grace Period** for automatic session recovery, preventing data loss during network fluctuations.


* 
**Hardware Resilience:** Integrated specialized hooks to handle hardware-specific camera issues (e.g., Samsung API) via a managed hard-restart flow.


* 
**Precision Engineering:** Strictly using **Integer Cents** for all currency operations to eliminate floating-point precision issues.



## 📈 Project Status

The system is currently **Production Ready** (v1.4), having successfully passed full MVP testing with performance benchmarks of sub-1.5 second response times.

---

Developed by **Roi Meshulam** | May 2026 

```

```
