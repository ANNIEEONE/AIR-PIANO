# 🎹 Air Piano

Play a virtual piano with nothing but your webcam and your index finger. No MIDI controller, no touchscreen, no installs — just open a web page and start playing music in the air.

---

## ✨ Features

- **Real-time hand tracking** — your webcam feed is analyzed live in the browser to find your hand and index fingertip.
- **Virtual piano keyboard** — a full octave (C–B) plus a closing high C, with black keys, styled like a real instrument.
- **Play notes with your finger** — move your index fingertip over a key's zone to play it; move away and back to play it again.
- **Real piano-ish sound** — notes are synthesized on the fly with the Web Audio API using an ADSR envelope (Attack, Decay, Sustain, Release), not a single flat beep.
- **Three instrument voices** — Grand Piano, Electric Piano, and Soft Synth.
- **Adjustable octave** (3, 4, or 5) and **volume**.
- **Optional hand-skeleton overlay** so you can see exactly what the tracker sees.
- **Live status indicators** for camera connection, hand detection, and the current note.
- **Graceful error handling** — denied camera permissions, missing cameras, no hand in frame, multiple hands, or a failed model load all show a clear message instead of crashing the page.
- **Runs 100% locally** — no server, no build tools, no video or audio ever leaves your machine.

---

## 🧠 How It Works

1. **Hand tracking.** The app uses [MediaPipe Tasks Vision](https://developers.google.com/mediapipe/solutions/vision/hand_landmarker) (`HandLandmarker`), loaded straight from a CDN, to detect 21 landmark points on your hand for every video frame — no server or Python required, it all runs on-device in WebAssembly.

2. **Finding the fingertip.** Of those 21 points, landmark **#8** is the tip of the index finger. Its position is given as normalized coordinates (`x`, `y` between `0` and `1`), which the app maps onto real pixel positions over the on-screen piano.

3. **Note zones.** Each piano key is a real `<div>` on the page. Every frame, the app checks whether the mapped fingertip position falls inside a key's bounding box — that's the "note zone." Black keys are checked first since they visually sit above the white keys.

4. **Debouncing.** A note only plays when your fingertip's zone *changes* — entering a new key, or leaving all keys. Staying still inside a zone will **not** re-trigger the note over and over; you have to leave and come back.

5. **Sound synthesis.** When a note triggers, the app builds a small Web Audio graph on the fly: two slightly detuned oscillators → a low-pass filter → a gain node driven by an ADSR envelope → your volume control → your speakers. No audio files are used or downloaded.

---

## 🛠️ Technologies Used

| Purpose            | Technology                              |
|---------------------|------------------------------------------|
| Structure            | HTML5                                    |
| Styling               | CSS3 (glassmorphism, responsive layout) |
| Logic                 | Vanilla JavaScript (ES modules)         |
| Hand tracking         | [MediaPipe Tasks Vision](https://developers.google.com/mediapipe/solutions/vision/hand_landmarker) (`HandLandmarker`) |
| Sound synthesis       | Web Audio API                           |

No React, no Node.js, no backend, no build tools, no Python.

---

## 📁 Project Structure

This project ships as a **single self-contained file**: all HTML, CSS, and JavaScript live in one place, so there's nothing to wire up.

```
air-piano/
└── air-piano.html   ← open this file in your browser, that's it
```

---

## ▶️ How to Run It

1. Download `air-piano.html`.
2. Double-click it, or open it directly in a modern browser (Chrome or Edge recommended).
   - Because the app fetches the MediaPipe hand-tracking model from a CDN over `https://`, you'll need an internet connection the first time you run it (the browser caches it after that).
   - Some browsers restrict camera access when a page is opened directly as a `file://` URL. If the camera doesn't start, serve the file with any simple local server instead, for example:
     ```bash
     # Python 3 — run this in the folder containing air-piano.html
     python -m http.server 8000
     # then visit http://localhost:8000/air-piano.html in your browser
     ```
3. Click **Start Camera**, allow camera access when prompted, and start moving your index finger over the keys!

---

## 🌐 Browser Requirements

- A modern, up-to-date browser with support for:
  - `getUserMedia` (webcam access)
  - WebAssembly (used by MediaPipe)
  - The Web Audio API
  - ES modules (`<script type="module">`)
- Recommended: **Google Chrome** or **Microsoft Edge**, latest version.
- A working webcam and reasonably bright, even lighting.
- Audio playback requires at least one click on the page first (browser autoplay policies) — clicking "Start Camera" takes care of this.

---

## ✋ How Hand Tracking Works (Beginner-Friendly)

Think of `HandLandmarker` as a model that has learned what a hand looks like from thousands of example photos. For every webcam frame, it outputs 21 tiny (x, y) coordinates — one for each hand joint (fingertips, knuckles, wrist, etc.) — all as fractions between 0 and 1 relative to the frame size.

The app only cares about **one** of those 21 points: the index fingertip. It converts that fractional coordinate into a real pixel position on your screen, lines it up against the piano keys, and asks: *"is this point inside a key's box right now?"* If yes, that key lights up and its note plays. It's the same idea as clicking a button with a mouse — except the "cursor" is your fingertip, tracked by the camera instead of a mouse driver.

---

## 🚀 Future Improvements

- Multi-finger / chord support (play more than one note at once).
- Depth-based velocity so pressing "harder" (closer to camera) plays louder.
- Record & playback of what you've played.
- MIDI export of a recorded performance.
- Custom key mapping / scales (pentatonic, minor, etc.) instead of only chromatic white/black keys.
- Mobile camera support with touch fallback for devices without a good front camera.

---

## 👤 Author

ANNIEEONE
