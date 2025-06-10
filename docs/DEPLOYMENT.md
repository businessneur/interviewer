🚀 Deployment Configuration
Docker Compose Structure
yamlversion: '3.8'
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
Kubernetes Deployment
yaml# STT Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: stt-service
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: faster-whisper
        image: faster-whisper:latest
        resources:
          requests:
            nvidia.com/gpu: 1
          limits:
            nvidia.com/gpu: 1

---
# TTS Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tts-piper
spec:
  replicas: 5
  template:
    spec:
      containers:
      - name: piper-tts
        image: piper-tts:latest
        resources:
          requests:
            cpu: 100m
            memory: 150Mi

---
# KEDA ScaledObject for Celery
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: celery-scaler
spec:
  scaleTargetRef:
    name: celery-worker
  triggers:
  - type: redis
    metadata:
      address: redis:6379
      listName: celery
      listLength: '5'
