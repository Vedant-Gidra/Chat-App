
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

## Project Structure

```
Real-time-chat-application/
├── backend/
│   ├── config/
│   │   ├── db.js
│   │   └── generateToken.js
│   ├── controllers/
│   │   ├── userControllers.js
│   │   ├── chatControllers.js
│   │   └── messageControllers.js
│   ├── middleware/
│   │   ├── authMiddleware.js
│   │   └── errorMiddleware.js
│   ├── models/
│   │   ├── userModel.js
│   │   ├── chatModel.js
│   │   └── messageModel.js
│   ├── routes/
│   │   ├── userRoutes.js
│   │   ├── chatRoutes.js
│   │   └── messageRoutes.js
│   └── server.js
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── Pages/
│   │   ├── components/
│   │   ├── Context/
│   │   ├── config/
│   │   ├── data/
│   │   ├── App.js
│   │   └── index.js
│   └── package.json
├── .env
├── package.json
└── README.md
```

## Deployment

### Backend Deployment (Render)

1. Push code to GitHub
2. Create account on Render.com
3. Create new Web Service
4. Connect GitHub repository
5. Set environment variables (PORT, MONGO_URI, JWT_SECRET, NODE_ENV)
6. Deploy

Backend URL: `https://your-app-backend.onrender.com`

### Frontend Deployment (Vercel)

1. Create account on Vercel.com
2. Import GitHub repository
3. Set root directory to `./frontend`
4. Set environment variable: `REACT_APP_API_URL=<backend_url>`
5. Deploy

Frontend URL: `https://your-app-frontend.vercel.app`

## MongoDB Atlas Setup

1. Create account at mongodb.com/atlas
2. Create a free cluster
3. Get connection string: Cluster > Connect > Drivers
4. Whitelist your IP address in Network Access
5. Use connection string as MONGO_URI in .env

## Socket.io Real-Time Events

### Server Emits
- `message received` - New message arrived
- `typing` - User started typing
- `stop typing` - User stopped typing

### Server Listens
- `setup` - User connection setup
- `join chat` - User joins chat room
- `new message` - New message sent
- `typing` - Typing event
- `stop typing` - Stop typing event

## Troubleshooting

### MongoDB Connection Issues
- Verify MONGO_URI format
- Check IP whitelist in MongoDB Atlas (allow 0.0.0.0/0)
- Confirm database name in URI

### Port Already in Use
- Change PORT in .env file
- Or kill process using the port

### Build Errors
- Delete `node_modules` and `package-lock.json`
- Run `npm install` again
- Use `npm install --legacy-peer-deps` for frontend

### Real-Time Messages Not Appearing
- Verify Socket.io connection in browser DevTools
- Check backend server logs
- Ensure both users are in same chat room

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

## License

ISC

## Author

Vedant Gidra

  
