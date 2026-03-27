# AnythingLLM with Kimi Code Support

A fork of [AnythingLLM](https://github.com/Mintplex-Labs/anything-llm) with fully integrated **Kimi Code API** support, including UI provider selection, model listing, and Roo Code client headers.

[![Docker](https://img.shields.io/badge/docker-ready-blue.svg)](https://www.docker.com/)
[![Kimi Code](https://img.shields.io/badge/Kimi%20Code-supported-green.svg)](https://www.moonshot.cn/)

## Quick Start

### Prerequisites
- Docker Engine or Docker Desktop installed
- Kimi Code API key (get from [Kimi / Moonshot AI](https://www.moonshot.cn/))

### Docker Compose (Recommended)

1. Clone this repository:
```bash
git clone https://github.com/tengjuikchang1983/anything-llm-kimicode.git
cd anything-llm-kimicode
```

2. Copy the environment template and add your API key:
```bash
cp .env.example .env
```

3. Edit `.env` and set your Kimi Code API key:
```
KIMI_CODE_API_KEY=your-api-key-here
```

4. Generate dependency lock files (required for first build):
```bash
npm install -g yarn
cd frontend && yarn install && cd ..
cd server && yarn install && cd ..
```

5. Build and start:
```bash
docker compose up -d --build
```

6. Access AnythingLLM at **http://localhost:3001**

> **Note:** The first build takes several minutes (downloads system packages, Node.js, Chromium, and all dependencies). Subsequent builds use cached layers and are much faster.

## Configuration

### Environment Variables

| Variable | Required | Description | Default |
|----------|----------|-------------|---------|
| `KIMI_CODE_API_KEY` | Yes | Your Kimi Code API key | - |
| `KIMI_CODE_MODEL_PREF` | No | Preferred model | `kimi-for-coding` |
| `LLM_PROVIDER` | No | Set to `kimicode` to use Kimi Code as default | - |
| `EMBEDDING_ENGINE` | No | Embedding engine | `native` (built-in) |
| `VECTOR_DB` | No | Vector database | `lancedb` (built-in) |

### Available Models

- **kimi-for-coding** (default)
  - Context Window: 262,144 tokens
  - Max Output: 32,768 tokens
  - Supports reasoning, images, and video input

## Setup Instructions

### First Time Setup

1. After starting the container, open **http://localhost:3001**
2. Complete the initial setup wizard
3. Go to **Settings -> LLM Preference**
4. Select **Kimi Code** from the provider dropdown
5. Enter your Kimi Code API key in the API Key field
6. Select `kimi-for-coding` from the model dropdown
7. Click **Save changes**
8. Create a new workspace and start chatting

### Using via Environment (No UI Setup)

If you set `KIMI_CODE_API_KEY` and `LLM_PROVIDER=kimicode` in your `.env` file before first launch, Kimi Code will be pre-configured as the default provider.

## Getting Your Kimi Code API Key

1. Visit [Moonshot AI / Kimi](https://www.moonshot.cn/)
2. Sign up or log in to your account
3. Navigate to API settings
4. Generate a new API key
5. Copy the key and add it to your `.env` file or paste it in the UI settings

## Technical Details

### Roo Code Client Headers

The Kimi Code API requires specific client identification headers to allow access. This fork sends the following headers with every request:

```
User-Agent: RooCode/1.0.0
X-Client-Name: roo-code
X-Client-Version: 1.0.0
```

Without these headers, the API returns a `403 Forbidden` error with the message "Kimi For Coding is currently only available for Coding Agents".

### Files Modified / Added

**Backend (Server):**
- `server/utils/AiProviders/kimiCode/index.js` - Kimi Code LLM provider implementation
- `server/utils/helpers/index.js` - Added `kimicode` case to `getLLMProvider()`, `getBaseLLMProviderClass()`, and `getBaseLLMProviderModel()`
- `server/utils/helpers/customModels.js` - Added `getKimiCodeModels()` for model listing via API
- `server/utils/helpers/updateENV.js` - Added `KimiCodeApiKey` and `KimiCodeModelPref` env key mappings
- `server/models/systemSettings.js` - Exposed Kimi Code settings to the frontend

**Frontend (UI):**
- `frontend/src/components/LLMSelection/KimiCodeOptions/index.jsx` - API key input and model selection component
- `frontend/src/media/llmprovider/kimicode.png` - Provider logo
- `frontend/src/pages/GeneralSettings/LLMPreference/index.jsx` - Added Kimi Code to `AVAILABLE_LLM_PROVIDERS`
- `frontend/src/pages/OnboardingFlow/Steps/LLMPreference/index.jsx` - Added Kimi Code to onboarding LLM list

**Docker:**
- `docker/Dockerfile` - Added `--ignore-engines` flag for Node.js 18 compatibility with newer AWS SDK
- `frontend/package.json` - Added `regenerator-runtime` dependency, pinned `@phosphor-icons/react` to 2.1.7

### Architecture

```
Browser (React UI on port 3001)
    |
    v
Express Server (port 3001)
    |-- KimiCodeLLM provider --> https://api.kimi.com/coding/v1
    |       (with Roo Code headers)
    |-- Prisma ORM + SQLite
    |-- LanceDB (vector search)
    |
    v
Collector (document processing)
    |-- Puppeteer (web scraping)
    |-- PDF/DOCX/image parsing
    |-- OCR (Tesseract.js)
```

### Original README

For the original AnythingLLM documentation, see [README.original.md](./README.original.md).

## Troubleshooting

### 403 Forbidden Error
If you see "Kimi For Coding is currently only available for Coding Agents", ensure you're using this fork which includes the required Roo Code client headers.

### Container Won't Start
Check logs:
```bash
docker logs anythingllm-kimi
```

### API Key Not Working
- Verify the key is correct by testing directly:
  ```bash
  curl https://api.kimi.com/coding/v1/models \
    -H "Authorization: Bearer YOUR_API_KEY" \
    -H "User-Agent: RooCode/1.0.0" \
    -H "X-Client-Name: roo-code" \
    -H "X-Client-Version: 1.0.0"
  ```
- When saving in the UI, make sure to **re-enter the full API key** (not the masked `****` placeholder)
- Restart the container after changing environment variables: `docker compose restart`

### Build Fails with Missing yarn.lock
Run `yarn install` in both `frontend/` and `server/` directories before building:
```bash
cd frontend && yarn install && cd ..
cd server && yarn install && cd ..
```

### Build Fails with Node Engine Incompatibility
The Dockerfile uses `--ignore-engines` to handle packages that require Node.js >= 20 while the container runs Node.js 18. This is safe and does not affect runtime functionality.

## Updates

To pull latest changes and rebuild:
```bash
git pull origin main
docker compose up -d --build
```

## License

This project maintains the same license as the original [AnythingLLM](https://github.com/Mintplex-Labs/anything-llm) project (MIT).

## Credits

- Original [AnythingLLM](https://github.com/Mintplex-Labs/anything-llm) by Mintplex Labs
- Kimi Code API by Moonshot AI

---

**Note**: This is a fork with full Kimi Code integration (backend provider + frontend UI). For the original AnythingLLM, visit the [official repository](https://github.com/Mintplex-Labs/anything-llm).
