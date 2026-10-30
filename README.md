# askitom
askitom- ozəl app 
from pathlib import Path
import zipfile, textwrap, json

root = Path("/mnt/data/askitom_starter")
root.mkdir(exist_ok=True)

html = r'''<!doctype html>
<html lang="az">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
<meta name="theme-color" content="#8f3d58">
<link rel="manifest" href="manifest.webmanifest">
<title>Aşkitom</title>
<style>
:root{--rose:#8f3d58;--pink:#f4dfe5;--cream:#fffaf4;--ink:#482936;--gold:#c49a55}
*{box-sizing:border-box} body{margin:0;background:linear-gradient(160deg,#fffaf4,#f8e7e9);font-family:Arial,sans-serif;color:var(--ink);min-height:100vh}
header{padding:22px 20px 10px;text-align:center} h1{margin:0;color:var(--rose);font-size:29px} .sub{font-size:13px;color:#9b7180;margin-top:5px}
main{max-width:520px;margin:auto;padding:12px 16px 32px}.quote{background:#fff;border:1px solid #f0d9df;border-radius:22px;padding:18px;text-align:center;box-shadow:0 8px 22px #75435412;margin:10px 0 20px}.quote small{color:var(--gold);font-weight:bold;letter-spacing:.5px}.quote p{font-size:18px;line-height:1.45;margin:10px 0 0;font-weight:600}
.grid{display:grid;grid-template-columns:1fr 1fr;gap:13px}.tile{border:0;border-radius:23px;min-height:150px;padding:18px 14px;text-align:left;background:#fff;box-shadow:0 8px 22px #75435414;color:var(--ink);cursor:pointer}.tile .emoji{font-size:42px;display:block;margin-bottom:15px}.tile strong{font-size:18px;color:var(--rose)}.tile span:last-child{display:block;color:#9b7180;font-size:12px;margin-top:5px}
.panel{display:none;background:#fff;border-radius:24px;padding:18px;box-shadow:0 8px 22px #75435414}.panel.active{display:block}.back{border:0;background:#f7e8ec;color:var(--rose);border-radius:13px;padding:10px 14px;font-size:15px;margin-bottom:14px}.panel h2{margin:4px 0 14px;color:var(--rose)}.dish{background:#fff5f0;border-radius:20px;padding:24px 16px;text-align:center;margin:10px 0 16px}.dish .foodemoji{font-size:66px}.dish h3{font-size:24px;margin:12px 0 4px}.muted{color:#987985;font-size:13px}
button.primary{border:0;background:var(--rose);color:#fff;border-radius:16px;padding:15px 18px;font-weight:bold;font-size:15px;width:100%;margin:5px 0;cursor:pointer}button.secondary{border:1px solid #e8cbd4;background:#fff;color:var(--rose);border-radius:16px;padding:13px 18px;font-weight:bold;font-size:14px;width:100%;margin:5px 0;cursor:pointer}
.info{background:#fff7f8;border-radius:16px;padding:14px;margin:10px 0;color:#77515f;font-size:14px;line-height:1.5}.link{display:block;text-decoration:none;text-align:center;background:var(--rose);color:#fff;padding:13px;border-radius:15px;margin:9px 0;font-weight:bold}
.products{display:grid;grid-template-columns:1fr 1fr;gap:10px}.product{background:#fff7f8;border-radius:18px;padding:15px;text-align:center}.product .emoji{font-size:38px}.product b{display:block;margin:8px 0;color:var(--rose)}.product a{font-size:12px;color:var(--rose)}
footer{text-align:center;color:#b08b98;font-size:11px;padding:22px}
</style>
</head>
<body>
<header><h1>♡ Aşkitom</h1><div class="sub">Sənin üçün kiçik bir sevgi dünyası</div></header>
<main>
<section id="home">
 <div class="quote"><small>♡ GÜNÜN ÜRƏK SÖZÜ ♡</small><p id="quote">Sabahın xeyir, gözəlim. Günün ürəyin qədər gözəl olsun.</p></div>
 <div class="grid">
  <button class="tile" onclick="show('food')"><span class="emoji">🍲</span><strong>Yemək</strong><span>Bu gün nə bişirək?</span></button>
  <button class="tile" onclick="show('serial')"><span class="emoji">🎬</span><strong>Serial</strong><span>Sevdiyin seriallar</span></button>
  <button class="tile" onclick="show('fashion')"><span class="emoji">👜</span><strong>Moda</strong><span>Bu fəslin trendləri</span></button>
  <button class="tile" onclick="show('kids')"><span class="emoji">🧸</span><strong>Uşaq</strong><span>1–2 yaş üçün</span></button>
 </div>
</section>
<section id="food" class="panel">
 <button class="back" onclick="show('home')">← Geri</button><h2>Bu gün nə bişirək?</h2>
 <div class="dish"><div class="foodemoji">🍽️</div><div class="muted">Bu günün seçimi</div><h3 id="dish">Düyməyə bas</h3><div class="muted">Siyahıdan təsadüfi seçim</div></div>
 <button class="primary" onclick="pickDish()">Bu gün nə bişirək?</button><button class="secondary" onclick="pickDish()">Başqa təklif</button>
 <div class="muted">Yemək şəkillərinin internetdən avtomatik tapılması növbəti mərhələdə qoşulacaq.</div>
</section>
<section id="serial" class="panel">
 <button class="back" onclick="show('home')">← Geri</button><h2>Seriallarım</h2>
 <div class="info"><b>Yeraltı</b><p>Yeni bölüm və fraqman üçün rəsmi YouTube kanalına keçid.</p><a class="link" target="_blank" rel="noopener" href="https://www.youtube.com/results?search_query=Yeralt%C4%B1+resmi+kanal+son+b%C3%B6l%C3%BCm">YouTube-da axtar</a></div>
 <div class="info"><b>Sevdiyim sənsin</b><p>Yeni bölüm və fraqman üçün YouTube keçidi.</p><a class="link" target="_blank" rel="noopener" href="https://www.youtube.com/results?search_query=Sevdiyim+s%C9%99nsin+resmi+kanal+son+b%C3%B6l%C3%BCm">YouTube-da axtar</a></div>
 <div class="muted">Rəsmi kanalların dəqiq identifikatorları tapıldıqdan sonra yeni bölümlər avtomatik göstəriləcək.</div>
</section>
<section id="fashion" class="panel">
 <button class="back" onclick="show('home')">← Geri</button><h2>Bu fəslin trendləri</h2>
 <div class="products">
  <div class="product"><div class="emoji">🧥</div><b>Qadın kurtkası</b><a target="_blank" rel="noopener" href="https://www.temu.com/search_result.html?search_key=women+jacket">Temu-da oxşarını tap</a></div>
  <div class="product"><div class="emoji">👜</div><b>Qadın çantası</b><a target="_blank" rel="noopener" href="https://www.temu.com/search_result.html?search_key=women+handbag">Temu-da oxşarını tap</a></div>
  <div class="product"><div class="emoji">👟</div><b>Qadın ayaqqabısı</b><a target="_blank" rel="noopener" href="https://www.temu.com/search_result.html?search_key=women+shoes">Temu-da oxşarını tap</a></div>
  <div class="product"><div class="emoji">🧣</div><b>Mövsüm aksesuarı</b><a target="_blank" rel="noopener" href="https://www.temu.com/search_result.html?search_key=women+accessories">Temu-da oxşarını tap</a></div>
 </div>
 <div class="muted" style="margin-top:12px">Bunlar ilkin kateqoriya keçidləridir, real məhsul kartları və qiymətlər hələ qoşulmayıb.</div>
</section>
<section id="kids" class="panel">
 <button class="back" onclick="show('home')">← Geri</button><h2>1–2 yaş üçün</h2>
 <div class="products">
  <div class="product"><div class="emoji">👶</div><b>Uşaq geyimi</b><a target="_blank" rel="noopener" href="https://www.temu.com/search_result.html?search_key=toddler+clothes">Temu-da oxşarını tap</a></div>
  <div class="product"><div class="emoji">👟</div><b>Balaca ayaqqabı</b><a target="_blank" rel="noopener" href="https://www.temu.com/search_result.html?search_key=toddler+shoes">Temu-da oxşarını tap</a></div>
  <div class="product"><div class="emoji">🧢</div><b>Papaq</b><a target="_blank" rel="noopener" href="https://www.temu.com/search_result.html?search_key=toddler+hat">Temu-da oxşarını tap</a></div>
  <div class="product"><div class="emoji">🛒</div><b>Kalyaska aksesuarı</b><a target="_blank" rel="noopener" href="https://www.temu.com/search_result.html?search_key=stroller+accessories">Temu-da oxşarını tap</a></div>
 </div>
</section>
<footer>Sevgi ilə hazırlanır ♡ Aşkitom</footer>
</main>
<script>
const dishes = [
"Dolma","Küftə","Aovuz","Kartof-ət qızartması","Badımcan qızartması","Boranı","Lobya",
"Qreçka ətlə","Qreçka yumurta ilə","Plov","Yumurta-pomidor","Kükü","Püre","Kotlet-vermişel",
"Kotlet-kartof","Kotlet-qreçka","Düyü şorbası","Mərci şorbası","Vermişel şorbası",
"Makaron ət","Makaron kotlet","Üç bacı dolması","Əriştə şorbası","Borş"
];
const quotes = [
"Sabahın xeyir, gözəlim. Günün ürəyin qədər gözəl olsun.",
"Sən həmişəki kimi göz qamaşdırırsan.",
"Yaxşı ki, varsan, yaxşı ki, həyatımdasan.",
"Sənin gülüşün günümün ən gözəl anıdır.",
"Bu gün də özünə yaxşı bax, mənim gözəlim.",
"Sənə baxmaqdan doymuram. ❤️",
"Ümid edirəm, bu gün üzündən təbəssüm əskik olmaz.",
"Bir fincan çay, bir az rahatlıq və bol-bol gülüş arzulayıram sənə."
];
function show(id){document.getElementById('home').style.display=id==='home'?'block':'none';document.querySelectorAll('.panel').forEach(p=>p.classList.toggle('active',p.id===id));}
function pickDish(){document.getElementById('dish').textContent=dishes[Math.floor(Math.random()*dishes.length)];}
const day=Math.floor(Date.now()/86400000);document.getElementById('quote').textContent=quotes[day%quotes.length];
if('serviceWorker' in navigator) window.addEventListener('load',()=>navigator.serviceWorker.register('./sw.js').catch(()=>{}));
</script>
</body></html>'''
(root/"index.html").write_text(html, encoding="utf-8")
(root/"manifest.webmanifest").write_text(json.dumps({
 "name":"Aşkitom","short_name":"Aşkitom","start_url":"./","display":"standalone",
 "background_color":"#fffaf4","theme_color":"#8f3d58",
 "icons":[{"src":"icon.svg","sizes":"any","type":"image/svg+xml","purpose":"any maskable"}]
}, ensure_ascii=False, indent=2), encoding="utf-8")
(root/"icon.svg").write_text('''<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512"><rect width="512" height="512" rx="120" fill="#f4dfe5"/><circle cx="256" cy="256" r="174" fill="#fffaf4" stroke="#c49a55" stroke-width="12"/><path d="M256 365S105 274 105 183c0-74 94-105 151-35 57-70 151-39 151 35 0 91-151 182-151 182z" fill="#8f3d58"/></svg>''', encoding="utf-8")
(root/"sw.js").write_text("self.addEventListener('install',e=>self.skipWaiting()); self.addEventListener('fetch',e=>{});", encoding="utf-8")
(root/"README.txt").write_text("""AŞKİTOM – ilkin prototip

Bu paket Android telefonda quraşdırıla bilən PWA prototipinin fayllarıdır.
Quraşdırmaq üçün faylları HTTPS dəstəkləyən hostinqə yerləşdirmək lazımdır.
Sonra Honor X9b-də Chrome ilə ünvanı açıb menyudan "Add to Home screen" / "Install app" seçilir.

Hazırda işləyən:
- 4 bölmə arasında keçid
- 24 ayrı yeməkdən təsadüfi seçim
- Gündəlik romantik cümlə (tarix və il göstərilmir)
- Seriallar üçün YouTube axtarış keçidləri
- Temu kateqoriya axtarış keçidləri

Hələ qoşulmayan:
- YouTube API ilə rəsmi kanalların avtomatik izlənməsi və yeni bölüm bildirişləri
- Məhsul kataloqunun avtomatik mənbədən gətirilməsi, real şəkil və qiymətlər
- Yeməklərin internet şəkilləri

Qeyd: ilkin siyahıdakı "Aovuz" yazılışı istifadəçinin mətnindəki kimi saxlanılıb; istəsə sonra düzəldilə bilər.
""", encoding="utf-8")
zip_path=Path("/mnt/data/Askitom_ilkin_prototip.zip")
with zipfile.ZipFile(zip_path,"w",zipfile.ZIP_DEFLATED) as z:
    for p in root.iterdir(): z.write(p, arcname=p.name)
print(zip_path, zip_path.stat().st_size)
