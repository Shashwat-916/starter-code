# Scribble Game - 20 Phase Development Guide

A comprehensive step-by-step guide to building a real-time multiuser drawing and guessing game.

---

## Phase 1: Project Foundation

### 1.1 Initialize Project Structure
- Create monorepo structure with `backend/` and `frontend/` directories
- Initialize both projects with package.json
- Set up TypeScript configuration in both directories

### 1.2 Backend Setup
- Install dependencies: `express`, `cors`, `ws`, `uuid`, `nodemon`
- Configure `tsconfig.json` with proper module settings
- Set up build scripts for TypeScript compilation

### 1.3 Frontend Setup
- Initialize Vite + React + TypeScript project
- Install dependencies: `react`, `react-dom`
- Configure Vite for development server

---

## Phase 2: WebSocket Server

### 2.1 Basic HTTP Server
- Create Express server on port 3000
- Configure CORS middleware
- Set up static file serving for frontend build

### 2.2 WebSocket Integration
- Install `ws` library for WebSocket server
- Create WebSocketServer attached to HTTP server
- Implement connection handler for client events

### 2.3 Client Connection Management
- Create client registry Map to track connected users
- Implement connection/disconnection handlers
- Store socket metadata (userId, roomId, socketId)

---

## Phase 3: Room Management System

### 3.1 Room Creation API
- Create POST `/create-room` endpoint
- Generate unique room ID (8 characters)
- Generate admin salt for room authentication

### 3.2 Join Room Functionality
- Implement `JOIN_ROOM` WebSocket message handler
- Validate room existence
- Add user to room's player list

### 3.3 Room State Broadcasting
- Implement room state synchronization
- Broadcast `ROOM_UPDATED` events to all players
- Handle player disconnection and cleanup

---

## Phase 4: Manager Architecture

### 4.1 UserManager Implementation
- Track players with userId, name, avatarId, socketId
- Manage admin role assignment
- Provide player list for state sync

### 4.2 DrawManager Implementation
- Buffer canvas events for late joiners
- Store drawing history as array of CanvasEvent
- Clear canvas between turns

### 4.3 ChatManager Implementation
- Store chat messages with name and content
- Provide message history for state sync
- Handle system messages

### 4.4 ScoreManager Implementation
- Track player scores
- Calculate points based on guess speed
- Implement scoring formula

---

## Phase 5: Game Logic Core

### 5.1 RoomManager Orchestration
- Create RoomManager to coordinate all managers
- Implement game state machine
- Manage turn progression

### 5.2 Game Start Logic
- Implement `START_GAME` message handler
- Validate admin authorization
- Select first drawer randomly

### 5.3 Turn System
- Select random word from provided list
- Assign drawer role to player
- Set turn timer (default 60 seconds)

### 5.4 Word Selection
- Create word list with categories
- Randomly select word for drawer
- Hide word from guessers

---

## Phase 6: Drawing System

### 6.1 Canvas Event Handling
- Implement `DRAW_EVENT` message handler
- Validate drawer authorization
- Store events in DrawManager

### 6.2 Real-time Drawing Broadcast
- Broadcast drawing events to all room players
- Filter events for non-drawers
- Optimize event transmission

### 6.3 Canvas Synchronization
- Sync drawing history to late joiners
- Clear canvas on new turn
- Handle canvas state management

---

## Phase 7: Guessing System

### 7.1 Guess Processing
- Implement `GUESS` message handler
- Compare guess against current word
- Handle case-insensitive matching

### 7.2 Correct Guess Logic
- Award points to correct guesser
- Notify all players of correct guess
- Advance turn if all players guessed

### 7.3 Incorrect Guess Handling
- Display guess in chat as regular message
- Don't reveal the word
- Continue turn timer

---

## Phase 8: Scoring System

### 8.1 Point Calculation
- Calculate points based on time remaining
- Faster guesses = more points
- Drawer also gets points when word is guessed

### 8.2 Scoreboard State
- Include scores in game state
- Update on each correct guess
- Display in UI

### 8.3 Game End Scoring
- Track total rounds played
- Display final scores at game end
- Declare winner

---

## Phase 9: Frontend Canvas Component

### 9.1 Canvas Setup
- Create React Canvas component
- Set up HTML5 canvas element
- Handle window resize

### 9.2 Drawing Input
- Capture mouse/touch events
- Generate CanvasEvent with coordinates
- Emit events to WebSocket

### 9.3 Drawing Rendering
- Listen for DRAW_EVENT from server
- Render lines on canvas
- Implement smooth drawing

---

## Phase 10: Frontend State Management

### 10.1 Game State Store
- Create React state for game data
- Store: players, gameStarted, drawer, currentWord, messages, scores
- Update on WebSocket events

### 10.2 Connection Handling
- Implement WebSocket client wrapper
- Handle reconnection on disconnect
- Queue messages during reconnection

### 10.3 Event Dispatcher
- Create event handlers for each message type
- Update React state accordingly
- Trigger UI re-renders

---

## Phase 11: UI Components

### 11.1 Lobby Screen
- Room creation input
- Join room with room ID
- Username and avatar selection
- Display connected players

### 11.2 Game Header
- Display current word (for drawer) or blanks
- Show timer countdown
- Display round number

### 11.3 Player List
- Show all players in room
- Highlight current drawer
- Display player scores
- Show admin indicator

---

## Phase 12: Chat System UI

### 12.1 ChatBox Component
- Message display area
- Auto-scroll to newest message
- Distinguish system messages

### 12.2 Guess Input
- Text input for guesses
- Submit on Enter key
- Clear input after guess

### 12.3 Message Styling
- Style correct guess notifications
- Differentiate player messages
- Highlight system events

---

## Phase 13: Drawing Tools

### 13.1 Color Palette
- Implement color selection UI
- Send color with DRAW_EVENT
- Render with correct color

### 13.2 Brush Size
- Add brush size selector
- Include brush size in events
- Apply to canvas context

### 13.3 Clear Canvas
- Add clear button for drawer
- Broadcast CLEAR_EVENT
- Reset canvas for all players

---

## Phase 14: Game Flow

### 14.1 Turn Transitions
- Handle NEXT_TURN events
- Update drawer assignment
- Select new word
- Reset timer

### 14.2 Game Over Handling
- Detect end of game (all rounds)
- Broadcast GAME_OVER event
- Show final scores

### 14.3 Restart Game
- Allow admin to start new game
- Reset all scores
- Clear game state

---

## Phase 15: Admin System

### 15.1 Admin Role Assignment
- First player in room becomes admin
- Admin can start game
- Admin can control game flow

### 15.2 Admin Controls UI
- Show Start Game button for admin
- Display admin indicator
- Control game settings

### 15.3 Salt-based Authentication
- Generate salt on room creation
- Store in URL for persistence
- Validate on reconnection

---

## Phase 16: Performance Optimization

### 16.1 Event Throttling
- Throttle DRAW_EVENT emissions (20-30/sec)
- Debounce chat messages
- Batch state updates

### 16.2 State Optimization
- Cache game state
- Send only deltas when possible
- Optimize serialization

### 16.3 Connection Handling
- Implement heartbeat/ping-pong
- Handle stale connections
- Graceful degradation

---

## Phase 17: Error Handling

### 17.1 Server-side Validation
- Validate all incoming messages
- Sanitize user inputs
- Prevent injection attacks

### 17.2 Error Broadcasting
- Send error messages to clients
- Display user-friendly errors
- Log server errors

### 17.3 Client Error Handling
- Handle WebSocket disconnects
- Show reconnection UI
- Graceful error recovery

---

## Phase 18: Deployment

### 18.1 Docker Configuration
- Create Dockerfile for backend
- Create Dockerfile for frontend
- Set up multi-stage builds

### 18.2 Docker Compose
- Configure development environment
- Set up production deployment
- Handle environment variables

### 18.3 Production Build
- Build frontend for production
- Configure reverse proxy
- Set up SSL/TLS

---

## Phase 19: Polish & Features

### 19.1 Animations
- Add smooth transitions
- Animate timer countdown
- Smooth drawing rendering

### 19.2 Sound Effects
- Add correct guess sound
- Turn timer alerts
- Game over fanfare

### 19.3 Enhanced UI
- Improve visual design
- Add loading states
- Responsive design

---

## Phase 20: Advanced Features

### 20.1 Word Categories
- Support multiple word categories
- Let admin select category
- Difficulty levels

### 20.2 Custom Words
- Allow admin to add custom words
- Store in room configuration
- Persist across rounds

### 20.3 Leaderboards
- Track historical scores
- Implement persistent rankings
- Display statistics

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                      FRONTEND                            │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌──────────┐  │
│  │ Lobby   │  │ Canvas  │  │ ChatBox │  │Scoreboard│  │
│  └────┬────┘  └────┬────┘  └────┬────┘  └─────┬────┘  │
│       │            │            │             │        │
│       └────────────┴────────────┴─────────────┘        │
│                         │                                │
│              ┌──────────┴──────────┐                   │
│              │   WebSocket Client   │                   │
│              └──────────┬──────────┘                   │
└─────────────────────────┼───────────────────────────────┘
                          │
                          │ WebSocket
                          │
┌─────────────────────────┼───────────────────────────────┐
│                    BACKEND                                │
│              ┌──────────┴──────────┐                    │
│              │   WebSocket Server  │                    │
│              └──────────┬──────────┘                    │
│                         │                                │
│  ┌──────────────────────┼──────────────────────────────┐│
│  │              ┌───────┴───────┐                      ││
│  │              │ ScribbleGame  │ (Facade)             ││
│  │              └───────┬───────┘                      ││
│  │                      │                               ││
│  │    ┌─────────────────┼─────────────────┐            ││
│  │    │                 │                 │            ││
│  │ ┌──┴───┐      ┌──────┴─────┐    ┌──────┴─────┐     ││
│  │ │User  │      │   Room     │    │   Draw    │     ││
│  │ │Manager│      │  Manager   │    │  Manager  │     ││
│  │ └──┬───┘      └──────┬─────┘    └──────┬─────┘     ││
│  │    │                 │                 │            ││
│  │ ┌──┴───┐      ┌──────┴─────┐    ┌──────┴─────┐     ││
│  │ │Chat  │      │   Score    │    │   Admin    │     ││
│  │ │Manager│     │  Manager   │    │  Manager   │     ││
│  │ └──────┘      └────────────┘    └────────────┘     ││
│  └─────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────┘
```

## Message Protocol

| Message Type | Direction | Payload |
|-------------|-----------|---------|
| `JOIN_ROOM` | Client→Server | `{ roomId, user: { userId, name, avatarId }, salt? }` |
| `START_GAME` | Client→Server | `{ roomId, words: string[], salt? }` |
| `DRAW_EVENT` | Client→Server | `{ roomId, event: CanvasEvent }` |
| `GUESS` | Client→Server | `{ roomId, userId, name, guess }` |
| `ROOM_UPDATED` | Server→Client | `{ players, gameStarted, ... }` |
| `GAME_STARTED` | Server→Client | `{ players, drawer, word, timeLeft, ... }` |
| `DRAW_EVENT` | Server→Client | `{ x, y, type, color, size }` |
| `CHAT_UPDATE` | Server→Client | `{ messages }` |
| `GUESS_UPDATE` | Server→Client | `{ players, scores }` |
| `NEXT_TURN` | Server→Client | `{ drawer, word, timeLeft, ... }` |
| `GAME_OVER` | Server→Client | `{ players, scores, winner }` |
| `TIMER_UPDATE` | Server→Client | `{ timeLeft }` |
| `ERROR` | Server→Client | `{ message }` |

## Technology Stack

- **Backend**: Node.js, Express, TypeScript, WebSocket (ws)
- **Frontend**: React, Vite, TypeScript, HTML5 Canvas
- **Deployment**: Docker, Docker Compose
- **Protocol**: JSON over WebSocket
