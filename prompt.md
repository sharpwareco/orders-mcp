Bu repo içinde eğitim amaçlı çok basit bir MCP demo projesi oluştur.

Amaç:
Claude Code / MCP entegrasyonunu göstermek için dış bir REST API’den sipariş verisi alan küçük bir MCP server istiyorum. MCP veriyi çeksin, Claude da bu veriyi kullanarak analiz/rapor üretebilsin.

Teknoloji:
- Node.js 22+
- TypeScript
- @modelcontextprotocol/sdk
- zod
- Mock REST API için json-server
- MCP transport olarak stdio

Repo yapısını mümkün olduğunca basit tut:

mcp-orders-demo/
  package.json
  tsconfig.json
  db.json
  src/
    index.ts
  README.md

Gereksinimler:

1. Mock REST API
- json-server kullan.
- Port: 3001
- db.json içinde en az 8 sipariş olsun.
- Alanlar:

  id
  customer
  amount
  currency
  status
  daysLate
  createdAt

- status değerleri en az:
  NEW
  PROCESSING
  SHIPPED
  DELAYED
  CANCELLED

- En az 3 DELAYED kayıt olsun.
- Tutarlar farklı olsun.
- Veri eğitim demosunda rahat okunabilir olsun.

2. MCP server
Server adı:

orders-demo

Şu tool'ları expose et:

list_orders

Parametreler:
- status?: string

Davranış:
- http://localhost:3001/orders adresinden veriyi çek.
- status verilmişse filtrele.
- Sonucu MCP text content olarak JSON döndür.

get_order

Parametre:
- id: string

Davranış:
- ilgili siparişi getir.
- bulunamazsa anlaşılır hata döndür.

get_order_summary

Parametre yok.

Davranış:
REST API’den siparişleri al ve şunları hesapla:
- totalOrders
- totalAmount
- delayedOrders
- delayedAmount
- cancelledOrders
- averageOrderAmount

MCP server sadece veri sağlayıcı olsun.
LLM reasoning yapmasın.
Risk yorumu, sınıflandırma veya doğal dil raporu MCP içinde yapılmasın.

3. Hata yönetimi
- json-server çalışmıyorsa anlaşılır hata mesajı üret.
- fetch başarısız olursa MCP server crash etmesin.
- HTTP status kontrolleri yap.

4. package.json scriptleri

Şunlar olsun:

npm run mock-api
npm run build
npm run mcp
npm run dev

Tercihen dev script MCP'yi tsx ile doğrudan çalıştırabilir.

5. Claude Code MCP config örneği

README içinde Claude Code'a nasıl bağlanacağını göster.

Örneğin buna benzer bir config ver:

{
  "mcpServers": {
    "orders-demo": {
      "command": "node",
      "args": ["/ABSOLUTE/PATH/mcp-orders-demo/dist/index.js"]
    }
  }
}

Config'in hangi dosyaya veya komuta göre eklenmesi gerektiğini güncel Claude Code yaklaşımına göre açıkla.

6. README demo akışı

README içinde kısa bir eğitim senaryosu yaz.

Terminal 1:

npm install
npm run mock-api

Terminal 2:

npm run build

Claude Code üzerinden örnek görev:

"orders-demo MCP'sini kullanarak tüm siparişleri getir.
Geciken siparişleri analiz et.
Toplam geciken sipariş tutarını hesapla.
En yüksek tutarlı 3 geciken siparişi listele.
Sonucu order-risk-report.md dosyasına Markdown olarak yaz."

Başka bir örnek:

"orders-demo MCP'sinden sipariş özetini al ve mevcut durumu yöneticiler için 5 maddede özetle."

7. Kod kalitesi
- Gereksiz framework kullanma.
- Abstraction katmanlarını şişirme.
- Eğitim projesi olduğu için kod mümkün olduğunca okunabilir olsun.
- Tek MCP server dosyası yeterliyse bölme.
- TypeScript strict açık olsun.
- any kullanmamaya çalış.
- Açıklayıcı ama kısa error mesajları kullan.

8. Son kontrol

Uygulamayı gerçekten çalıştır:
- npm install
- mock API'yi başlat
- build al
- mümkünse MCP server'ın API'ye eriştiğini doğrula

README'deki komutların gerçekten çalıştığından emin ol.

İş bittiğinde bana:
- oluşturulan dosyaları
- nasıl çalıştıracağımı
- Claude Code'a nasıl bağlayacağımı
- örnek MCP tool isimlerini
kısa biçimde özetle.

Önemli:
Bu bir production sistemi değil. Amaç 15-20 dakikalık MCP eğitimi demosu. Basitlik ve anlaşılabilirlik öncelikli.
