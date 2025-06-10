
### 💰 Cost Optimization Strategy

We've implemented a robust strategy to minimize operational costs while maintaining high performance and reliability:

#### Resource Allocation
Our compute resources are strategically allocated based on workload characteristics:

* **70% CPU workload:** Primarily handled by **Piper TTS**, which is highly cost-effective due to its CPU optimization.
* **20% GPU workload:** Dedicated to **Faster-Whisper STT**, leveraging GPUs for its high-performance demands.
* **10% GPU workload:** Allocated to **Kokoro TTS**, serving as a higher-quality fallback.

#### Auto-Scaling Benefits
Leveraging intelligent auto-scaling mechanisms provides significant cost savings:

* **Scale to Zero:** Celery workers can automatically scale down to zero instances during idle periods, drastically reducing compute costs when demand is low.
* **Queue-based Scaling:** Workers only scale up when there are tasks in the queue, ensuring resources are provisioned precisely when needed.
* **Spot Instances:** We can utilize cheaper spot instances for non-critical or interruptible workloads, further optimizing costs.
* **Caching:** By caching common interview questions and generated audio files in Redis, we reduce redundant compute on our STT/TTS backends, lowering operational expenses.
