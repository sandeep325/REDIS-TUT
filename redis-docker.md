![Redis](https://img.shields.io/badge/Redis-FF3C00?style=for-the-badge&logo=redis&logoColor=white) ![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white) ![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)

# REDIS + DOCKER + NODE.JS
## COMPLETE NOTES

## What is Redis?

Redis is an in-memory database used for fast data storage and retrieval.

It stores data in RAM instead of disk, so it is extremely fast.

## Why Redis is fast

Redis stores data primarily in memory (RAM) rather than reading from disk each time.

That makes operations often complete in:

* microseconds
* low milliseconds


## WHY WE USED DOCKER FOR REDIS

Redis does not officially support native installation on Windows properly.
So, for learning and development purposes, we used Docker to run Redis inside a containerized environment.

Docker provides an isolated lightweight environment where applications can run consistently across systems.

---

## WHAT IS DOCKER?

Docker is a containerization platform used to package and run applications inside containers.

Containers are lightweight isolated environments that include:

* application
* dependencies
* runtime

This helps developers run software consistently on any machine.

---

## WHY DO WE USE DOCKER FOR REDIS?

We use Docker for Redis because:

1. Easy setup
   Redis can start with a single command.

2. No manual installation
   No need to install Redis directly on Windows.

3. Production-like environment
   Most real-world companies run Redis inside containers.

4. Isolation
   Redis runs independently without affecting the local system.

5. Portability
   Same setup works on any developer machine.

---

##  DOCKER + WSL ISSUE WE FACED  (IF)

Initially Docker commands failed with error:

failed to connect to the docker API at npipe:////./pipe/dockerDesktopLinuxEngine

Reason:
Docker Desktop requires WSL2 (Windows Subsystem for Linux) on Windows.

Problem was:

* WSL outdated or not installed properly.

---

 # HOW WE FIXED THE ISSUE

Step 1:
Opened PowerShell as Administrator.

Step 2:
Ran command:

wsl --update

Step 3:
Restarted the system.

Step 4:
Opened Docker Desktop again.

After restart Docker Engine started successfully.

---

**VERIFY DOCKER IS RUNNING**

Command:

```clipboard
docker ps
```

If no error comes:
Docker Engine is running successfully.

---

**RUN REDIS CONTAINER**

Command:

```clipboard
docker run --name redis-demo -p 6379:6379 -d redis
```

*Explanation:*

- `docker run` → creates and starts container
- `--name redis-demo` → container name
- `-p 6379:6379` → maps Redis port to local machine
- `-d` → runs container in detached/background mode
- `redis` → official Redis image from Docker Hub

---

**VERIFY REDIS CONTAINER**

Run:
```clipboard
docker ps
```

Redis container should appear in running containers list.

`Example:`
redis-demo container running on port 6379.

---

**OPEN REDIS CLI**

*Command:*

```clipboard
docker exec -it redis-demo redis-cli
```

*Explanation:*

- `docker exec` → runs command inside container
- `-it` → interactive terminal mode
- `redis-demo` → container name
- `redis-cli` → Redis command line interface

---

**TEST REDIS WORKING**

*Inside redis-cli run:*
```clipboard
ping
```

*Expected Output:*

- `PONG`

Meaning:
Redis server is working successfully.

---


## IMPORTANT DOCKER COMMANDS ##

*See running containers:*  
```clipboard
 docker ps
   ```

*See all containers:*
```clipboard
docker ps -a
```

*Stop Redis container:*
```clipboard
docker stop redis-demo
```

*Start Redis container:*
```clipboard
docker start redis-demo
```

*Remove Redis container:*
```clipboard
docker rm redis-demo
```

---
**REDIS DEFAULT PORT**  `6379`

---

## HOW NODE.JS CONNECTS TO REDIS ##

Node.js application connects using:

redis://localhost:6379

*Example:*
```clipboard
const redis = require("redis");

const client = redis.createClient({
url: "redis://localhost:6379"
});

await client.connect();
```

---


**INSTALL REDIS PACKAGE IN NODE.JS**
```clipboard
npm install redis
```

---

**PROJECT SETUP**

From the project root, install dependencies and start the Node.js server:

```bash
npm install
node server.js
```

The API runs on port `3000` by default:

```text
http://localhost:3000
```

### Available API endpoints

* `GET /` → health check
* `POST /set` → save a key/value pair
* `GET /get/:key` → retrieve a value by key

**Test API**
**1.** Example `POST /set` request body:

```json
{
  "key": "name",
  "value": "Admin"
}
```
 Example `POST /set` Response body:
```json
{
  "success": true,
  "message": "Data stored in Redis"
}
```

**2.** Example `GET /get/:key` request:
```json
curl http://localhost:3000/get/name
```

 Example `GET /get/:key` Response body:
```json
{
  "success": true,
  "data": "Admin"
}
```
---

**HOW DATA FLOW WORKS**

```text
Node.js Application
   |
   v
localhost:6379
   |
   v
Docker Container
   |
   v
Redis Server Running
```

This graph shows the request flow from the Node.js application to Redis:

* The application sends Redis commands to `localhost:6379`.
* Docker maps that port to the Redis container.
* The Redis server inside the container receives and processes the commands.

This makes Redis an external service for Node.js, with data managed independently in the Redis process.

---

**ADVANTAGES OF USING REDIS**

1. Extremely fast
2. In-memory storage
3. Used for caching
4. Session management
5. Real-time applications
6. Queue management
7. Rate limiting

---

COMMON REAL-WORLD USE CASES

* API caching
* OTP storage
* Session storage
* Chat applications
* Leaderboards
* Background jobs
* Real-time analytics

---

## IMPORTANT NOTE:- ##

“We used Docker to run Redis because Redis works more reliably in a Linux-based container environment on Windows systems. Docker also provides an easy, isolated, and production-like setup for development and practice.”

---

## COMPLETE FLOW SUMMARY ##

1. Installed Docker Desktop
2. Faced Docker Engine issue
3. Updated WSL using:
   wsl --update
4. Restarted system
5. Opened Docker Desktop
6. Verified Docker using:
   docker ps
7. Started Redis container
8. Verified Redis container running
9. Opened Redis CLI
10. Tested Redis using:
    ping
11. Connected Redis with Node.js application

---


**QUESTION**

If Node.js server restarts, will Redis data be deleted?

ANSWER

No.

Restarting the Node.js server does NOT delete Redis data.

Reason:
Node.js application and Redis server are separate processes/services.

Redis stores data independently in its own memory.

---

EXPLANATION

Architecture:

Node.js Server
|
Redis Client
|
Redis Server

Node.js only connects to Redis.

Actual data is stored inside Redis server memory, not inside Node.js application memory.

---
IMPORTANT UNDERSTANDING

Node.js Restart
→ Does NOT remove Redis data

Redis Restart
→ May remove data depending on persistence configuration

Container Remove
→ Data deleted

---

DOCKER EXAMPLE

If Redis container is only stopped:

docker stop redis-demo

and restarted:

docker start redis-demo

Data usually remains.

BUT IF CONTAINER IS REMOVED

docker rm redis-demo

Then Redis data is lost because container is deleted.

---


WHY DATA SURVIVES NODE.JS RESTART

Because:

Redis is an external cache/database service
Node.js is only client application
Data lifecycle depends on Redis server, not Node.js process

------

IMPORTANT INTERVIEW LINE

“Node.js restart does not affect Redis data because Redis runs as a separate service. The application only connects to Redis, while the actual cached data is maintained independently inside the Redis server.”

------

SCENARIO 1

Question:

If Node.js application restarts on AWS, will Redis data be lost?

ANSWER

No.

Restarting the Node.js application does NOT delete Redis data.

Reason:

Node.js application and Redis are separate services.
Redis stores data independently.

------


ARCHITECTURE

Node.js Application (EC2/ECS/Lambda)
|
AWS Redis (Elasticache Redis)
|
Data Stored In Redis Memory

------

SCENARIO 2

Question:

If EC2 server restarts, will Redis data be lost?

ANSWER DEPENDS ON REDIS LOCATION

CASE 2:
Using AWS Elasticache Redis (MOST COMMON)

Example:

Node.js app on EC2
Redis on AWS Elasticache

Then:

Restarting EC2 does NOT affect Redis data.

Reason:
Elasticache Redis runs on separate AWS-managed infrastructure.


------

ARCHITECTURE

EC2 (Node.js App)
|
Elasticache Redis
|
Redis Data Independent

------
   **REDIS PERSISTENCE EXPLAINED IN DETAIL**

WHAT IS PERSISTENCE IN REDIS?

Persistence means:

Redis saves in-memory data permanently to disk/storage so that data can survive:

Redis restart
server reboot
crash
power failure

WITHOUT PERSISTENCE

Redis stores data only in RAM.

RAM is temporary memory.

If Redis server stops or crashes:
→ data may be lost.

WITH PERSISTENCE

Redis periodically saves data to disk.

If Redis restarts:
→ data can be restored from saved files.

IMPORTANT UNDERSTANDING

RAM
→ Fast but temporary

Disk/Storage
→ Slower but permanent

Persistence helps Redis recover RAM data after restart.

------

**REDIS TIME EXPIRY (TTL - TIME TO LIVE)**


---

WHAT IS TIME EXPIRY IN REDIS?

Time expiry means:

Redis automatically deletes data after a specific time.

This is called:

* TTL (Time To Live)
* Expiration Time

---

WHY TIME EXPIRY IS USED?

Used for temporary data like:

* OTPs
* login sessions
* API cache
* access tokens
* rate limiting
* temporary verification data

---

EXAMPLE

Store OTP for 60 seconds.

After 60 seconds:
Redis automatically removes it.

---

HOW TO SET EXPIRY IN REDIS

---

METHOD 1: USING EXPIRE COMMAND

STEP 1:
Store data.

SET otp 1234

---

STEP 2:
Set expiry.

EXPIRE otp 60

Meaning:
Delete key after 60 seconds.

---

METHOD 2: SET WITH EX OPTION (MOST COMMON)

SET otp 1234 EX 60

Meaning:

* store data
* expire after 60 seconds

Single command.

---

NODE.JS EXAMPLE

await client.set("otp", "1234", {
EX: 60
});

---

EXPLANATION

"otp"
→ key

"1234"
→ value

EX: 60
→ expiry time in seconds

---

CHECK REMAINING TIME

TTL otp

Example Output:

45

Meaning:
45 seconds remaining before deletion.

---

WHEN TIME EXPIRES

After expiry:

GET otp

Output:

(nil)

Meaning:
Data deleted automatically.

---

IMPORTANT INTERVIEW LINE

“Redis TTL (Time To Live) is used to automatically expire and remove keys after a configured duration.”

---

REAL-WORLD SCENARIOS

---

1. OTP SYSTEM

SET otp:1234 5678 EX 120

Meaning:
OTP valid for 2 minutes.

---

2. USER SESSION

SET session:user1 token123 EX 3600

Meaning:
Session expires after 1 hour.

---

3. API CACHE

SET products_api cachedResponse EX 300

Meaning:
Cache refresh every 5 minutes.

---

4. RATE LIMITING

SET user:count 1 EX 60

Meaning:
Count resets every minute.

---

IMPORTANT UNDERSTANDING

Redis automatically handles cleanup.

No manual delete required.

---

TTL COMMANDS

---

Set Expiry:

EXPIRE key seconds

---

Check Remaining Time:

TTL key

---

Remove Expiry:

PERSIST key

---

See Exact Expiry Time:

EXPIRETIME key

---

NODE.JS PRACTICAL EXAMPLE

---

STORE DATA WITH EXPIRY

app.post("/set", async (req, res) => {

```
const { key, value } = req.body;

await client.set(key, value, {
    EX: 60
});

res.json({
    success: true,
    message: "Data stored with 60 sec expiry"
});
```

});

---

CHECK TTL

app.get("/ttl/:key", async (req, res) => {

```
const ttl = await client.ttl(req.params.key);

res.json({
    ttl
});
```

});

---

IMPORTANT TTL VALUES

TTL Result Meaning:

-2
→ key does not exist

-1
→ key exists but no expiry set

Positive Number
→ remaining seconds

---

INTERVIEW QUESTION

Question:
Why is Redis TTL important?

Answer:
TTL helps automatically clean temporary cache/session data and prevents unnecessary memory usage.

---

IMPORTANT PRODUCTION USE CASE

Example:

API response caching.

First request:

* fetch data from DB
* store in Redis with TTL

Next requests:

* return data from Redis

After TTL expires:

* cache refreshes automatically

---

CACHE FLOW

Client Request
|
Check Redis Cache
|
If Exists:
Return Cached Data
|
If Not Exists:
Fetch From DB
|
Store In Redis With TTL
|
Return Response

---

BEST PRACTICE

Always use TTL for:

* cache
* OTP
* temporary tokens
* rate limiting

to avoid unnecessary memory usage in Redis.

---

## Command Cheatsheet

### Docker commands

Use these Docker commands to install, run, and manage your Redis container on Windows.

**Check Docker version**

```clipboard
docker --version
```

**List running containers**

```clipboard
docker ps
```

**List all containers**

```clipboard
docker ps -a
```

**Download and start Redis container**

```clipboard
docker run --name redis-demo -p 6379:6379 -d redis
```

**Start Redis container**

```clipboard
docker start redis-demo
```

**Stop Redis container**

```clipboard
docker stop redis-demo
```

**Restart Redis container**

```clipboard
docker restart redis-demo
```

**Remove Redis container**

```clipboard
docker rm redis-demo
```

**Remove Redis container forcefully**

```clipboard
docker rm -f redis-demo
```

**Open Redis CLI inside container**

```clipboard
docker exec -it redis-demo redis-cli
```

**View container logs**

```clipboard
docker logs redis-demo
```

**View live container logs**

```clipboard
docker logs -f redis-demo
```

**List Docker images**

```clipboard
docker images
```

**Remove Redis image**

```clipboard
docker rmi redis
```

**Inspect Redis container**

```clipboard
docker inspect redis-demo
```

### Redis CLI commands

Use these Redis CLI commands to test Redis health, store and retrieve data, manage keys, and inspect server state.

**Test Redis server**

```clipboard
PING
```

**Store data**

```clipboard
SET name Sandeep
```

**Get data**

```clipboard
GET name
```

**Delete key**

```clipboard
DEL name
```

**List all keys**

```clipboard
KEYS *
```

**Check if key exists**

```clipboard
EXISTS name
```

**Set expiry on key**

```clipboard
EXPIRE name 60
```

**Store key with expiry**

```clipboard
SET otp 1234 EX 60
```

**Check remaining TTL**

```clipboard
TTL otp
```

**Remove expiry**

```clipboard
PERSIST otp
```

**Show exact expire time**

```clipboard
EXPIRETIME otp
```

**Clear all data**

```clipboard
FLUSHALL
```

**Show Redis info**

```clipboard
INFO
```

**Show memory info**

```clipboard
INFO memory
```

**Check persistence settings**

```clipboard
CONFIG GET save
```

```clipboard
CONFIG GET appendonly
```

**Increment value**

```clipboard
INCR count
```

**Decrement value**

```clipboard
DECR count
```

**Show keyspace info**

```clipboard
INFO keyspace
```

**Switch database**

```clipboard
SELECT 1
```

**Get multiple values**

```clipboard
MGET key1 key2
```

**Store multiple values**

```clipboard
MSET key1 value1 key2 value2
```












