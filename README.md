const express = require("express");
const http = require("http");
const socketIo = require("socket.io");

const app = express();
const server = http.createServer(app);
const io = socketIo(server);

app.use(express.static("public"));

io.on("connection", (socket) => {
  console.log("A user connected");

  socket.on("chat message", (msg) => {
    io.emit("chat message", msg); // broadcast to all
  });

  socket.on("disconnect", () => {
    console.log("User disconnected");
  });
});

const PORT = process.env.PORT || 3000;
server.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Chat App</title>
  <link rel="stylesheet" href="style.css"/>
</head>
<body>
  <div id="chat-container">
    <ul id="messages"></ul>
    <form id="chat-form">
      <input id="m" autocomplete="off" placeholder="Type your message" />
      <button>Send</button>
    </form>
  </div>
  <script src="/socket.io/socket.io.js"></script>
  <script src="client.js"></script>
</body>
</html>
body {
  font-family: Arial, sans-serif;
  background: #f1f1f1;
  margin: 0;
  padding: 0;
}

#chat-container {
  max-width: 600px;
  margin: 50px auto;
  background: white;
  padding: 20px;
  border-radius: 8px;
}

#messages {
  list-style-type: none;
  padding: 0;
  max-height: 300px;
  overflow-y: scroll;
  margin-bottom: 10px;
}

#messages li {
  padding: 8px 10px;
  border-bottom: 1px solid #eee;
}

#chat-form {
  display: flex;
}

#chat-form input {
  flex: 1;
  padding: 10px;
  font-size: 16px;
}

#chat-form button {
  padding: 10px 20px;
}
const socket = io();

const form = document.getElementById("chat-form");
const input = document.getElementById("m");
const messages = document.getElementById("messages");

form.addEventListener("submit", (e) => {
  e.preventDefault();
  if (input.value) {
    socket.emit("chat message", input.value);
    input.value = "";
  }
});

socket.on("chat message", (msg) => {
  const item = document.createElement("li");
  item.textContent = msg;
  messages.appendChild(item);
  messages.scrollTop = messages.scrollHeight;
});
