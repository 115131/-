# -// SayHi🌍 - 简约版网页版交友App（React + Tailwind）
// 功能包含：首页、多语言支持、注册登录、用户列表、基础聊天界面（第一句免费）

// 📁 项目结构预览
// ├── public/
// │   └── index.html
// ├── src/
// │   ├── App.jsx
// │   ├── pages/
// │   │   ├── Home.jsx
// │   │   ├── Register.jsx
// │   │   ├── Login.jsx
// │   │   ├── MatchList.jsx
// │   │   └── ChatRoom.jsx
// │   ├── components/
// │   │   ├── Navbar.jsx
// │   │   └── UserCard.jsx
// │   ├── i18n.js
// │   └── index.js
// └── tailwind.config.js

// 👉 以下是简化的版本（页面组件 + 多语言支持框架）

// src/i18n.js
import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';
import LanguageDetector from 'i18next-browser-languagedetector';

const resources = {
  en: { translation: { welcome: "Welcome to SayHi🌍", intro: "Meet the world, one chat at a time." }},
  zh: { translation: { welcome: "欢迎来到 SayHi🌍", intro: "在这里，遇见世界的你。" }},
  ko: { translation: { welcome: "SayHi🌍에 오신 것을 환영합니다", intro: "세계를 만나는 첫 인연." }},
  ja: { translation: { welcome: "SayHi🌍へようこそ", intro: "世界中の人と出会おう。" }}
};

i18n
  .use(LanguageDetector)
  .use(initReactI18next)
  .init({
    resources,
    fallbackLng: 'en',
    interpolation: { escapeValue: false },
  });

export default i18n;


// src/App.jsx
import React from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import Register from './pages/Register';
import Login from './pages/Login';
import MatchList from './pages/MatchList';
import ChatRoom from './pages/ChatRoom';
import './i18n';
import Navbar from './components/Navbar';

function App() {
  return (
    <Router>
      <Navbar />
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/register" element={<Register />} />
        <Route path="/login" element={<Login />} />
        <Route path="/match" element={<MatchList />} />
        <Route path="/chat/:userId" element={<ChatRoom />} />
      </Routes>
    </Router>
  );
}
export default App;


// src/pages/Home.jsx
import React from 'react';
import { useTranslation } from 'react-i18next';

function Home() {
  const { t } = useTranslation();
  return (
    <div className="flex flex-col items-center justify-center p-10">
      <h1 className="text-4xl font-bold mb-4">{t('welcome')}</h1>
      <p className="text-lg">{t('intro')}</p>
    </div>
  );
}
export default Home;


// tailwind.config.js
module.exports = {
  content: ['./src/**/*.{js,jsx,ts,tsx}'],
  theme: {
    extend: {},
  },
  plugins: [],
};
// src/pages/ChatRoom.jsx
import React, { useState, useEffect } from 'react';
import { useParams } from 'react-router-dom';

const mockUser = {
  id: 'user123',
  gender: 'male',  // male/female
  chatCredits: 3,  // 充值的虚拟币数量
};

const ChatRoom = () => {
  const { userId } = useParams();
  const [messages, setMessages] = useState([]);
  const [input, setInput] = useState('');
  const [firstFreeUsed, setFirstFreeUsed] = useState(false);
  const [credits, setCredits] = useState(mockUser.chatCredits);
  const [showPayPrompt, setShowPayPrompt] = useState(false);

  const sendMessage = () => {
    if (!input.trim()) return;

    // 男性用户逻辑
    if (mockUser.gender === 'male') {
      if (!firstFreeUsed) {
        // 免费第一句
        setFirstFreeUsed(true);
      } else {
        if (credits <= 0) {
          setShowPayPrompt(true);
          return;
        } else {
          setCredits(credits - 1);
        }
      }
    }

    setMessages([...messages, { sender: 'me', text: input }]);
    setInput('');
  };

  return (
    <div className="p-4 max-w-md mx-auto">
      <h2 className="text-xl font-bold mb-4">Chat with {userId}</h2>

      <div className="border p-2 h-64 overflow-auto mb-4 bg-gray-50">
        {messages.map((msg, i) => (
          <div key={i} className={`my-1 ${msg.sender === 'me' ? 'text-right' : 'text-left'}`}>
            <span className="inline-block bg-blue-300 rounded px-2 py-1">{msg.text}</span>
          </div>
        ))}
      </div>

      {showPayPrompt && (
        <div className="mb-2 p-2 bg-yellow-200 rounded text-center">
          你的聊天币不足，请前往充值页面购买。
        </div>
      )}

      <div className="flex gap-2">
        <input
          value={input}
          onChange={e => setInput(e.target.value)}
          className="flex-grow border rounded px-2"
          placeholder="请输入消息"
        />
        <button onClick={sendMessage} className="bg-blue-500 text-white px-4 rounded">
          发送
        </button>
      </div>

      <div className="mt-2 text-sm text-gray-500">剩余聊天币：{credits}</div>
    </div>
  );
};

export default ChatRoom;
npm init -y
npm install react react-dom react-router-dom i18next react-i18next i18next-browser-languagedetector tailwindcss
"scripts": {
  "start": "react-scripts start",
  "build": "react-scripts build"
}
npm start
