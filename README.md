# AI Text-to-Video Generator

A sleek, modern Next.js web application that connects to a Google Colab GPU backend to generate and stream AI videos directly to your browser. 

Inspired by professional creator tools (like Adobe Firefly), this interface features a clean, dark-mode aesthetic with interactive states, real-time fetching, and a reliable Server Action backend designed to handle long-running ML generation tasks seamlessly.

![UI Concept](https://images.unsplash.com/photo-1514565131-fce0801e5785?q=80&w=2070&auto=format&fit=crop)

## ✨ Features

- **Modern UI/UX**: A dark-themed, sophisticated design built with Tailwind CSS, featuring subtle animations, loading states, and a central video display area.
- **Colab GPU Integration**: Built to work dynamically with remote Google Colab instances exposed via Ngrok, avoiding local hardware limits.
- **Robust Video Streaming**: Bypasses the default 5-minute HTTP `fetch` timeouts in Node.js by utilizing the native `https` module to directly stream large `.mp4` chunks onto the filesystem.
- **Secure Server Actions**: Employs Next.js Server Actions to keep the Ngrok endpoints, headers, and processing completely secure on the backend layer.

## 🛠 Tech Stack

- **Framework**: [Next.js 14+ (App Router)](https://nextjs.org/)
- **UI & Styling**: React, Tailwind CSS
- **Network & File I/O**: Native Node.js `https` and `fs` (for reliable long-polling and video streaming)
- **Backend Model**: Google Colab (expected to run a Python Server + Text-to-Video AI) + Ngrok

## 🚀 Getting Started

### 1. Set Up Your Colab Backend
You will need a Google Colab notebook running a Text-to-Video model (like Stable Video Diffusion, ModelScope, etc.) and serving requests via HTTP.
Ensure your Python server uses **Ngrok** to expose the local server to the web, and handles `GET /generate?prompt=...` returning a raw `.mp4` stream.

### 2. Configure the Frontend
Clone this repository and update the Ngrok URL.
Ngrok free tier URLs reset every time you tunnel. Before running the frontend, update the target URL in the Server Actions.

Open `app/actions.ts` and modify the `COLAB_URL`:
```typescript
// app/actions.ts
const COLAB_URL = "https://your-ngrok-url.ngrok-free.dev";
```

### 3. Install Dependencies
```bash
npm install
# or
yarn install
```

### 4. Run the Development Server
```bash
npm run dev
```
Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

## 🧠 How it Works

1. **User Prompt**: The user enters a video idea in the Next.js frontend UI.
2. **Server Action**: The client triggers `generateVideo(prompt)`, passing the prompt securely to the Next.js server.
3. **Colab Tunnel**: The Next.js server initiates an `https.get` request directly to the Colab Ngrok tunnel.
4. **Infinite Timeout**: Recognizing that AI video can take 5+ minutes, the Next.js backend forces `timeout: 0` so it naturally waits for the GPU to finish.
5. **Disk Write**: The resulting video stream is piped straight to the `public/generated_video.mp4` path.
6. **Playback**: The URL is sent back to the client with a newly generated cache-busting timestamp, auto-playing the localized video.

---

*This project was developed with a focus on extending standard Next.js limits to accommodate rigorous Machine Learning workflows.*
