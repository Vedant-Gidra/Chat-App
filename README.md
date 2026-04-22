
# Real-Time Chat Application

A full-stack MERN application for real-time messaging with user authentication, one-on-one chats, group conversations, and live notifications.

## Overview

Talk-a-Tive is a modern chat application built with the MERN stack (MongoDB, Express, React, Node.js). It enables users to communicate in real-time using WebSocket technology, search for other users, create group chats, and manage conversations seamlessly.

## Tech Stack

### Frontend
- React.js - UI library
- Context API - State management
- Socket.io-client - Real-time communication
- React Router - Client-side routing
- Lottie - Animated components

### Backend
- Node.js - JavaScript runtime
- Express.js - Server framework
- MongoDB - NoSQL database
- Mongoose - ODM for MongoDB
- Socket.io - WebSocket library for real-time events
- JWT - Authentication tokens
- bcryptjs - Password hashing

### Database
- MongoDB Atlas - Cloud database

## Features

### Core Functionality
- User registration and login with JWT authentication
- Secure password hashing with bcryptjs
- User search and profile browsing
- One-on-one private messaging
- Group chat creation and management
- Real-time message delivery via Socket.io
- Typing indicators for active conversations
- Message history and persistence
- Add/remove members from groups
- Rename group chats
- Latest message preview in chat list
- User notifications for new messages

### Security
- JWT-based authentication with 30-day expiration
- Protected API routes with middleware
- Password never exposed in API responses
- Passwords hashed with bcrypt (10 salt rounds)
- User exclusion from own search results
- Authorization header validation

## Prerequisites

Before running the application, ensure you have:
- Node.js v14 or higher
- npm or yarn package manager
- MongoDB Atlas account (free tier available)
- Git for version control

## Installation

### Step 1: Clone the Repository

```bash
git clone https://github.com/yourusername/Real-time-chat-application
cd Real-time-chat-application
```

### Step 2: Install Backend Dependencies

```bash
npm install
```

### Step 3: Install Frontend Dependencies

```bash
cd frontend
npm install --legacy-peer-deps
cd ..
```

### Step 4: Environment Configuration

Create a `.env` file in the root directory:

```
PORT=5000
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/chatapp
JWT_SECRET=your_super_secret_jwt_key_here
NODE_ENV=development
```

To generate a secure JWT_SECRET, run:
```bash
node -e "console.log(require('crypto').randomBytes(64).toString('hex'))"
```

Replace `MONGO_URI` with your MongoDB Atlas connection string from your cluster.

## Running Locally

### Start Backend Server

In the root directory:
```bash
npm run server
```

The backend will run on `http://localhost:5000` and watch for file changes.

### Start Frontend Application

In a new terminal:
```bash
cd frontend
npm start
```

The frontend will run on `http://localhost:3000` and automatically open in your browser.

### Test the Application

1. Register a new account with name, email, and password
2. Open a second browser window and register another account
3. Search for the first user in the second account
4. Send messages and verify real-time delivery
5. Test group chat creation by adding multiple users
6. Verify typing indicators and notifications

## API Endpoints

### User Endpoints
- `POST /api/user` - Register new user
- `POST /api/user/login` - Login user
- `GET /api/user?search=<query>` - Search users (protected)

### Chat Endpoints
- `GET /api/chat` - Get all chats for user (protected)
- `POST /api/chat` - Create or fetch one-on-one chat (protected)
- `POST /api/chat/group` - Create group chat (protected)
- `PUT /api/chat/rename` - Rename group (protected)
- `PUT /api/chat/groupadd` - Add user to group (protected)
- `PUT /api/chat/groupremove` - Remove user from group (protected)

### Message Endpoints
- `GET /api/message/:chatId` - Get all messages in chat (protected)
- `POST /api/message` - Send new message (protected)

For detailed endpoint documentation, see `description.txt`.

## Future Enhancements

- End-to-end message encryption
- File and image sharing
- Voice and video calls
- Message reactions and editing
- User activity status indicators
- Advanced notifications and muting
- Two-factor authentication
- Message scheduling
- Admin dashboard

## Author

Vedant Gidra

  
