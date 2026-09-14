# mcp-orders-demo

MCP eğitimi için mini demo. json-server ile mock bir REST API (port 3001) ve bu API'den sipariş verisi çeken bir MCP server (stdio transport).

MCP server **sadece veri sağlar** (listele / getir / sayısal özet); analiz, risk yorumu ve raporlama Claude tarafında yapılır.

## Kurulum ve çalıştırma

Gereksinim: Node.js 22+

**Terminal 1 — mock API:**

```bash
npm install
npm run mock-api
```

**Terminal 2 — build (Claude Code'un bağlanacağı dosyayı üretir):**

```bash
npm run build
```

Hızlı kontrol:

```bash
curl http://localhost:3001/orders
```

## Claude Code'a bağlama

### Seçenek A — CLI komutu (önerilen)

```bash
claude mcp add orders-demo -- node <PATH_TO_DEMO>/mcp_server/mcp-orders-demo/dist/index.js
```

- `-s user`: makinedeki tüm projelerde kullanmak için (kullanıcı scope'u)
- `-s project`: proje kökündeki `.mcp.json` dosyasına yazar (ekiple paylaşılabilir)
- Kontrol: `claude mcp list` · Kaldırma: `claude mcp remove orders-demo`

### Seçenek B — `.mcp.json` dosyası

Proje köküne `.mcp.json` yazın (Claude Code başlarken proje scope'lu server'ları buradan okur):

```json
{
  "mcpServers": {
    "orders-demo": {
      "command": "node",
      "args": ["/Users/cemguler/dev_env/node_workspace/mcp_server/mcp-orders-demo/dist/index.js"]
    }
  }
}
```

Notlar:

- Yol **mutlak** olmalı.
- MCP server'ı kullanırken Terminal 1'deki mock API'nin çalışıyor olması gerekir.
- Build olmadan hızlı denemek: `npm run dev` (tsx ile çalışır). Claude Code'a bağlarken build edilmiş `dist/index.js` kullanın.

## MCP araçları

| Tool | Parametreler | Ne yapar |
|------|--------------|----------|
| `list_orders` | `status?: string` | Tüm siparişleri döner; verilirse status filtresi uygular |
| `get_order` | `id: string` | Tek siparişi getirir (bulunamazsa anlaşılır hata) |
| `get_order_summary` | — | `totalOrders`, `totalAmount`, `delayedOrders`, `delayedAmount`, `cancelledOrders`, `averageOrderAmount` |

## Demo senaryosu

1. Terminal 1'de mock API'yi çalıştırın, Terminal 2'de build alın.
2. Claude Code'u açın (`claude mcp add` kaydını yaptıktan sonra).
3. Şu görevleri deneyin:

> orders-demo MCP'sini kullanarak tüm siparişleri getir. Geciken siparişleri analiz et. Toplam geciken sipariş tutarını hesapla. En yüksek tutarlı 3 geciken siparişi listele. Sonucu order-risk-report.md dosyasına Markdown olarak yaz.

> orders-demo MCP'sinden sipariş özetini al ve mevcut durumu yöneticiler için 5 maddede özetle.

## Sorun giderme

- **"Mock API'ye bağlanılamadı"** → `npm run mock-api` çalışıyor mu? `curl http://localhost:3001/orders` ile deneyin.
- **Claude Code tool'ları görmüyor** → `claude mcp list` çıktısına ve `.mcp.json` içindeki mutlak yola bakın.
- **Build hatası** → Node 22+ kullandığınızdan emin olun (`node --version`).
