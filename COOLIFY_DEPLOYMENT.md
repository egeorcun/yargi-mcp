# Yargi MCP Server - Coolify Deployment Rehberi

Bu rehber, Yargi MCP Server'ınızı Coolify platformunda Docker ile nasıl deploy edeceğinizi adım adım açıklar.

## 📋 Ön Gereksinimler

- Coolify hesabı ve kurulumu
- GitHub/GitLab repository'si
- Domain name (opsiyonel)

## 🚀 Coolify'da Deployment

### 1. Yeni Uygulama Oluşturma

1. Coolify dashboard'una giriş yapın
2. **"New Application"** butonuna tıklayın
3. **"Docker Compose"** seçeneğini seçin
4. Repository bilgilerinizi girin:
   - **Repository URL**: `https://github.com/your-username/yargi-mcp.git`
   - **Branch**: `main`
   - **Build Pack**: `Docker Compose`

### 2. Docker Compose Dosyası Seçimi

1. **"Docker Compose File"** alanında: `docker-compose.coolify.yml`
2. **"Service Name"**: `yargi-mcp`

### 3. Environment Variables Ayarlama

Coolify'da **Environment** sekmesine gidin ve aşağıdaki değişkenleri ekleyin:

#### 🔧 Temel Ayarlar
```bash
# Uygulama Ayarları
HOST=0.0.0.0
PORT=8000
LOG_LEVEL=info
PYTHONUNBUFFERED=1

# Domain ve CORS (Subdomain Önerilen)
BASE_URL=https://api.yargimcp.com  # Kendi subdomain'inizi yazın
ALLOWED_ORIGINS=https://yargimcp.com,https://api.yargimcp.com

# Alternatif: Doğrudan Coolify domain (geçici test için)
# BASE_URL=https://your-app.coolify.io
# ALLOWED_ORIGINS=*
```

#### 🔐 Authentication (Opsiyonel)
OAuth authentication kullanmak istiyorsanız:

```bash
# Authentication
ENABLE_AUTH=true
CLERK_SECRET_KEY=sk_test_xxxxx  # Clerk.dev'den alın
CLERK_PUBLISHABLE_KEY=pk_test_xxxxx
CLERK_ISSUER=https://your-app.clerk.accounts.dev
```

#### 🛡️ Security (Opsiyonel)
```bash
# API güvenliği için
API_TOKEN=your-secure-random-token-here
```

### 4. Port Ayarları

1. **"Ports"** sekmesine gidin
2. **Port Mapping**: `8000:8000`
3. **Public Port**: `80` (HTTP) veya `443` (HTTPS)

### 5. Domain Ayarlama

#### 🎯 Önerilen: Subdomain Kullanımı

**Neden Subdomain?**
- ✅ Profesyonel görünüm (`api.domain.com`)
- ✅ Frontend/API ayrımı
- ✅ Kolay SSL yönetimi
- ✅ Ölçeklenebilir yapı

**Setup:**
1. **DNS Provider'ınızda** (Cloudflare, GoDaddy, vs):
   ```
   Type: A Record
   Name: api (veya mcp)
   Value: [Coolify Server IP]
   ```

2. **Coolify'da**:
   - **"Domains"** sekmesine gidin
   - **Add Custom Domain**: `api.yourdomain.com`
   - **SSL**: Otomatik Let's Encrypt aktif

3. **Environment'da**:
   ```bash
   BASE_URL=https://api.yourdomain.com
   ALLOWED_ORIGINS=https://yourdomain.com,https://api.yourdomain.com
   ```

#### 🔄 Alternatif: Coolify Domain (Test için)
Hızlı test için doğrudan Coolify domain'i:
- Domain: `your-app.coolify.io`
- SSL otomatik
- Production için önerilmez

### 6. Resource Limits

**"Resources"** sekmesinde:
- **Memory Limit**: `1GB`
- **CPU Limit**: `0.5`
- **Memory Reservation**: `512MB`

### 7. Deployment

1. **"Deploy"** butonuna tıklayın
2. Build loglarını takip edin
3. Deployment tamamlandığında uygulama kullanıma hazır!

## 🔍 Deployment Doğrulama

### Otomatik Test Scripti (Önerilen)
```bash
# Repository'de bulunan test scriptini kullanın
chmod +x test_deployment.sh
./test_deployment.sh https://your-app.coolify.io
```

Bu script otomatik olarak tüm kritik endpoint'leri test eder ve deployment durumunu rapor eder.

### Manuel Test

Deployment başarılı olduktan sonra şu URL'leri test edin:

#### Health Check
```bash
curl https://your-app.coolify.io/health
```

**Beklenen yanıt:**
```json
{
  "status": "healthy",
  "service": "Yargı MCP Server",
  "version": "0.1.0",
  "tools_count": 15,
  "auth_enabled": false
}
```

#### MCP Endpoint
```bash
curl https://your-app.coolify.io/mcp/
```

#### API Documentation
Browser'da: `https://your-app.coolify.io/`

#### Discovery Endpoints
```bash
# MCP Discovery
curl https://your-app.coolify.io/.well-known/mcp

# OAuth Metadata
curl https://your-app.coolify.io/.well-known/oauth-authorization-server
```

## 🛠️ Troubleshooting

### ❌ Port Already Allocated Hatası
**Hata**: `Bind for 0.0.0.0:8000 failed: port is already allocated`

**Çözüm**: 
1. `docker-compose.coolify.yml` dosyasında `ports` mapping'i kaldırın
2. Sadece `expose: ["8000"]` kullanın
3. Coolify otomatik port routing yapar

```yaml
# ❌ Yanlış
ports:
  - "8000:8000"

# ✅ Doğru (Coolify için)
expose:
  - "8000"
```

### Container Başlamıyor
1. **Logs** sekmesinde hataları kontrol edin
2. Port çakışması var mı kontrol edin (yukarıya bakın)
3. Environment variable'ları doğru mu kontrol edin
4. Docker build hataları var mı kontrol edin

### Memory Issues
```bash
# Resource limits'i artırın:
Memory: 1.5GB
CPU: 0.75
```

### SSL Problemi
1. Domain doğru ayarlanmış mı kontrol edin
2. DNS ayarları doğru mu kontrol edin
3. Coolify otomatik SSL'i yeniden denesin

### Database Connection (Gelecek)
Redis veya PostgreSQL eklemek için:
1. Coolify'da yeni **Database Service** oluşturun
2. Connection string'i environment variable olarak ekleyin

## 🔄 Güncelleme

Kod değişikliklerinden sonra:

1. GitHub/GitLab'a push yapın
2. Coolify'da **"Redeploy"** butonuna tıklayın
3. Veya Webhook ile otomatik deployment ayarlayın

## 📊 Monitoring

Coolify dashboard'unda:
- **CPU/Memory kullanımı**
- **Network trafiği**
- **Application logs**
- **Health check durumu**

## 🔐 Güvenlik Önerileri

1. **Environment variables'ı secrets olarak saklayın**
2. **API_TOKEN kullanın**
3. **CORS ayarlarını production'da kısıtlayın**
4. **SSL/HTTPS kullanın**
5. **Regular backups alın**

## 🆘 Destek

Sorun yaşarsanız:
1. Coolify logs'ları kontrol edin
2. GitHub Issues'da sorun bildirin
3. Coolify community'sine danışın

## 📝 Notlar

- Bu proje Playwright kullanır, ilk build biraz uzun sürebilir
- Memory kullanımı ~500MB civarında
- Health check 30 saniye aralıklarla çalışır
- Loglar `/app/logs` dizininde saklanır

---

**✅ Başarılı deployment sonrası uygulamanız hazır!**

Turkish legal databases'lere MCP protokolü ile erişim sağlayabilirsiniz.
