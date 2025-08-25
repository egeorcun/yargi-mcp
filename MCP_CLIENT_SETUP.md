# Yargi MCP Client Setup Rehberi

Bu rehber, Yargi MCP Server'ını farklı MCP client'larına nasıl ekleyeceğinizi anlatır.

## 🤖 Claude Desktop

### Method 1: FastMCP (Önerilen)
```bash
# Terminal'de çalıştır
fastmcp install yargi-mcp
```

### Method 2: HTTP Endpoint (Coolify Deployment)
Claude Desktop config dosyasını düzenle:

**macOS:**
```bash
code ~/Library/Application\ Support/Claude/claude_desktop_config.json
```

**Windows:**
```bash
code %APPDATA%\Claude\claude_desktop_config.json
```

**Config içeriği:**
```json
{
  "mcpServers": {
    "yargi-mcp": {
      "transport": {
        "type": "http",
        "endpoint": "https://your-app.coolify.io/mcp",
        "headers": {
          "Content-Type": "application/json"
        }
      }
    }
  }
}
```

### Method 3: Local Development
```json
{
  "mcpServers": {
    "yargi-mcp-local": {
      "transport": {
        "type": "http",
        "endpoint": "http://localhost:8000/mcp"
      }
    }
  }
}
```

## 🔥 5ire MCP Client

1. **5ire uygulamasını aç**
2. **Workspace → MCP Servers**
3. **Add MCP Server**
4. **Konfigürasyon:**
   - **Name**: `Yargi MCP`
   - **Type**: `HTTP`
   - **URL**: `https://your-app.coolify.io/mcp`
   - **Authentication**: `None` (veya Bearer token)

## 🚀 Local Test Setup

### 1. Server'ı Çalıştır
```bash
# Repository dizininde
cd /path/to/yargi-mcp
uvicorn asgi_app:app --host 0.0.0.0 --port 8000
```

### 2. Health Check
```bash
curl http://localhost:8000/health
```

**Beklenen yanıt:**
```json
{
  "status": "healthy",
  "service": "Yargı MCP Server",
  "tools_count": 15
}
```

### 3. MCP Endpoint Test
```bash
curl http://localhost:8000/mcp/
```

## 🔐 Authentication (Opsiyonel)

### Bearer Token ile
```json
{
  "mcpServers": {
    "yargi-mcp-auth": {
      "transport": {
        "type": "http",
        "endpoint": "https://your-app.coolify.io/mcp",
        "headers": {
          "Authorization": "Bearer YOUR_JWT_TOKEN_HERE"
        }
      }
    }
  }
}
```

### OAuth Setup
```json
{
  "mcpServers": {
    "yargi-mcp-oauth": {
      "transport": {
        "type": "http",
        "endpoint": "https://your-app.coolify.io/mcp",
        "oauth": {
          "authorization_url": "https://your-app.coolify.io/auth/login",
          "token_url": "https://your-app.coolify.io/token",
          "client_id": "your-client-id"
        }
      }
    }
  }
}
```

## 🛠️ Troubleshooting

### Claude Desktop Görünmüyor
1. **Config dosyası doğru mu kontrol et**
2. **Claude Desktop'u tamamen kapat ve aç**
3. **JSON syntax'ı doğru mu kontrol et**
4. **Server çalışıyor mu kontrol et**

### Connection Error
```bash
# Health check
curl https://your-app.coolify.io/health

# MCP discovery
curl https://your-app.coolify.io/.well-known/mcp
```

### Authentication Issues
1. **Token geçerli mi kontrol et**
2. **CORS ayarları doğru mu kontrol et**
3. **SSL sertifikası geçerli mi kontrol et**

## 📝 Available Tools

MCP client'ınızda şu tools görünecek:

### Turkish Legal Databases
- `search_bedesten_unified` - Multiple courts search
- `get_bedesten_document_markdown` - Document retrieval
- `search_emsal_detailed_decisions` - UYAP precedent search
- `search_uyusmazlik_decisions` - Jurisdictional disputes
- `search_anayasa_unified` - Constitutional Court
- `search_kik_decisions` - Public Procurement Authority
- `search_rekabet_kurumu_decisions` - Competition Authority
- `search_sayistay_unified` - Court of Accounts
- `search_kvkk_decisions` - Data Protection Authority
- `search_bddk_decisions` - Banking Regulation Authority

### Utility Tools
- `check_government_servers_health` - Health check
- `search` - ChatGPT Deep Research compatible
- `fetch` - ChatGPT Deep Research compatible

## 🎯 Usage Examples

### Example 1: Yargıtay Search
```
"Yargıtay'da mülkiyet hakkı ile ilgili 2024 yılından kararlar ara"
```

### Example 2: Constitutional Court
```
"Anayasa Mahkemesi'nde ifade özgürlüğü konusunda norm denetimi kararları"
```

### Example 3: Multiple Courts
```
"Bedesten API ile tüm mahkemelerde 'iş sözleşmesi' ara"
```

## 🔄 Updates

Server güncellemelerinde:
1. **Git pull** yap
2. **Coolify'da redeploy** et
3. **MCP client'ı yeniden başlat**

---

**✅ Setup tamamlandıktan sonra Türk hukuk veritabanlarına MCP ile erişebilirsiniz!**


