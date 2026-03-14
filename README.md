const express = require('express');
const http = require('http');
const { Server } = require('socket.io');
const multer = require('multer');
const cloudinary = require('cloudinary').v2;
const cors = require('cors');

const app = express();
const server = http.createServer(app);
const io = new Server(server, {
  cors: { origin: '*', methods: ['GET', 'POST'] }
});

app.use(cors());
app.use(express.json());

// ── Cloudinary सेटअप (फ़ोटो/वीडियो जमा करने के लिए) ──
cloudinary.config({
  cloud_name: process.env.CLOUDINARY_CLOUD_NAME,
  api_key:    process.env.CLOUDINARY_API_KEY,
  api_secret: process.env.CLOUDINARY_API_SECRET,
});

// ── Multer (फ़ाइल अपलोड के लिए) ──
const storage = multer.memoryStorage();
const upload = multer({ storage, limits: { fileSize: 50 * 1024 * 1024 } }); // 50MB तक

// ── सभी जुड़े हुए उपयोगकर्ता ──
const onlineUsers = {};

// ── फ़ोटो / वीडियो अपलोड करने का रास्ता ──
app.post('/upload', upload.single('file'), async (req, res) => {
  try {
    if (!req.file) return res.status(400).json({ error: 'कोई फ़ाइल नहीं मिली' });

    const isVideo = req.file.mimetype.startsWith('video/');
    const resourceType = isVideo ? 'video' : 'image';

    // Cloudinary पर अपलोड करो
    const result = await new Promise((resolve, reject) => {
      const stream = cloudinary.uploader.upload_stream(
        { resource_type: resourceType, folder: 'connectx-chat' },
        (error, result) => {
          if (error) reject(error);
          else resolve(result);
        }
      );
      stream.end(req.file.buffer);
    });

    res.json({
      url:  result.secure_url,
      type: resourceType,
      name: req.file.originalname,
    });
  } catch (err) {
    console.error('अपलोड में गड़बड़ी:', err);
    res.status(500).json({ error: 'अपलोड नहीं हुआ' });
  }
});

// ── सर्वर चालू है? जाँचने के लिए ──
app.get('/', (req, res) => res.send('ConnectX सर्वर चालू है ✅'));

// ── Socket.io — असली समय चैटिंग ──
io.on('connection', (socket) => {
  console.log('नया उपयोगकर्ता जुड़ा:', socket.id);

  // उपयोगकर्ता अपना नाम बताता है
  socket.on('user_join', (username) => {
    onlineUsers[socket.id] = username;
    io.emit('online_users', Object.values(onlineUsers));
    console.log(username, 'जुड़ा');
  });

  // सन्देश भेजना
  socket.on('send_message', (data) => {
    // data में होगा: { sender, receiver, text, type, url, name, time }
    io.emit('receive_message', data); // सभी को भेजो
  });

  // उपयोगकर्ता टाइप कर रहा है
  socket.on('typing', (data) => {
    socket.broadcast.emit('user_typing', data);
  });

  // उपयोगकर्ता बाहर गया
  socket.on('disconnect', () => {
    const name = onlineUsers[socket.id];
    delete onlineUsers[socket.id];
    io.emit('online_users', Object.values(onlineUsers));
    console.log(name, 'बाहर गया');
  });
});

const PORT = process.env.PORT || 3000;
server.listen(PORT, () => console.log(`सर्वर चालू है पोर्ट ${PORT} पर`));
