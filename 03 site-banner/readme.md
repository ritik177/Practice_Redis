# 03 Site Banner - Redis Practice

Is project me hum ek simple **site banner API** banate hain jisme banner message Redis ke andar store hota hai. Ye example Redis ke basic string operations ko real-world style use case ke through samjhata hai.

Example use case:

Ek website ke top par banner dikhana hai:

```txt
Big Sale is live!
```

Admin panel ya backend se banner update hoga, aur website baar-baar database hit karne ke bajay Redis se fast message read karegi.

---

## What Are We Building?

Ye ek small Express.js server hai jisme 4 APIs hain:

| Method | Route | Kaam |
| --- | --- | --- |
| `POST` | `/banner` | Redis me banner message save/update karta hai |
| `GET` | `/banner` | Redis se current banner message read karta hai |
| `DELETE` | `/banner` | Redis se banner delete karta hai |
| `GET` | `/banner/exists` | Check karta hai banner Redis me available hai ya nahi |

Redis key:

```txt
app:banner
```

Is key ke andar banner ka message store hota hai.

---

## Why Are We Using Redis Here?

Banner message ek aisa data hai jo:

- frequently read hota hai
- rarely update hota hai
- fast access chahiye hota hai
- har page load par dikh sakta hai

Agar har request par normal database se banner fetch karenge, to unnecessary database load badhega. Redis in-memory database hai, isliye reads bahut fast hoti hain.

Redis ka use karne ke benefits:

- **Fast read/write**: Data memory me store hota hai.
- **Simple key-value storage**: Banner jaise small config data ke liye perfect.
- **Database load kam hota hai**: Website baar-baar main DB ko hit nahi karti.
- **Real-world caching pattern samajh aata hai**: Ye same pattern feature flags, app settings, maintenance message, popup text, etc. me use hota hai.

---

## Project Structure

```txt
03 site-banner/
  src/
    index.js
  package.json
  package-lock.json
  readme.md
  .gitignore
```

Important files:

- `src/index.js`: Express server and Redis logic.
- `package.json`: Dependencies and scripts.
- `readme.md`: Study guide and setup notes.

---

## Tech Stack

- **Node.js**: JavaScript runtime
- **Express.js**: API server banane ke liye
- **Redis**: Banner message store karne ke liye
- **ioredis**: Node.js se Redis connect karne ke liye

Note: `package.json` me `mongoose` dependency bhi hai, lekin current project code me MongoDB ka use nahi ho raha. Is lesson ka main focus Redis hai.

---

## Prerequisites

Is project ko run karne ke liye system me ye cheezein honi chahiye:

1. Node.js installed
2. npm installed
3. Redis server running locally

Check Node.js:

```bash
node -v
```

Check npm:

```bash
npm -v
```

Check Redis CLI:

```bash
redis-cli --version
```

---

## Install Dependencies

Project folder me jao:

```bash
cd "03 site-banner"
```

Dependencies install karo:

```bash
npm install
```

---

## Start Redis Server

### Option 1: Local Redis

Redis server start karo:

```bash
redis-server
```

Dusre terminal me Redis test karo:

```bash
redis-cli ping
```

Expected output:

```txt
PONG
```

Agar `PONG` aa raha hai, Redis sahi se running hai.

### Option 2: Custom Redis URL

By default app ye Redis URL use karta hai:

```txt
redis://localhost:6379
```

Code:

```js
const redis = new Redis(process.env.REDIS_URL || "redis://localhost:6379");
```

Agar aap remote Redis ya custom port use kar rahe ho, to `REDIS_URL` environment variable set kar sakte ho:

```bash
REDIS_URL="redis://localhost:6379" npm run dev
```

---

## Run The Project

Server start karo:

```bash
npm run dev
```

Expected output:

```txt
server is running on port: 3000
```

API base URL:

```txt
http://localhost:3000
```

---

## API Testing

APIs ko aap `curl`, Postman, Thunder Client, ya Insomnia se test kar sakte ho.

### 1. Create Or Update Banner

Request:

```bash
curl -X POST http://localhost:3000/banner \
  -H "Content-Type: application/json" \
  -d '{"message":"Welcome to Redis Practice!"}'
```

Response:

```json
{
  "success": true
}
```

Redis ke andar ye value save ho jayegi:

```txt
key: app:banner
value: Welcome to Redis Practice!
```

### 2. Get Banner

Request:

```bash
curl http://localhost:3000/banner
```

Response:

```json
"Welcome to Redis Practice!"
```

### 3. Check Banner Exists

Request:

```bash
curl http://localhost:3000/banner/exists
```

Response:

```json
{
  "exists": true
}
```

### 4. Delete Banner

Request:

```bash
curl -X DELETE http://localhost:3000/banner
```

Response:

```json
{
  "success": true
}
```

Ab agar exists API call karoge:

```bash
curl http://localhost:3000/banner/exists
```

Response:

```json
{
  "exists": false
}
```

---

## Verify Data Directly In Redis

Redis CLI open karo:

```bash
redis-cli
```

Banner value check karo:

```bash
GET app:banner
```

Key exists check karo:

```bash
EXISTS app:banner
```

Key delete karo:

```bash
DEL app:banner
```

Redis se bahar aane ke liye:

```bash
exit
```

---

## Code Explanation

### Express App Setup

```js
import express from "express";
import Redis from "ioredis";

const app = express();

app.use(express.json());
```

Yaha Express app create hoti hai aur `express.json()` middleware request body me JSON data read karne ke liye use hota hai.

Example POST body:

```json
{
  "message": "Welcome to Redis Practice!"
}
```

### Redis Connection

```js
const redis = new Redis(process.env.REDIS_URL || "redis://localhost:6379");
```

Yaha app Redis se connect hoti hai.

- Agar `REDIS_URL` environment variable available hai, app usko use karegi.
- Agar nahi hai, app local Redis use karegi: `redis://localhost:6379`.

### Redis Key

```js
const BANNER_KEY = "app:banner";
```

Ye fixed Redis key hai jisme site banner ka message store hota hai.

Naming pattern:

```txt
app:banner
```

Colon-based naming Redis me common hai. Isse keys readable aur grouped lagti hain.

### POST `/banner`

```js
app.post("/banner", async(req, res)=>{
    await redis.set(BANNER_KEY, req.body.message || "Welcome to out Redis Practice course");
    res.json({success: true})
})
```

Ye endpoint Redis me banner message set karta hai.

Redis command used:

```bash
SET app:banner "message"
```

Important points:

- `req.body.message` se message read hota hai.
- Agar message nahi bheja, default message save hota hai.
- `redis.set()` existing value ko overwrite kar deta hai.

### GET `/banner`

```js
app.get("/banner", async(req, res)=>{
    const message = await redis.get(BANNER_KEY);
    res.json(message);
})
```

Ye endpoint Redis se banner message read karta hai.

Redis command used:

```bash
GET app:banner
```

Agar key exist nahi karti, Redis `null` return karega.

### DELETE `/banner`

```js
app.delete("/banner", async(req,res)=>{
    await redis.del(BANNER_KEY);
    res.json({success:true})
})
```

Ye endpoint Redis se banner key delete karta hai.

Redis command used:

```bash
DEL app:banner
```

### GET `/banner/exists`

```js
app.get("/banner/exists", async(req,res)=>{
    const exists = await redis.exists(BANNER_KEY);
    res.json({exists : Boolean(exists)})
})
```

Ye endpoint check karta hai ki Redis me banner key present hai ya nahi.

Redis command used:

```bash
EXISTS app:banner
```

Redis `EXISTS` command:

- `1` return karta hai agar key available hai
- `0` return karta hai agar key available nahi hai

Code me `Boolean(exists)` use karke `true`/`false` response bheja gaya hai.

---

## Request Flow

### When We Set Banner

```txt
Client/Postman
  -> POST /banner
  -> Express server
  -> redis.set("app:banner", message)
  -> Redis stores message
  -> Response: { success: true }
```

### When We Read Banner

```txt
Browser/Frontend
  -> GET /banner
  -> Express server
  -> redis.get("app:banner")
  -> Redis returns message
  -> Response: "Welcome to Redis Practice!"
```

---

## Real-World Use Cases

Ye pattern bahut jagah use hota hai:

- Website announcement banner
- Maintenance mode message
- Festival sale message
- Feature flag on/off value
- App configuration
- Temporary notice
- Homepage popup text
- Admin-controlled UI message

Example:

Admin panel se message update:

```txt
50% discount live till midnight!
```

Frontend har page load par:

```txt
GET /banner
```

Aur Redis se fast response mil jata hai.

---

## Why Not Store This Only In A Normal Database?

Normal database me store karna galat nahi hai. Production app me usually source of truth database hota hai. Lekin banner jaise data ko Redis me rakhne ka reason fast read aur low database load hota hai.

Common production pattern:

```txt
Admin updates banner
  -> Save in database
  -> Also update Redis cache

Frontend reads banner
  -> Read from Redis
  -> Very fast response
```

Is practice project me hum directly Redis me store kar rahe hain, taaki Redis commands clearly samajh aayein.

---

## Redis Commands Learned In This Project

| Redis Command | Code Method | Purpose |
| --- | --- | --- |
| `SET key value` | `redis.set()` | Value save/update karna |
| `GET key` | `redis.get()` | Value read karna |
| `DEL key` | `redis.del()` | Key delete karna |
| `EXISTS key` | `redis.exists()` | Key present hai ya nahi check karna |

---

## Common Errors And Fixes

### Error: Redis connection refused

Possible error:

```txt
ECONNREFUSED 127.0.0.1:6379
```

Reason:

Redis server running nahi hai.

Fix:

```bash
redis-server
```

Then test:

```bash
redis-cli ping
```

Expected:

```txt
PONG
```

### Error: Port 3000 already in use

Reason:

Port `3000` par already koi server running hai.

Fix:

- Pehle running server stop karo using `Ctrl + C`
- Ya code me port change karo

Current code:

```js
app.listen(3000, ()=>{
    console.log("server is running on port: 3000");
})
```

### GET `/banner` returns `null`

Reason:

Banner abhi set nahi hua ya delete ho chuka hai.

Fix:

Pehle `POST /banner` call karo.

---

## Practice Tasks

Khud try karne ke liye:

1. Banner message set karo.
2. Redis CLI se `GET app:banner` run karke value verify karo.
3. Banner delete karo.
4. `GET /banner` call karke `null` response observe karo.
5. `/banner/exists` endpoint se true/false behavior check karo.
6. Default message test karo by sending empty POST body:

```bash
curl -X POST http://localhost:3000/banner \
  -H "Content-Type: application/json" \
  -d '{}'
```

---

## Possible Improvements

Future me is project ko aur improve kar sakte hain:

- Banner message ke saath `isActive` flag add karna
- Banner expiry ke liye Redis `EX` option use karna
- Multiple banners store karna
- Admin authentication add karna
- Frontend page banana jahan banner display ho
- Database + Redis cache pattern implement karna
- Error handling add karna with `try/catch`
- Port ko environment variable se configurable banana

Example with expiry:

```js
await redis.set(BANNER_KEY, message, "EX", 3600);
```

Isse banner 1 hour ke baad automatically expire ho jayega.

---

## Quick Revision

- Redis ek fast in-memory data store hai.
- Site banner jaise frequently-read data ke liye Redis useful hai.
- `SET` se banner save hota hai.
- `GET` se banner read hota hai.
- `DEL` se banner delete hota hai.
- `EXISTS` se key present hai ya nahi check hota hai.
- Express API frontend/admin panel aur Redis ke beech bridge ka kaam karti hai.

---

## Final Mental Model

Is project ko aise samjho:

```txt
Redis = fast memory storage
Express = API layer
Banner = small app setting
Frontend = banner read karne wala client
Admin = banner update karne wala user
```

Flow:

```txt
Admin sets banner -> Express API -> Redis
User opens website -> Express API -> Redis -> Banner shown
```
