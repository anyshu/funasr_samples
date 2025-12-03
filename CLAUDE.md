# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repository contains sample client implementations for FunASR (Alibaba's open-source speech recognition service). Clients communicate with a FunASR WebSocket server to perform ASR (Automatic Speech Recognition) in three modes:
- **offline**: Non-streaming batch transcription
- **online**: Real-time streaming transcription
- **2pass**: Combines streaming (for low latency) and offline (for accuracy)

## Repository Structure

- `python/` - Python WebSocket clients
- `cpp/` - C++ WebSocket clients (offline and 2pass)
- `java/` - Java WebSocket client
- `html/` - Browser-based demo with microphone/file input

## Build Commands

### Python Client
```shell
pip3 install websockets  # or: pip3 install -r python/requirements_client.txt
python python/funasr_wss_client.py --host 0.0.0.0 --port 10095 --mode offline
```

### C++ Client
```shell
# Prerequisites: libssl-dev (Ubuntu), openssl-devel (CentOS), or brew install openssl (macOS)
# On macOS: export OPENSSL_ROOT_DIR=/opt/homebrew/opt/openssl@1.1

cd cpp/websocket_client
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=release ..
make

# Run:
./bin/funasr-wss-client --server-ip 127.0.0.1 --port 10095 --wav-path ../audio/asr_example.wav
./bin/funasr-wss-client-2pass --server-ip 127.0.0.1 --port 10095 --wav-path ../audio/asr_example.pcm
```

### Java Client
```shell
cd java
make downjar         # Download dependencies
make buildwebsocket  # Compile
make runclient       # Run with default settings
```

## Key Parameters

Common parameters across all clients:
- `--host/--server-ip`: FunASR server IP address
- `--port`: Server port (default: 10095)
- `--mode`: `offline`, `online`, or `2pass`
- `--chunk_size`: Streaming chunk configuration, e.g., "5,10,5" (600ms) or "8,8,4" (480ms)
- `--audio_in/--wav-path`: Input audio file or wav.scp (Kaldi-style list)
- `--hotword`: Hotword boosting file (format: "word score" per line)
- `--ssl/--is-ssl`: Enable/disable SSL (1=enabled, 0=disabled)
- `--use_itn`: Enable inverse text normalization (1=enabled, 0=disabled)

## WebSocket Protocol

All clients use the same JSON protocol:
1. Send initial config: `{"mode": "2pass", "chunk_size": [5,10,5], "is_speaking": true, ...}`
2. Send audio data as binary frames
3. Send end signal: `{"is_speaking": false}`
4. Receive results: `{"text": "transcription", "mode": "offline", "timestamp": [...]}`

## Python API Usage

```python
from funasr_client_api import Funasr_websocket_recognizer

rcg = Funasr_websocket_recognizer(host="127.0.0.1", port="30035", is_ssl=True, mode="2pass")
text = rcg.feed_chunk(audio_data)  # Send PCM chunks
text = rcg.close(timeout=3)        # Get final result
```
