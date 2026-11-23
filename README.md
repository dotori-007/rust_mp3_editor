# 🎵 PureRust MP3 Editor Studio (rust_mp3_editor)

> **Pure Rust Ultra-Lightweight, High-Performance Native Audio Studio & Nightcore/Lolicore DSP Processor**  
> Running as a single native executable (`.exe`, ~7.3MB) without heavy runtimes or web browsers, featuring a 60FPS hardware-accelerated dark studio UI, real-time zero-latency DSP audio effects, a SoundCloud-style interactive waveform timeline dock, and a high-speed online audio extractor.

---

## ✨ Key Features

### 1. 🎛️ Zero-Latency Real-Time DSP Audio Engine
Instantaneous zero-latency audio DSP processing applied directly to playback as you adjust sliders or type numerical values.

- **Independent Tempo Control & Pitch-Preserved Time Stretch**:
  - Scales playback speed (`0.25x` ~ `4.00x`) while **perfectly preserving original vocal and instrumental pitch**.
  - Internal zero-latency `DualDelayPitchShifter` accurately counterbalances pitch shifts caused by resampling speedup ($+12 \times \log_2(\text{speed})$) in real time.
- **🔗 Varispeed / Turntable Mode**:
  - Toggle checkbox to switch to classic vinyl turntable / tape reel behavior where speed and pitch scale together proportionately for authentic Nightcore aesthetics.
- **🎵 Precision Pitch Shifter**:
  - `-24.0` ~ `+24.0` semitones with fine 0.1 semitone precision adjustment.
- **🔊 2nd-Order IIR Biquad Low-Shelf Bass Boost**:
  - `0.0dB` ~ `+24.0dB` punchy low-end amplification at `100Hz`.
- **💨 2nd-Order IIR Biquad High-Shelf Treble / Vocal Air Boost**:
  - `0.0dB` ~ `+20.0dB` pristine vocal presence and high-end air boost at `8kHz`.
- **🌌 Schroeder Reverb**:
  - Dreamy reverberation engine based on 4 parallel comb filters and 2 series allpass filters (`0%` ~ `100%`).
- **⌨️ Integrated 3-Tier Value Input Controls**:
  - `[Label]` + `[Direct Numerical Input Box (DragValue)]` + `[Slider]`.
  - Supports both smooth mouse drag adjustments and direct keyboard typing (Enter) into the number box for exact numeric precision.

---

### 2. 🌊 SoundCloud-Style Interactive Waveform Timeline Dock
An interactive waveform dock anchored at the bottom gives an intuitive, panoramic view of the entire audio flow.

- **Streaming Peak Extraction**: Streaming decode of the entire audio track into 220 visual peak buckets.
- **Neon Gradient Mirrored Bars**: Clear visual contrast between played sections (Cyan $\rightarrow$ Purple gradient) and unplayed sections (Dark Slate Blue).
- **Sub-Pixel 0.0px Synchronization**: Playhead line (`playhead_x`) aligns pixel-perfectly with waveform active bar boundaries.
- **Interactive Seek-on-Click / Drag**: Click or drag across the waveform to instantly seek playback position.
- **Mouse Hover Guide & Timecode Tooltip**: Dynamic timeline cursor showing current hover timecode.
- **Spacebar Shortcut**: Instant play/pause toggle (smartly bypasses toggle when typing in text fields).

---

### 3. ⚡ One-Click Genre Preset Chips
- ⚡ **Classic Nightcore**: 1.25x Varispeed Turntable + 2.0dB Bass Boost
- 💎 **Modern Nightcore**: 1.20x Vocal Tuning (+3 Semitones) + 3.5dB Treble Air + 10% Dream Reverb
- 🔥 **Lolicore Hyper**: 1.45x (+5 Semitones) + Hyper-speed rhythmic punch
- ⚡ **Lolicore Extreme**: 1.65x (+7 Semitones) + Extreme speed & pitch
- 🌙 **Slowed & Reverb**: 0.85x Slowdown (-3 Semitones) + 25% Dream Reverb (Dreamcore vibe)

---

### 4. 🌐 High-Speed Online Audio Extractor (YouTube & SoundCloud)
- Paste URL into the top extraction bar and hit the button to extract high-quality audio streams.
- **Multi-threaded parallel download** (`--concurrent-fragments 4`, `-f ba/b`) providing up to 3x faster extraction speeds.
- **Suppression of background console popups** (`CREATE_NO_WINDOW = 0x08000000`).
- Automatically loads directly into the editing track upon download completion.

---

### 5. 📦 FIFO Queue-Based Smart Disk Management
- Automatically purges the oldest file when temporary items in `uploads/` or `outputs/` exceed **10 files**.
- Safely protects current in-use downloads or exports via `preserve_path`.
- Cleans and initializes old queue items upon app launch (`main.rs`).

---

### 6. 💾 Lossless Export & Multi-Track Merger
- **Supported Export Formats**:
  - `MP3`: 192~320 kbps high-bitrate
  - `WAV`: 16-bit PCM lossless
  - `FLAC`: Lossless compression
- **Track Merger**:
  - Add multiple audio files, reorder tracks (▲/▼), and merge losslessly into a single file.

---

## 🏗️ Architecture

Built with pure Rust native modules to minimize external dependencies and maintain solid performance:

```
rust_mp3_editor/
├── Cargo.toml                 # Rust package manifest & dependencies
├── run.bat                    # One-click desktop app runner script
├── README.md                  # Project documentation
├── src/
│   ├── main.rs                # Entry point, eframe setup, FIFO queue init
│   ├── app.rs                 # Main app loop, spacebar shortcut, worker threads
│   ├── audio/
│   │   ├── mod.rs
│   │   ├── dsp.rs             # Zero-latency DSP stream (IIR Low/High-Shelf, pitch shifter, reverb)
│   │   ├── engine.rs          # FFmpeg encoding, track merger, metadata parser
│   │   ├── extractor.rs       # yt-dlp multi-threaded online audio extractor
│   │   └── player.rs          # Rodio-based in-app player & 220-bucket waveform extractor
│   ├── ui/
│   │   ├── mod.rs
│   │   ├── theme.rs           # Deep dark studio palette & font bindings
│   │   ├── waveform.rs        # Interactive waveform timeline dock renderer
│   │   ├── tab_editor.rs      # Track editor (URL bar + waveform + real-time DSP)
│   │   ├── tab_extract.rs     # Online audio extractor manager
│   │   └── tab_merge.rs       # Multi-track merger tab
│   └── utils/
│       ├── mod.rs
│       ├── paths.rs           # System32 safe path resolution & 10-item FIFO queue
│       └── tools.rs           # Tool auto-discovery & CREATE_NO_WINDOW helper
├── uploads/                   # Temporary online audio downloads (Max 10 FIFO)
└── outputs/                   # Exported audio files (Max 10 FIFO)
```

---

## 🚀 Getting Started

### Prerequisites
- [Rust toolchain](https://rustup.rs/) (1.75+ recommended)
- (Optional) `ffmpeg` and `yt-dlp` for audio rendering and web stream extraction (auto-discovered from PATH or local directory)

### Running the Application
```powershell
# Clone the repository
git clone https://github.com/dotori-007/rust_mp3_editor.git
cd rust_mp3_editor

# One-click launch (run.bat)
.\run.bat

# Or run directly via Cargo
cargo run --release
```

### Running Tests
```powershell
cargo test
# (All unit/integration tests & FIFO 10-item queue tests passing)
```

---

## 🎹 Keybindings & Controls

| Shortcut / Action | Description |
|:---|:---|
| **Space** | Toggle play / pause for active track (safely typing space in input fields) |
| **Mouse Click & Drag (Waveform)** | Instant seek to selected timestamp |
| **Mouse Hover (Waveform)** | Displays real-time timecode tooltip |
| **Click Number Box (DSP)** | Enter numeric value directly using keyboard and press Enter |
| **Drag Number Box (DSP)** | Fine-tune numeric parameters with left/right mouse drag |
| **File Drag & Drop** | Drag and drop audio files directly into window to load |

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).