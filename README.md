# FunASR Samples

FunASR 实时语音听写服务的示例客户端代码集合。

## 来源

本仓库代码来源于 FunASR 官方文档：
- **下载地址**: https://isv-data.oss-cn-hangzhou.aliyuncs.com/ics/MaaS/ASR/sample/funasr_samples.tar.gz
- **文档**: [FunASR 实时语音听写服务开发指南](https://github.com/modelscope/FunASR/blob/c4ac64fd5d24bb3fc8ccc441d36a07c83c8b9015/runtime/docs/SDK_advanced_guide_online_zh.md)

## 快速开始

```bash
# 下载示例代码
wget https://isv-data.oss-cn-hangzhou.aliyuncs.com/ics/MaaS/ASR/sample/funasr_samples.tar.gz

# 解压
tar -zxvf funasr_samples.tar.gz
```

## 目录结构

```
funasr_samples/
├── audio/          # 测试音频文件
├── python/         # Python WebSocket 客户端
├── cpp/            # C++ WebSocket 客户端
├── java/           # Java WebSocket 客户端
└── html/           # 浏览器端演示
```

## 支持的模式

| 模式 | 说明 |
|------|------|
| `offline` | 非实时，一句话识别 |
| `online` | 实时流式识别 |
| `2pass` | 实时流式 + 离线纠错（兼顾低延迟和高准确率） |

## 客户端使用

### Python

```bash
pip3 install websockets
python python/funasr_wss_client.py --host 127.0.0.1 --port 10095 --mode 2pass
```

### C++

```bash
cd cpp/websocket_client
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=release ..
make
./bin/funasr-wss-client --server-ip 127.0.0.1 --port 10095 --wav-path ../audio/asr_example.wav
```

### Java

```bash
cd java
make downjar && make buildwebsocket && make runclient
```

### HTML (浏览器)

直接在浏览器中打开 `html/static/index.html`，支持麦克风实时录音和文件上传。

## 常用参数

| 参数 | 说明 |
|------|------|
| `--host/--server-ip` | FunASR 服务器地址 |
| `--port` | 服务端口 (默认: 10095) |
| `--mode` | 识别模式: offline/online/2pass |
| `--chunk_size` | 流式分块配置，如 "5,10,5" |
| `--hotword` | 热词文件路径 |
| `--ssl` | 是否启用 SSL (1=启用) |

## 相关链接

- [FunASR GitHub](https://github.com/modelscope/FunASR)
- [FunASR 文档](https://github.com/modelscope/FunASR/tree/main/runtime/docs)
