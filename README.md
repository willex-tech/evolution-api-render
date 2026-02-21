# Evolution API on Render

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/willex-tech/evolution-api-render)

Evolution API deployment for WhatsApp Business automation testing. This repo provides a Render Blueprint for one-click deployment of the [Evolution API](https://github.com/EvolutionAPI/evolution-api) with managed PostgreSQL.

---

## What is Evolution API?

Evolution API is an open-source WhatsApp Business API wrapper built on top of WhatsApp Web (via Baileys/WWebJS). It provides:

- Multi-instance WhatsApp management
- REST API for sending/receiving messages
- Webhook support for real-time events
- QR code & pairing code authentication
- Chat management and media handling

---

## Quick Deploy

1. Click the **"Deploy to Render"** button above
2. Render will automatically:
   - Create a Web Service running Evolution API
   - Provision a managed PostgreSQL database
   - Generate a secure API key
   - Configure environment variables

---

## Configuration

| Variable | Description |
|----------|-------------|
| `SERVER_URL` | Auto-set to your Render service URL |
| `AUTHENTICATION_API_KEY` | Auto-generated — copy from Environment tab |
| `DATABASE_CONNECTION_URI` | Auto-injected from managed Postgres |
| `CACHE_LOCAL_ENABLED` | `true` — uses local memory cache |
| `CACHE_REDIS_ENABLED` | `false` — Redis disabled for simplicity |

---

## Post-Deploy: Get Your API Key

1. Go to [Render Dashboard](https://dashboard.render.com/)
2. Select your `evolution-api-willex` service
3. Go to **Environment** tab
4. Copy `AUTHENTICATION_API_KEY` value

---

## API Usage

### Health Check
```bash
curl -H "apikey: YOUR_API_KEY" \
  https://evolution-api-willex.onrender.com/
```

### Create Instance (QR Code)
```bash
curl -X POST \
  -H "apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "instanceName": "my-instance",
    "integration": "WHATSAPP-BAILEYS",
    "qrcode": true
  }' \
  https://evolution-api-willex.onrender.com/instance/create
```

### Create Instance (Pairing Code)
```bash
curl -X POST \
  -H "apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "instanceName": "my-instance",
    "integration": "WHATSAPP-BAILEYS",
    "qrcode": false,
    "number": "234XXXXXXXXXX"
  }' \
  https://evolution-api-willex.onrender.com/instance/create

# Request pairing code
curl -X POST \
  -H "apikey: YOUR_API_KEY" \
  https://evolution-api-willex.onrender.com/instance/my-instance/requestPairingCode
```

### Check Connection State
```bash
curl -H "apikey: YOUR_API_KEY" \
  https://evolution-api-willex.onrender.com/instance/connectionState/my-instance
```

### Send Message
```bash
curl -X POST \
  -H "apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "number": "234XXXXXXXXXX",
    "textMessage": { "text": "Hello from Evolution API!" }
  }' \
  https://evolution-api-willex.onrender.com/message/sendText/my-instance
```

---

## Known Limitations

| Issue | Cause | Workaround |
|-------|-------|------------|
| **QR code not displaying** | Render free tier WebSocket limitations | Use pairing code method instead |
| **Pairing code hangs** | Same WebSocket issue | Deploy on VPS or upgrade Render plan |
| **Cold starts** | Free tier spins down after inactivity | First request may take 30s+ to wake |

**Status:** Currently blocked on Render free tier due to WebSocket restrictions. For production use, consider:
- Deploying on VPS (WITEL-VPS-001)
- Upgrading to Render paid tier
- Using WhatsApp Business Cloud API instead

---

## Troubleshooting

### Instance not connecting
1. Check if number is linked elsewhere (WhatsApp → Settings → Linked Devices)
2. Delete and recreate instance with fresh name
3. Use incognito browser for QR scanning
4. Check Render service logs

### API returns 401
- Verify API key is correct
- Ensure `apikey` header (not `ApiKey` or `API-Key`)

### Database connection errors
- Managed Postgres is auto-configured
- Check `DATABASE_CONNECTION_URI` env var is populated

---

## Resources

- [Evolution API Docs](https://docs.evolution-api.com/)
- [Evolution API GitHub](https://github.com/EvolutionAPI/evolution-api)
- [Render Web Services](https://render.com/docs/web-services)
- [Willex OS Project](https://github.com/willex-tech/platform-infra)

---

## License

This deployment config is ISC licensed. Evolution API has its own [license](https://github.com/EvolutionAPI/evolution-api/blob/main/LICENSE.md).

---

Maintained by [Willex Tech](https://willextech.com)
