# 📘 SoloScribe – On‑Premise Meeting Recorder & Summarizer

## Overview

SoloScribe is an on‑premise Docker‑packaged appliance that:

* Captures meeting audio/video via a headless browser extension
* Transcribes via Whisper
* Summarizes and extracts action items via an LLM + RAG
* Integrates SAML/SCIM for enterprise auth
* Stores encrypted artifacts in customer‑managed S3

## Tech Stack

| Component        | Technology / Tools                                         |
| ---------------- | ---------------------------------------------------------- |
| Recorder Service | Headless Chrome + Puppeteer + Browser Extension            |
| Transcription    | PyTorch + Whisper (GPU/CPU fallback)                       |
| Summarization    | FastAPI + OpenAI / local LLaMA-style model (e.g., Mixtral) |
| Auth             | SAML 2.0, SCIM 2.0 microservice (e.g., SSOREADY or custom) |
| Storage          | AWS CLI SDK → S3-compatible object storage                 |
| Containerization | Docker / Docker Compose                                    |
| CI/CD            | GitHub Actions                                             |
| Monitoring       | Prometheus, Grafana, ELK Stack                             |
| Encryption       | TLS (Ingress), AWS KMS encryption for S3                   |

---

## 🚀 Quickstart – 17 Steps to Get Started

1. **Clone repo & bootstrap submodules**

   ```bash
   git clone <repo-url> && cd soloscribe  
   git submodule update --init --recursive
   ```

2. **Install Docker & Docker‑Compose**
   Ensure Docker ≥20.x, Docker Compose ≥2.x on host (Linux/Windows/Mac).

3. **Configure `.env` file**
   Set up environment variables:

   ```
   S3_BUCKET=your-bucket-name
   S3_REGION=your-region
   SAML_METADATA_URL=https://idp.example.com/…
   SCIM_SECRET=super-secret
   LLM_API_KEY=…
   ```

4. **Implement Recorder Service**

   * Build Chrome extension for Zoom/Teams/Webex detection
   * Use Puppeteer in `recorder/` to join and record silently

5. **Build Transcription Service**

   * Containerize Whisper model inference (GPU with CUDA, CPU fallback)
   * Endpoint: `/transcribe?file=/path/to/audio.mp4`

6. **Build Summarization Service**

   * FastAPI app with endpoints like `/summarize`
   * Integrate RAG via local embedding store or OpenAI
   * Accept JSON input → output summary + action items

7. **Develop Auth Service**

   * Create SAML SP for login flow
   * Add SCIM 2.0 endpoints (`/scim/v2/Users`, `/Groups`)
   * Use SSOREADY or build lightweight microservice

8. **Create Storage Service**

   * Wrap AWS SDK to upload artifacts to S3
   * Manage encryption via AWS KMS
   * Support lifecycle policy ENV variables

9. **Assemble Docker Compose**

   * Services: recorder, transcriber, summarizer, auth, storage
   * Expose ports, set up internal networking, volumes

10. **Add Monitoring (Prometheus + Grafana)**

* Instrument key metrics: jobs queued, process times, GPU usage
* Add exporters to each relevant service

11. **Set Up Logging (ELK Stack)**

* Centralize logs via Filebeat or Fluentd
* Configure Elasticsearch index & Kibana dashboard

12. **Add TLS and Security**

* Generate self‑signed certs or use customer‑provided CA
* Ensure HTTPS between browser <> container and containers <> S3

13. **Write Browser Extension UI**

* Build popup to start/stop recording
* Inject script to detect meeting pages and trigger headless recording

14. **Build & Tag Docker Images**

```bash
docker-compose build
docker tag recorder:latest soloscribe/recorder:v1.0.0
```

15. **Write GitHub Actions Workflows**

* CI: build/test containers, lint code
* CD: on tag push → publish images to Docker Hub

16. **Test Full Workflow Locally**

* Launch Compose stack with a test meeting
* Validate audio capture → transcribe → summarize → store

17. **Package & Distribute**

* Export single `docker-compose.yml` + `.env.sample` + TLS cert template
* Provide installer script (`install.sh`) for quick bootstrap
* Include license-key activation step at startup

---

## 🛠 Development Tips

* Use `CUDA_VISIBLE_DEVICES=0` to target GPU hosts
* Enable Prometheus `/metrics` endpoints in Python apps
* Provide fallback CPU mode in Whisper
* Use versioning tags: `v1.0.0`, `v1.1.0`

---

## 🧠 What’s Next

* Add managed cloud fallback (AWS GPU-backed Whisper/LLM)
* Support multi-node/distributed GPU caching
* Optimize Docker images (<50 MB target)

---

End of README – feel free to adapt models, testing frameworks, or deployment scripts as needed.
