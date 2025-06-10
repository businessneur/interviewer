# 🚀 Deployment Configuration

---

## 🐳 Docker Compose Structure

```yaml
version: '3.8'
services:
  # Load Balancer
  nginx:
    image: nginx:alpine
    ports: ["80:80", "443:443"]

  # STT Service
  stt-worker:
    image: faster-whisper:gpu
    deploy:
      replicas: 3
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]

  # TTS Services
  tts-piper:
    image: piper-tts:cpu
    deploy:
      replicas: 5

  tts-kokoro:
    image: kokoro-tts:gpu
    deploy:
      replicas: 2
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]

  # Queue System
  redis:
    image: redis:alpine

  celery-worker:
    depends_on: [redis]
    deploy:
      replicas: 4

  # Database
  postgresql:
    image: postgres:15
    environment:
      POSTGRES_DB: interview_platform

  # Monitoring
  prometheus:
    image: prom/prometheus

  grafana:
    image: grafana/grafana
