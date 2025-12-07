# DeepSeek-OCR Docker Hub 镜像

## 🎉 All-in-One 镜像已发布

**Docker Hub**: `neosun/deepseek-ocr`

### 镜像特点

✅ **完全独立** - 包含所有依赖和预下载的模型（~7GB）  
✅ **无需外部下载** - 首次启动即可使用，无需等待模型下载  
✅ **GPU 加速** - 支持 NVIDIA GPU (CUDA)  
✅ **完整功能** - 包含所有 API 端点和 Web UI  
✅ **生产就绪** - 经过完整测试验证  

---

## 🚀 快速开始

### 1. 拉取镜像

```bash
docker pull neosun/deepseek-ocr:latest
```

或指定版本：

```bash
docker pull neosun/deepseek-ocr:v3.3-allinone
```

### 2. 运行容器

**使用 GPU**:
```bash
docker run -d \
  --name deepseek-ocr \
  --gpus all \
  -p 8001:8001 \
  --shm-size=8g \
  neosun/deepseek-ocr:latest
```

**仅 CPU** (不推荐，速度很慢):
```bash
docker run -d \
  --name deepseek-ocr \
  -p 8001:8001 \
  neosun/deepseek-ocr:latest
```

### 3. 访问服务

- **Web UI**: http://localhost:8001
- **API**: http://localhost:8001/ocr
- **健康检查**: http://localhost:8001/health
- **API 文档**: http://localhost:8001/docs

---

## 📋 可用标签

| 标签 | 说明 | 大小 |
|------|------|------|
| `latest` | 最新稳定版本 | ~40GB |
| `v3.3-allinone` | v3.3 完整版本 | ~40GB |

---

## 🔌 API 端点

### 1. 单图片 OCR
```bash
curl -X POST http://localhost:8001/ocr \
  -F "file=@image.png" \
  -F "prompt_type=ocr"
```

### 2. PDF 完整 OCR
```bash
curl -X POST http://localhost:8001/ocr-pdf \
  -F "file=@document.pdf" \
  -F "prompt_type=document" \
  --max-time 600
```

### 3. PDF 转图片
```bash
curl -X POST http://localhost:8001/pdf-to-images \
  -F "file=@document.pdf"
```

---

## 🐳 Docker Compose

创建 `docker-compose.yml`:

```yaml
version: '3.8'

services:
  deepseek-ocr:
    image: neosun/deepseek-ocr:latest
    container_name: deepseek-ocr
    ports:
      - "8001:8001"
    shm_size: 8g
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8001/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 5m
```

启动：
```bash
docker compose up -d
```

---

## 📊 系统要求

### 最低配置
- **GPU**: NVIDIA GPU with 16GB+ VRAM (推荐 24GB+)
- **RAM**: 16GB+
- **磁盘**: 50GB+ 可用空间
- **CUDA**: 11.8+

### 推荐配置
- **GPU**: NVIDIA A100 / RTX 4090 / V100
- **RAM**: 32GB+
- **磁盘**: 100GB+ SSD

---

## ⚡ 性能指标

- **模型加载**: ~56 秒（首次）
- **单图识别**: ~3-5 分钟
- **PDF 识别**: ~3-5 分钟/页
- **GPU 内存**: ~14GB（加载时）

---

## 🔧 环境变量

```bash
docker run -d \
  --name deepseek-ocr \
  --gpus all \
  -p 8001:8001 \
  -e CUDA_VISIBLE_DEVICES=0 \
  -e API_HOST=0.0.0.0 \
  --shm-size=8g \
  neosun/deepseek-ocr:latest
```

---

## 📖 完整文档

- **GitHub**: https://github.com/neosun100/DeepSeek-OCR-WebUI
- **API 文档**: 容器内 `/app/API.md`
- **MCP 支持**: 容器内 `/app/MCP_SETUP.md`

---

## 🐛 故障排除

### 问题 1: 容器启动失败
```bash
# 查看日志
docker logs deepseek-ocr

# 检查 GPU
nvidia-smi
```

### 问题 2: 内存不足
```bash
# 增加共享内存
docker run --shm-size=16g ...
```

### 问题 3: 端口冲突
```bash
# 使用其他端口
docker run -p 8002:8001 ...
```

---

## 📝 更新日志

### v3.3-allinone (2025-12-07)
- ✅ 包含预下载的模型
- ✅ 新增 PDF OCR 端点
- ✅ 支持 MCP 协议
- ✅ 完整 API 文档
- ✅ GPU 内存自动管理

---

## 📞 支持

- **Issues**: https://github.com/neosun100/DeepSeek-OCR-WebUI/issues
- **Discussions**: https://github.com/neosun100/DeepSeek-OCR-WebUI/discussions

---

## 📄 许可证

MIT License - 详见 [LICENSE](https://github.com/neosun100/DeepSeek-OCR-WebUI/blob/main/LICENSE)

---

**⭐ 如果这个项目对你有帮助，请给个 Star！**
