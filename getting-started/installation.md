# Installation

## Download Binary

**macOS (Apple Silicon):**
```bash
curl -L https://github.com/not7/core/raw/main/dist/not7-darwin-arm64 -o not7
chmod +x not7
```

**macOS (Intel):**
```bash
curl -L https://github.com/not7/core/raw/main/dist/not7-darwin-amd64 -o not7
chmod +x not7
```

**Linux (AMD64):**
```bash
curl -L https://github.com/not7/core/raw/main/dist/not7-linux-amd64 -o not7
chmod +x not7
```

**Linux (ARM64):**
```bash
curl -L https://github.com/not7/core/raw/main/dist/not7-linux-arm64 -o not7
chmod +x not7
```

**Windows:**
Download: https://github.com/not7/core/raw/main/dist/not7-windows-amd64.exe

## Configure

```bash
cp not7.conf.example not7.conf
```

Edit `not7.conf`:
```
OPENAI_API_KEY=sk-your-actual-api-key-here
```

## Verify

```bash
./not7 --help
```

You should see usage information.

## First Run

```bash
./not7 run examples/poem-generator.json
```

If you see a poem, you're all set! ✅

## Troubleshooting

**"Failed to load config"**
- Make sure `not7.conf` exists in the same directory
- Check that `OPENAI_API_KEY` is set

**"Permission denied"**
- Run: `chmod +x not7`

**"API key not configured"**
- Edit `not7.conf` and add your OpenAI API key

---

Next: [Quick Start →](quick-start.md)

