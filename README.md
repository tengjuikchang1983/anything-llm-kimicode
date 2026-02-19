# AnythingLLM with Kimi Code Support

A fork of [AnythingLLM](https://github.com/Mintplex-Labs/anything-llm) with integrated **Kimi Code API** support.

[![Docker](https://img.shields.io/badge/docker-ready-blue.svg)](https://www.docker.com/)
[![Kimi Code](https://img.shields.io/badge/Kimi%20Code-supported-green.svg)](https://www.moonshot.cn/)

## 🚀 Quick Start

### Prerequisites
- Docker Desktop installed
- Kimi Code API key (get from [Kimi](https://www.moonshot.cn/))

### Option 1: Docker Compose (Recommended)

1. Clone this repository:
```bash
git clone https://github.com/tengjuikchang1983/anything-llm-kimicode.git
cd anything-llm-kimicode
```

2. Copy the environment template:
```bash
cp .env.example .env
```

3. Edit `.env` and add your Kimi Code API key:
```bash
KIMI_CODE_API_KEY=your-api-key-here
```

4. Start the containers:
```bash
docker-compose up -d
```

5. Access AnythingLLM at http://localhost:3001

### Option 2: Docker Run

```bash
docker run -d \
  --name anythingllm-kimi \
  -p 3001:3001 \
  -e KIMI_CODE_API_KEY=your-api-key-here \
  -e LLM_PROVIDER='kimicode' \
  mintplexlabs/anythingllm:latest
```

Then manually apply the patches from this repo to the running container.

## 🔧 Configuration

### Environment Variables

| Variable | Required | Description | Default |
|----------|----------|-------------|---------|
| `KIMI_CODE_API_KEY` | ✅ Yes | Your Kimi Code API key | - |
| `KIMI_CODE_MODEL_PREF` | No | Preferred model | `kimi-for-coding` |
| `LLM_PROVIDER` | No | Set to 'kimicode' to use Kimi | - |

### Available Models

- **kimi-for-coding** (default)
  - Context Window: 262,144 tokens
  - Max Output: 32,768 tokens
  - Supports reasoning, images, and video input

## 📝 Setup Instructions

### First Time Setup

1. After starting the container, open http://localhost:3001
2. Complete the initial setup wizard
3. Go to **Settings → LLM Preference**
4. Select **Kimi Code** from the dropdown
5. Your API key should be auto-configured from the environment
6. Test by creating a new workspace and chatting

### Login Credentials

Default credentials (if not changed during setup):
- Username: `admin`
- Password: (set during first run)

## 🔑 Getting Your Kimi Code API Key

1. Visit [Kimi](https://www.moonshot.cn/)
2. Sign up or log in to your account
3. Navigate to API settings
4. Generate a new API key
5. Copy the key and paste it into your `.env` file

## 🛠️ Technical Details

### Custom Headers

This fork adds the following headers required by Kimi Code API:
- `User-Agent: RooCode/1.0.0`
- `X-Client-Name: roo-code`
- `X-Client-Version: 1.0.0`

These headers identify the client as a coding agent, which is required by Kimi's API to prevent 403 errors.

### Files Modified

- `server/utils/AiProviders/kimiCode/index.js` - New Kimi Code provider implementation
- `server/utils/helpers/customModels.js` - Added Kimi Code to supported models list

### Original README

For the original AnythingLLM documentation, see [README.original.md](./README.original.md).

## 🐛 Troubleshooting

### 403 Forbidden Error
If you see "Kimi For Coding is currently only available for Coding Agents", ensure you're using this patched version with the proper headers.

### Container Won't Start
Check logs:
```bash
docker logs anythingllm-kimi
```

### API Key Not Working
- Verify the key is correct
- Check that `KIMI_CODE_API_KEY` is set in your environment
- Restart the container after changing environment variables

## 🔄 Updates

To pull latest changes from this fork:
```bash
git pull origin main
docker-compose up -d --build
```

## 📄 License

This project maintains the same license as the original [AnythingLLM](https://github.com/Mintplex-Labs/anything-llm) project.

## 🙏 Credits

- Original [AnythingLLM](https://github.com/Mintplex-Labs/anything-llm) by Mintplex Labs
- Kimi Code API by Moonshot AI

---

**Note**: This is a fork with custom patches for Kimi Code integration. For the original AnythingLLM, visit the [official repository](https://github.com/Mintplex-Labs/anything-llm).
