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
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/你的用户名/sayhi-app.git
git push -u origin main
README.md// src/pages/Payment.jsx
import React, { useState } from 'react';

const packages = [
  { id: 1, name: '10 Chat Credits', price: 5 },
  { id: 2, name: '30 Chat Credits', price: 12 },
  { id: 3, name: '50 Chat Credits', price: 18 },
];

const Payment = () => {
  const [selected, setSelected] = useState(null);
  const [message, setMessage] = useState('');

  const handlePurchase = () => {
    if (!selected) {
      setMessage('请选择充值套餐');
      return;
    }
    // 这里可以调用支付接口，示例为模拟成功
    setMessage(`成功购买了 ${packages.find(p => p.id === selected).name}！`);
    // TODO: 这里要更新用户余额（状态管理或后端）
  };

  return (
    <div className="max-w-md mx-auto p-4">
      <h2 className="text-xl font-bold mb-4">充值聊天币</h2>
      <ul>
        {packages.map((pkg) => (
          <li key={pkg.id} className="mb-2">
            <label className="flex items-center cursor-pointer">
              <input
                type="radio"
                name="package"
                value={pkg.id}
                onChange={() => setSelected(pkg.id)}
                className="mr-2"
              />
              <span>{pkg.name} - ${pkg.price}</span>
            </label>
          </li>
        ))}
      </ul>
      <button
        onClick={handlePurchase}
        className="mt-4 px-4 py-2 bg-green-500 text-white rounded"
      >
        立即购买
      </button>
      {message && <p className="mt-2 text-green-700">{message}</p>}
    </div>
  );
};

export default Payment;
// src/pages/Register.jsx
import React, { useState } from 'react';
import { useNavigate } from 'react-router-dom';

const Register = () => {
  const [form, setForm] = useState({ username: '', password: '', gender: 'female' });
  const [error, setError] = useState('');
  const navigate = useNavigate();

  const handleChange = (e) => {
    setForm({ ...form, [e.target.name]: e.target.value });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!form.username || !form.password) {
      setError('用户名和密码不能为空');
      return;
    }
    // 模拟注册成功
    alert('注册成功！现在去登录');
    navigate('/login');
  };

  return (
    <div className="max-w-md mx-auto p-4">
      <h2 className="text-xl font-bold mb-4">注册</h2>
      {error && <p className="text-red-600 mb-2">{error}</p>}
      <form onSubmit={handleSubmit} className="flex flex-col gap-3">
        <input
          type="text"
          name="username"
          placeholder="用户名"
          value={form.username}
          onChange={handleChange}
          className="border px-2 py-1 rounded"
        />
        <input
          type="password"
          name="password"
          placeholder="密码"
          value={form.password}
          onChange={handleChange}
          className="border px-2 py-1 rounded"
        />
        <select
          name="gender"
          value={form.gender}
          onChange={handleChange}
          className="border px-2 py-1 rounded"
        >
          <option value="female">女性</option>
          <option value="male">男性</option>
        </select>
        <button type="submit" className="bg-blue-600 text-white py-2 rounded">
          注册
        </button>
      </form>
    </div>
  );
};

export default Register;
// src/pages/Login.jsx
import React, { useState } from 'react';
import { useNavigate } from 'react-router-dom';

const Login = () => {
  const [form, setForm] = useState({ username: '', password: '' });
  const [error, setError] = useState('');
  const navigate = useNavigate();

  const handleChange = (e) => {
    setForm({ ...form, [e.target.name]: e.target.value });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!form.username || !form.password) {
      setError('用户名和密码不能为空');
      return;
    }
    // 模拟登录成功
    alert('登录成功！');
    navigate('/match');
  };

  return (
    <div className="max-w-md mx-auto p-4">
      <h2 className="text-xl font-bold mb-4">登录</h2>
      {error && <p className="text-red-600 mb-2">{error}</p>}
      <form onSubmit={handleSubmit} className="flex flex-col gap-3">
        <input
          type="text"
          name="username"
          placeholder="用户名"
          value={form.username}
          onChange={handleChange}
          className="border px-2 py-1 rounded"
        />
        <input
          type="password"
          name="password"
          placeholder="密码"
          value={form.password}
          onChange={handleChange}
          className="border px-2 py-1 rounded"
        />
        <button type="submit" className="bg-green-600 text-white py-2 rounded">
          登录
        </button>
      </form>
    </div>
  );
};

export default Login;
// src/pages/UserProfile.jsx
import React, { useState } from 'react';

const UserProfile = () => {
  const [profile, setProfile] = useState({
    nickname: '',
    interests: '',
    language: 'en',
  });

  const handleChange = (e) => {
    setProfile({ ...profile, [e.target.name]: e.target.value });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    alert('资料已保存（模拟）');
  };

  return (
    <div className="max-w-md mx-auto p-4">
      <h2 className="text-xl font-bold mb-4">用户资料设置</h2>
      <form onSubmit={handleSubmit} className="flex flex-col gap-3">
        <input
          type="text"
          name="nickname"
          placeholder="昵称"
          value={profile.nickname}
          onChange={handleChange}
          className="border px-2 py-1 rounded"
        />
        <textarea
          name="interests"
          placeholder="兴趣爱好"
          value={profile.interests}
          onChange={handleChange}
          className="border px-2 py-1 rounded"
          rows={3}
        />
        <select
          name="language"
          value={profile.language}
          onChange={handleChange}
          className="border px-2 py-1 rounded"
        >
          <option value="en">English</option>
          <option value="zh">中文</option>
          <option value="ko">한국어</option>
          <option value="ja">日本語</option>
        </select>
        <button type="submit" className="bg-purple-600 text-white py-2 rounded">
          保存资料
        </button>
      </form>
    </div>
  );
};

export default UserProfile;
// src/components/LanguageSwitcher.jsx
import React from 'react';
import { useTranslation } from 'react-i18next';

const LanguageSwitcher = () => {
  const { i18n } = useTranslation();

  const changeLanguage = (lng) => {
    i18n.changeLanguage(lng);
  };

  return (
    <div className="flex space-x-2">
      <button onClick={() => changeLanguage('en')} className="px-2 py-1 border rounded">EN</button>
      <button onClick={() => changeLanguage('zh')} className="px-2 py-1 border rounded">中文</button>
      <button onClick={() => changeLanguage('ko')} className="px-2 py-1 border rounded">한국어</button>
      <button onClick={() => changeLanguage('ja')} className="px-2 py-1 border rounded">日本語</button>
    </div>
  );
};

export default LanguageSwitcher;
// src/pages/MatchList.jsx
import React, { useState, useEffect } from 'react';
import { Link } from 'react-router-dom';

// 模拟用户数据
const mockUsers = [
  { id: 1, name: 'Alice', gender: 'female', language: 'en' },
  { id: 2, name: 'Bob', gender: 'male', language: 'ko' },
  { id: 3, name: 'Carol', gender: 'female', language: 'zh' },
  { id: 4, name: 'Dave', gender: 'male', language: 'ja' },
];

const MatchList = () => {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    // 模拟请求用户列表
    setUsers(mockUsers);
  }, []);

  return (
    <div className="max-w-lg mx-auto p-4">
      <h2 className="text-xl font-bold mb-4">可匹配用户列表</h2>
      <ul>
        {users.map((user) => (
          <li key={user.id} className="mb-3 p-3 border rounded hover:bg-gray-100">
            <Link to={`/chat/${user.id}`} className="block">
              <strong>{user.name}</strong> - {user.gender} - 语言: {user.language}
            </Link>
          </li>
        ))}
      </ul>
    </div>
  );
};

export default MatchList;
// src/components/Navbar.jsx
import React from 'react';
import { Link, useNavigate } from 'react-router-dom';
import LanguageSwitcher from './LanguageSwitcher';

const Navbar = () => {
  const navigate = useNavigate();

  const handleLogout = () => {
    alert('已退出登录（模拟）');
    navigate('/login');
  };

  return (
    <nav className="bg-blue-600 text-white p-4 flex justify-between items-center">
      <div>
        <Link to="/" className="font-bold text-lg">SayHi🌍</Link>
      </div>
      <div className="flex items-center space-x-4">
        <LanguageSwitcher />
        <Link to="/match" className="hover:underline">匹配</Link>
        <Link to="/profile" className="hover:underline">资料</Link>
        <Link to="/payment" className="hover:underline">充值</Link>
        <button onClick={handleLogout} className="bg-red-500 px-3 py-1 rounded">退出</button>
      </div>
    </nav>
  );
};

export default Navbar;
// src/App.jsx
import React from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';

import Navbar from './components/Navbar';
import Login from './pages/Login';
import Register from './pages/Register';
import MatchList from './pages/MatchList';
import ChatRoom from './pages/ChatRoom';
import UserProfile from './pages/UserProfile';
import Payment from './pages/Payment';

const App = () => {
  return (
    <Router>
      <Navbar />
      <div className="container mx-auto mt-4">
        <Routes>
          <Route path="/" element={<Login />} />
          <Route path="/login" element={<Login />} />
          <Route path="/register" element={<Register />} />
          <Route path="/match" element={<MatchList />} />
          <Route path="/chat/:userId" element={<ChatRoom />} />
          <Route path="/profile" element={<UserProfile />} />
          <Route path="/payment" element={<Payment />} />
        </Routes>
      </div>
    </Router>
  );
};

export default App;
// src/index.js
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

import './index.css'; // Tailwind CSS 样式文件

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
// src/i18n.js
import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';

const resources = {
  en: {
    translation: {
      welcome: "Welcome",
      login: "Login",
      register: "Register",
      // 可根据需求继续添加更多翻译
    },
  },
  zh: {
    translation: {
      welcome: "欢迎",
      login: "登录",
      register: "注册",
    },
  },
  ko: {
    translation: {
      welcome: "환영합니다",
      login: "로그인",
      register: "등록",
    },
  },
  ja: {
    translation: {
      welcome: "ようこそ",
      login: "ログイン",
      register: "登録",
    },
  },
};

i18n.use(initReactI18next).init({
  resources,
  lng: "zh",
  fallbackLng: "en",
  interpolation: {
    escapeValue: false,
  },
});

export default i18n;
import './i18n';
// src/pages/ChatRoom.jsx
import React, { useState, useEffect, useRef } from 'react';
import { useParams } from 'react-router-dom';

const ChatRoom = () => {
  const { userId } = useParams();
  const [messages, setMessages] = useState(() => {
    // 尝试读取本地存储消息
    const saved = localStorage.getItem(`chat_with_${userId}`);
    return saved ? JSON.parse(saved) : [];
  });
  const [input, setInput] = useState('');
  const messageEndRef = useRef(null);

  useEffect(() => {
    // 滚动到底部
    messageEndRef.current?.scrollIntoView({ behavior: 'smooth' });
  }, [messages]);

  const sendMessage = () => {
    if (!input.trim()) return;
    const newMsg = {
      id: Date.now(),
      text: input.trim(),
      sender: 'me',
      time: new Date().toLocaleTimeString(),
    };
    const updatedMessages = [...messages, newMsg];
    setMessages(updatedMessages);
    localStorage.setItem(`chat_with_${userId}`, JSON.stringify(updatedMessages));
    setInput('');
  };

  return (
    <div className="max-w-md mx-auto p-4 flex flex-col h-[80vh] border rounded">
      <h2 className="text-xl font-bold mb-4">聊天 - 用户 {userId}</h2>
      <div className="flex-1 overflow-auto mb-4 border p-2 rounded bg-gray-50">
        {messages.length === 0 && <p className="text-gray-500">暂无消息，开始聊天吧！</p>}
        {messages.map(msg => (
          <div
            key={msg.id}
            className={`mb-2 p-2 rounded max-w-[70%] ${
              msg.sender === 'me' ? 'bg-blue-400 text-white self-end' : 'bg-gray-300 self-start'
            }`}
          >
            <div>{msg.text}</div>
            <div className="text-xs text-gray-700 mt-1">{msg.time}</div>
          </div>
        ))}
        <div ref={messageEndRef} />
      </div>
      <div className="flex space-x-2">
        <input
          type="text"
          value={input}
          onChange={e => setInput(e.target.value)}
          className="flex-1 border rounded px-2"
          placeholder="输入消息..."
          onKeyDown={e => e.key === 'Enter' && sendMessage()}
        />
        <button
          onClick={sendMessage}
          className="bg-green-500 text-white px-4 rounded"
        >
          发送
        </button>
      </div>
    </div>
  );
};

export default ChatRoom;
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./src/**/*.{js,jsx,ts,tsx}", 
    "./public/index.html"
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
@tailwind base;
@tailwind components;
@tailwind utilities;
/src
  /components
    Navbar.jsx
    LanguageSwitcher.jsx
  /pages
    Login.jsx
    Register.jsx
    MatchList.jsx
    ChatRoom.jsx
    UserProfile.jsx
    Payment.jsx
  App.jsx
  i18n.js
  index.js
  index.css
tailwind.config.js
postcss.config.js
package.json
npm install react-router-dom react-i18next i18next tailwindcss postcss autoprefixer
import React, { createContext, useState, useContext } from 'react';

const AuthContext = createContext();

export const AuthProvider = ({ children }) => {
  const [user, setUser] = useState(null); // {id, name, gender, language}

  const login = (username) => {
    // 简单模拟登录，设一个固定用户
    setUser({ id: 123, name: username, gender: 'female', language: 'zh' });
  };

  const logout = () => {
    setUser(null);
  };

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
};

export const useAuth = () => useContext(AuthContext);
import React, { useState } from 'react';
import { useNavigate, Link } from 'react-router-dom';
import { useAuth } from '../context/AuthContext';

const Login = () => {
  const [username, setUsername] = useState('');
  const navigate = useNavigate();
  const { login } = useAuth();

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!username.trim()) return alert('请输入用户名');
    login(username.trim());
    navigate('/match');
  };

  return (
    <div className="max-w-sm mx-auto p-6 border rounded mt-20">
      <h2 className="text-2xl mb-6 text-center font-bold">登录 SayHi🌍</h2>
      <form onSubmit={handleSubmit} className="flex flex-col space-y-4">
        <input
          type="text"
          placeholder="用户名"
          value={username}
          onChange={e => setUsername(e.target.value)}
          className="border rounded px-3 py-2"
        />
        <button type="submit" className="bg-blue-600 text-white py-2 rounded">登录</button>
      </form>
      <p className="mt-4 text-center">
        还没有账号？ <Link to="/register" className="text-blue-600 underline">注册</Link>
      </p>
    </div>
  );
};

export default Login;
import React, { useState } from 'react';
import { useNavigate, Link } from 'react-router-dom';
import { useAuth } from '../context/AuthContext';

const Register = () => {
  const [username, setUsername] = useState('');
  const navigate = useNavigate();
  const { login } = useAuth();

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!username.trim()) return alert('请输入用户名');
    // 模拟注册后直接登录
    login(username.trim());
    navigate('/match');
  };

  return (
    <div className="max-w-sm mx-auto p-6 border rounded mt-20">
      <h2 className="text-2xl mb-6 text-center font-bold">注册 SayHi🌍</h2>
      <form onSubmit={handleSubmit} className="flex flex-col space-y-4">
        <input
          type="text"
          placeholder="用户名"
          value={username}
          onChange={e => setUsername(e.target.value)}
          className="border rounded px-3 py-2"
        />
        <button type="submit" className="bg-green-600 text-white py-2 rounded">注册</button>
      </form>
      <p className="mt-4 text-center">
        已有账号？ <Link to="/login" className="text-blue-600 underline">登录</Link>
      </p>
    </div>
  );
};

export default Register;
import React from 'react';
import { useAuth } from '../context/AuthContext';

const UserProfile = () => {
  const { user } = useAuth();

  if (!user) {
    return <p className="text-center mt-10">请先登录查看资料。</p>;
  }

  return (
    <div className="max-w-md mx-auto p-6 border rounded mt-10">
      <h2 className="text-2xl font-bold mb-6">我的资料</h2>
      <p><strong>用户名：</strong> {user.name}</p>
      <p><strong>性别：</strong> {user.gender}</p>
      <p><strong>语言：</strong> {user.language}</p>
      {/* 这里你可以继续扩展资料编辑功能 */}
    </div>
  );
};

export default UserProfile;
import React, { useState } from 'react';

const Payment = () => {
  const [credits, setCredits] = useState(() => {
    const saved = localStorage.getItem('sayhi_credits');
    return saved ? Number(saved) : 0;
  });

  const handleBuy = () => {
    // 模拟充值10个聊天币
    const newCredits = credits + 10;
    setCredits(newCredits);
    localStorage.setItem('sayhi_credits', newCredits);
    alert('充值成功！您获得了10聊天币。');
  };

  return (
    <div className="max-w-md mx-auto p-6 border rounded mt-10">
      <h2 className="text-2xl font-bold mb-6">充值聊天币</h2>
      <p>当前余额: <strong>{credits}</strong> 聊天币</p>
      <button onClick={handleBuy} className="mt-4 bg-yellow-500 text-white px-4 py-2 rounded">
        充值10聊天币
      </button>
      <p className="mt-6 text-gray-600">
        说明：男士第一条消息免费，后续消息需要聊天币才能发送。
      </p>
    </div>
  );
};

export default Payment;
// src/pages/ChatRoom.jsx
import React, { useState, useEffect, useRef } from 'react';
import { useParams } from 'react-router-dom';

const ChatRoom = () => {
  const { userId } = useParams();
  const [messages, setMessages] = useState(() => {
    const saved = localStorage.getItem(`chat_with_${userId}`);
    return saved ? JSON.parse(saved) : [];
  });
  const [input, setInput] = useState('');
  const [credits, setCredits] = useState(() => {
    const saved = localStorage.getItem('sayhi_credits');
    return saved ? Number(saved) : 0;
  });
  const messageEndRef = useRef(null);

  useEffect(() => {
    messageEndRef.current?.scrollIntoView({ behavior: 'smooth' });
  }, [messages]);

  const isMaleSender = true; // 这里你可以用用户性别判断，示范用true表示男士
  const firstMessageFree = messages.filter(m => m.sender === 'me').length === 0;

  const sendMessage = () => {
    if (!input.trim()) return;

    if (isMaleSender && !firstMessageFree) {
      if (credits <= 0) {
        alert('聊天币余额不足，请先充值！');
        return;
      } else {
        const newCredits = credits - 1;
        setCredits(newCredits);
        localStorage.setItem('sayhi_credits', newCredits);
      }
    }

    const newMsg = {
      id: Date.now(),
      text: input.trim(),
      sender: 'me',
      time: new Date().toLocaleTimeString(),
    };
    const updatedMessages = [...messages, newMsg];
    setMessages(updatedMessages);
    localStorage.setItem(`chat_with_${userId}`, JSON.stringify(updatedMessages));
    setInput('');
  };

  return (
    <div className="max-w-md mx-auto p-4 flex flex-col h-[80vh] border rounded">
      <h2 className="text-xl font-bold mb-4">聊天 - 用户 {userId}</h2>
      <div className="flex-1 overflow-auto mb-4 border p-2 rounded bg-gray-50">
        {messages.length === 0 && <p className="text-gray-500">暂无消息，开始聊天吧！</p>}
        {messages.map(msg => (
          <div
            key={msg.id}
            className={`mb-2 p-2 rounded max-w-[70%] ${
              msg.sender === 'me' ? 'bg-blue-400 text-white self-end' : 'bg-gray-300 self-start'
            }`}
          >
            <div>{msg.text}</div>
            <div className="text-xs text-gray-700 mt-1">{msg.time}</div>
          </div>
        ))}
        <div ref={messageEndRef} />
      </div>
      <div className="flex space-x-2 items-center">
        <input
          type="text"
          value={input}
          onChange={e => setInput(e.target.value)}
          className="flex-1 border rounded px-2 py-1"
          placeholder="输入消息..."
          onKeyDown={e => e.key === 'Enter' && sendMessage()}
        />
        <button
          onClick={sendMessage}
          className="bg-green-500 text-white px-4 py-2 rounded"
        >
          发送
        </button>
      </div>
      <p className="mt-2 text-sm text-gray-600">
        你的聊天币余额: <strong>{credits}</strong> （男士首条消息免费，后续消息1币）
      </p>
    </div>
  );
};

export default ChatRoom;
import React from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';

import Navbar from './components/Navbar';
import Login from './pages/Login';
import Register from './pages/Register';
import MatchList from './pages/MatchList';
import ChatRoom from './pages/ChatRoom';
import UserProfile from './pages/UserProfile';
import Payment from './pages/Payment';

import { AuthProvider } from './context/AuthContext';

const App = () => {
  return (
    <AuthProvider>
      <Router>
        <Navbar />
        <div className="container mx-auto mt-4">
          <Routes>
            <Route path="/" element={<Login />} />
            <Route path="/login" element={<Login />} />
            <Route path="/register" element={<Register />} />
            <Route path="/match" element={<MatchList />} />
            <Route path="/chat/:userId" element={<ChatRoom />} />
            <Route path="/profile" element={<UserProfile />} />
            <Route path="/payment" element={<Payment />} />
          </Routes>
        </div>
      </Router>
    </AuthProvider>
  );
};

export default App;
