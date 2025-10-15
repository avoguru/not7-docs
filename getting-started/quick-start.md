# Quick Start (5 Minutes)

## 1. Download & Setup (2 min)

```bash
# Download
curl -L https://github.com/not7/core/raw/main/dist/not7-darwin-arm64 -o not7
chmod +x not7

# Configure
cp not7.conf.example not7.conf
# Edit not7.conf: OPENAI_API_KEY=sk-your-key
```

## 2. Run Your First Agent (1 min)

```bash
./not7 run examples/poem-generator.json
```

**Output:** Creative poem about AI agents ✅

## 3. Deploy via API (1 min)

Start server:
```bash
./not7 run &
```

Deploy agent:
```bash
curl -X POST http://localhost:8080/api/v1/agents \
  -H "Content-Type: application/json" \
  -d @examples/poem-generator.json
```

## 4. Run Multi-Node Example (1 min)

```bash
curl -X POST http://localhost:8080/api/v1/agents/run \
  -H "Content-Type: application/json" \
  -d @examples/feedback-analyzer.json
```

**Output:** Multi-step analysis with actionable insights ✅

---

## What You've Learned

✅ How to run agents locally  
✅ How to deploy agents via API  
✅ Single-node vs multi-node agents  
✅ Server mode basics  

---

Next: [Agent Development →](../agent-development/overview.md)

