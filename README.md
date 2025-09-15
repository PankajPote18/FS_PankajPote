# FS_PankajPote
# Student Commute Optimizer — FS_PankajPote

A **carpooling and route-sharing application** designed for students.  
Instead of each student commuting individually, the app helps match students traveling along similar routes so they can share rides efficiently, save costs, and promote eco-friendly commuting.

---

##  Tech Stack
- **Backend:** JavaScript (Node.js + Express)  
- **Database:** PostgreSQL with SQL (supports geospatial queries)  
- **Frontend:** React (Map-based UI)  
- **Communication:** WebSockets for real-time chat  

---

##  Major Problems & Solutions

### 1. Privacy of Student Locations  
**Problem:** Students may hesitate to share their exact home location or personal details.  
**Solution:** Anonymous unique usernames and approximate pickup points.  

```pseudo
FUNCTION generateUniqueUsername():
  LOOP until unique username found:
    username = randomString()
    RETURN username

FUNCTION getApproximateLocation(location):
    // Add random noise or snap to nearby public pickup spot for privacy
    approximateLocation = applyGeospatialNoise(location)
    RETURN approximateLocation


2. Trust Between Students

Problem: Students may not trust unknown users for ride-sharing.
Solution: Show a “verified student” badge after college email/ID verification.

FUNCTION verifyStudentEmail(userId, collegeEmail):
    IF sendVerificationEmail(collegeEmail) AND receiveVerificationResponse():
        markUserAsVerified(userId)
        RETURN true
    ELSE
        RETURN false

FUNCTION getUserDisplayInfo(userId):
    user = getUserFromDB(userId)
    displayInfo = {
      username: user.uniqueUsername,
      verifiedBadge: false
    }
    IF user.isVerified:
        displayInfo.verifiedBadge = true
    RETURN displayInfo

3. Liability & Misuse Concerns

Problem: Carpooling may face liability issues in case of accidents or misuse.
Solution: Integrate with university system to apply policies.


3.Carpooling may face liability issues in case of accidents or misuse
Solution : Integrate with university system

FUNCTION integrateWithUniversitySystem(userId):
    userRecord = fetchUserFromUniversityDB(userId)
    IF userRecord.isEnrolled AND userRecord.approvalsAreValid:
        linkUserWithUniversityPolicy(userId)
        enforceCarpoolingRules(userId)
        RETURN true
    ELSE
        denyCarpoolingAccess(userId)
        RETURN false

FUNCTION enforceCarpoolingRules(userId):
    // Apply university policies such as liability disclaimers, emergency contacts
    notifyUserPolicies(userId)
    monitorUsageForAbuse(userId)


Backend : 
DATABASE TABLES:
  Users (id, username, password_hash, home_lat, home_lng, dest_lat, dest_lng, route_polyline)
  Chats (chat_id, user1_id, user2_id)
  Messages (message_id, chat_id, sender_user_id, message_text, timestamp)

// Hash password
FUNCTION hashPassword(password):
  RETURN hashedPassword

// Calculate route polyline between home and destination
FUNCTION calculateRoutePolyline(home, destination):
  CALL API  to get polyline
  RETURN polyline

// API: Register User
POST /register:
  RECEIVE {home_location, destination, password}
  username = generateUniqueUsername()
  passwordHash = hashPassword(password)
  routePolyline = calculateRoutePolyline(home_location, destination)
  INSERT new user with data into Users table
  RESPOND {username: username}

// API: Get Route Matches
GET /matches?username=xxx:
  currentUser = find user by username
  currentRoute = currentUser.route_polyline
  matchedUsers = []
  allUsers = get all users from Users table except currentUser
  FOR user IN allUsers:
    IF routesOverlap(currentRoute, user.route_polyline) OR routesClose(currentRoute, user.route_polyline):
      matchedUsers.append({username: user.username, homeLocation: user.home_lat/lng})
  RESPOND matchedUsers

// WebSocket /chat connection handler
ON chat connection:
  IDENTIFY users
  ON message received:
    SAVE message to Messages table under appropriate chat_id
    FORWARD message to recipient in real-time

Frontend Flow:

// RegisterForm 
INPUT: home location, destination, password
ON submit:
  POST /register with inputs
  IF success:
    SAVE username in app state
    REDIRECT to MatchesMap

// MatchesMap Component
ON load:
  GET /matches?username=currentUser
  RENDER map with:
    current user route polyline highlighted
    icons for each matched user at their home locations (anonymous usernames)
ON matched user icon click:
  OPEN ChatWindow with selected username

// ChatWindow Component
ON mount:
  CONNECT to WebSocket /chat with current username and chatting user
  ON send message:
    EMIT message via WebSocket
    DISPLAY message in UI
  ON receive message:
    DISPLAY incoming message in UI
---