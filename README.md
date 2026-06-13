<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>駅スナップ — 東京の駅別 撮影ガイド</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Shippori+Mincho:wght@400;600;800&family=Noto+Sans+JP:wght@300;400;500;700&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.min.css"/>
<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.min.js"></script>
<style>
  :root {
    --ink:#0D0D0D; --paper:#F2F0EB; --card:#1A1A1A; --card2:#222; --gold:#C8A96E;
    --gold-dim:#8a7048; --sage:#7A8C7E; --muted:#666; --border:#2a2a2a;
    --text:#E8E4DC; --text-dim:#999;
  }
  *, *::before, *::after { box-sizing:border-box; margin:0; padding:0; }
  html, body { height:100%; overflow:hidden; }
  body { background:var(--ink); color:var(--text); font-family:'Noto Sans JP',sans-serif; font-weight:300; display:flex; flex-direction:column; }

  /* HEADER */
  header { background:#111; border-bottom:1px solid var(--border); flex-shrink:0; }
  .film-strip { display:flex; align-items:stretch; height:54px; }
  .film-holes { display:flex; flex-direction:column; justify-content:space-around; padding:5px 8px; flex-shrink:0; }
  .film-holes span { display:block; width:11px; height:8px; border-radius:2px; background:var(--ink); border:1px solid #252525; }
  .film-title { display:flex; align-items:center; gap:14px; padding:0 18px; flex:1; }
  .film-title h1 { font-family:'Shippori Mincho',serif; font-weight:800; font-size:1.05rem; letter-spacing:0.08em; color:var(--paper); white-space:nowrap; }
  .film-title h1 em { font-style:normal; color:var(--gold); }
  .film-sub { font-family:'DM Mono',monospace; font-size:0.56rem; color:var(--muted); letter-spacing:0.12em; text-transform:uppercase; white-space:nowrap; }
  .exposure-badge { margin-left:auto; font-family:'DM Mono',monospace; font-size:0.54rem; color:var(--gold); letter-spacing:0.12em; border:1px solid var(--gold-dim); padding:3px 9px; border-radius:2px; white-space:nowrap; flex-shrink:0; margin-right:12px; }

  /* FILTER */
  .filter-area { background:#141414; border-bottom:1px solid var(--border); flex-shrink:0; }
  .filter-row { padding:5px 14px; display:flex; align-items:center; gap:7px; flex-wrap:wrap; min-height:34px; }
  .filter-label { font-family:'DM Mono',monospace; font-size:0.52rem; text-transform:uppercase; letter-spacing:0.12em; color:var(--muted); flex-shrink:0; }
  .filter-group { display:flex; gap:4px; flex-wrap:wrap; }
  .filter-btn { background:transparent; border:1px solid var(--border); color:var(--text-dim); font-family:'Noto Sans JP',sans-serif; font-size:0.62rem; padding:3px 10px; border-radius:20px; cursor:pointer; transition:all 0.13s; white-space:nowrap; }
  .filter-btn:hover { border-color:var(--gold-dim); color:var(--paper); }
  .filter-btn.active { background:var(--gold); border-color:var(--gold); color:var(--ink); font-weight:500; }
  .sep { color:var(--border); font-size:1rem; flex-shrink:0; }
  .count-badge { margin-left:auto; font-family:'DM Mono',monospace; font-size:0.58rem; color:var(--sage); letter-spacing:0.08em; flex-shrink:0; }

  /* MAIN */
  .main { display:grid; grid-template-columns:430px 1fr; flex:1; min-height:0; }

  /* LIST PANEL */
  .list-panel { overflow-y:auto; border-right:1px solid var(--border); background:#0e0e0e; }
  .list-panel::-webkit-scrollbar { width:3px; }
  .list-panel::-webkit-scrollbar-thumb { background:var(--border); border-radius:2px; }

  .station-card { border-bottom:1px solid var(--border); cursor:pointer; transition:background 0.13s; }
  .station-card:hover { background:var(--card); }
  .station-card.selected { background:var(--card2); border-left:2px solid var(--gold); }

  .station-head { padding:13px 15px 9px; }
  .station-top { display:flex; align-items:baseline; gap:8px; margin-bottom:5px; }
  .station-name { font-family:'Shippori Mincho',serif; font-size:1rem; font-weight:700; color:var(--paper); flex:1; }
  .station-ward { font-family:'DM Mono',monospace; font-size:0.5rem; letter-spacing:0.06em; border:1px solid var(--sage); color:var(--sage); padding:2px 5px; border-radius:2px; white-space:nowrap; }

  /* META INFO ROW */
  .station-meta {
    display:flex; gap:10px; flex-wrap:wrap; align-items:center;
    margin-bottom:7px; padding:5px 8px;
    background:#111; border-radius:4px; border:1px solid #1e1e1e;
  }
  .meta-item { display:flex; align-items:center; gap:3px; font-family:'DM Mono',monospace; font-size:0.56rem; color:var(--text-dim); }
  .meta-icon { font-size:0.7rem; }
  .meta-item b { color:var(--text); font-weight:400; }

  .station-intro { font-size:0.72rem; color:var(--text-dim); line-height:1.75; margin-bottom:8px; }
  .station-genres { display:flex; gap:4px; flex-wrap:wrap; align-items:center; }
  .genre-tag { font-size:0.56rem; padding:2px 7px; border-radius:12px; }
  .genre-tag.street    { background:#1e2a3a; color:#7aafd4; }
  .genre-tag.nature    { background:#1a2a1e; color:#7ac487; }
  .genre-tag.night     { background:#22182a; color:#b07ad4; }
  .genre-tag.retro     { background:#2a2018; color:#d4a87a; }
  .genre-tag.modern    { background:#1e2530; color:#7ac4d4; }
  .genre-tag.shitamachi{ background:#2a1a1a; color:#d47a7a; }
  .genre-tag.art       { background:#1a2226; color:#7ad4c4; }
  .article-count { font-family:'DM Mono',monospace; font-size:0.54rem; color:var(--gold-dim); margin-left:auto; }

  /* ARTICLES */
  .articles { padding:0 15px 13px; display:none; }
  .station-card.selected .articles { display:block; }
  .articles-label {
    font-family:'DM Mono',monospace; font-size:0.5rem; text-transform:uppercase;
    letter-spacing:0.14em; color:var(--muted); margin:3px 0 7px;
    display:flex; align-items:center; gap:6px;
  }
  .articles-label::before, .articles-label::after { content:''; flex:1; height:1px; background:var(--border); }

  .article-card { display:block; text-decoration:none; background:#171717; border:1px solid var(--border); border-radius:5px; padding:9px 11px; margin-bottom:6px; transition:all 0.13s; }
  .article-card:hover { border-color:var(--gold-dim); background:#1d1d1d; transform:translateX(2px); }
  .article-title { font-size:0.76rem; color:var(--paper); line-height:1.5; margin-bottom:3px; font-weight:400; }
  .article-card:hover .article-title { color:var(--gold); }
  .article-meta { display:flex; align-items:center; gap:5px; }
  .article-site { font-family:'DM Mono',monospace; font-size:0.54rem; color:var(--sage); }
  .article-arrow { margin-left:auto; color:var(--muted); font-size:0.68rem; }
  .article-card:hover .article-arrow { color:var(--gold); }
  .article-note { font-size:0.64rem; color:var(--text-dim); line-height:1.55; margin-top:3px; }

  .disclaimer { padding:10px 15px; font-size:0.58rem; color:var(--muted); line-height:1.6; border-top:1px dashed var(--border); background:#0b0b0b; }

  /* MAP */
  #map { width:100%; height:100%; }
  .leaflet-container { background:#0d0f14 !important; }
  .leaflet-control-zoom a { background:#1a1a1a !important; color:var(--text) !important; border-color:var(--border) !important; font-size:14px !important; }
  .leaflet-control-zoom a:hover { background:#252525 !important; color:var(--gold) !important; }
  .leaflet-control-attribution { background:rgba(8,8,8,0.75) !important; color:#555 !important; font-size:0.5rem !important; }
  .leaflet-control-attribution a { color:var(--gold-dim) !important; }
  .leaflet-popup-content-wrapper { background:#141414 !important; border:1px solid var(--gold-dim) !important; border-radius:6px !important; box-shadow:0 8px 30px rgba(0,0,0,0.8) !important; color:var(--text) !important; padding:0 !important; }
  .leaflet-popup-content { margin:0 !important; }
  .leaflet-popup-tip-container { display:none; }
  .leaflet-popup-close-button { color:var(--muted) !important; top:7px !important; right:9px !important; font-size:16px !important; }
  .popup-body { padding:11px 14px 12px; min-width:210px; }
  .popup-name { font-family:'Shippori Mincho',serif; font-size:0.92rem; color:var(--paper); margin-bottom:2px; padding-right:18px; }
  .popup-ward { font-family:'DM Mono',monospace; font-size:0.52rem; color:var(--gold); letter-spacing:0.08em; margin-bottom:5px; }
  .popup-meta { display:flex; gap:8px; flex-wrap:wrap; margin-bottom:6px; }
  .popup-meta-item { font-family:'DM Mono',monospace; font-size:0.52rem; color:var(--text-dim); }
  .popup-intro { font-size:0.66rem; color:var(--text-dim); line-height:1.6; margin-bottom:7px; }
  .popup-cta { display:inline-block; font-family:'DM Mono',monospace; font-size:0.56rem; color:var(--gold); border:1px solid var(--gold-dim); padding:3px 9px; border-radius:3px; cursor:pointer; }
</style>
</head>
<body>

<header>
  <div class="film-strip">
    <div class="film-holes"><span></span><span></span><span></span></div>
    <div class="film-title">
      <h1>駅スナップ <em>東京</em></h1>
      <span class="film-sub">Station Snap Guide · Tokyo · 17 Stations</span>
    </div>
    <div class="exposure-badge">作例リンク付き · 17駅 · 40記事</div>
    <div class="film-holes"><span></span><span></span><span></span></div>
  </div>
</header>

<div class="filter-area">
  <div class="filter-row">
    <span class="filter-label">ジャンル</span>
    <div class="filter-group" id="genre-filters">
      <button class="filter-btn active" data-genre="all">すべて</button>
      <button class="filter-btn" data-genre="street">ストリート</button>
      <button class="filter-btn" data-genre="night">夜景</button>
      <button class="filter-btn" data-genre="modern">モダン</button>
      <button class="filter-btn" data-genre="retro">レトロ</button>
      <button class="filter-btn" data-genre="shitamachi">下町</button>
      <button class="filter-btn" data-genre="nature">自然</button>
      <button class="filter-btn" data-genre="art">アート</button>
    </div>
    <span class="sep">·</span>
    <span class="filter-label">時間帯</span>
    <div class="filter-group" id="time-filters">
      <button class="filter-btn active" data-time="all">すべて</button>
      <button class="filter-btn" data-time="早朝">早朝</button>
      <button class="filter-btn" data-time="昼">昼</button>
      <button class="filter-btn" data-time="夕方">夕方</button>
      <button class="filter-btn" data-time="夜">夜</button>
    </div>
    <span class="count-badge" id="count-badge"></span>
  </div>
</div>

<div class="main">
  <div class="list-panel" id="list-panel"></div>
  <div id="map"></div>
</div>

<script>
// walk: 駅から主要スポットまでの徒歩目安
// bestTime: おすすめ時間帯（フィルター対象）
// bestTimeLabel: 表示用ラベル
// difficulty: 撮影難易度 (★〜★★★)
const STATIONS = [
  {
    id:"roppongi", name:"六本木駅", ward:"港区", lat:35.6628, lng:139.7314,
    walk:"徒歩3〜10分", bestTime:"夜", bestTimeLabel:"夜・夕方がおすすめ", difficulty:"★★",
    intro:"都会的な夜景と現代建築の宝庫。国立新美術館・ヒルズ・東京ミッドタウンを周回すると超広角〜望遠まで使い切れる。",
    genres:["night","modern"],
    articles:[
      { title:"スナップで街めぐり。Vol.16｜六本木", site:"ShaSha / カメラのキタムラ", url:"https://www.kitamura.jp/shasha/sony/snapshot-of-walking-around-town-16-20221208/", note:"プロ写真家による撮影設定つき定番記事。超広角推奨。" },
      { title:"六本木周辺の撮影スポット8選", site:"note (Nocchi)", url:"https://note.com/nocchi_24/n/n1791452dccf8", note:"東京タワー・毛利庭園など8スポットを作例で解説。" },
      { title:"みんなが撮影した六本木の写真（2500作品以上）", site:"GANREF", url:"https://ganref.jp/spot/photo/jpn/lp/roppongi.html", note:"撮影データ付き投稿作品を大量閲覧できる。" },
      { title:"浜松町〜六本木スナップ散歩コース（作例付き）", site:"note (ficklog)", url:"https://note.com/ficklog/n/n632f63ba55d0", note:"東京タワー経由〜六本木まで実際のルートと作例。" },
    ],
  },
  {
    id:"shinjuku", name:"新宿駅", ward:"新宿区", lat:35.6896, lng:139.7006,
    walk:"徒歩1〜15分", bestTime:"夜", bestTimeLabel:"夜・早朝がおすすめ", difficulty:"★",
    intro:"高層ビルの光と影、思い出横丁のレトロ、歌舞伎町のネオンまで一駅で完結。情報量が多く「撮れば画になる」街。",
    genres:["street","modern","night"],
    articles:[
      { title:"スナップで街めぐり。Vol.1｜新宿", site:"ShaSha / カメラのキタムラ", url:"https://www.kitamura.jp/shasha/article/483884728-20211019/", note:"三角ビル・都庁・大ガードの作例とコツを解説。" },
      { title:"新宿のおすすめ撮影スポット12選", site:"PhotoDou", url:"https://photodou.com/spot-shinjuku", note:"駅前からペンギン広場まで12スポットを網羅。" },
      { title:"新宿の撮影スポット 街撮りスポット完全版", site:"Nippon Photo Net", url:"https://nipponphoto.net/tokyo-shinjuku-spot/", note:"夜スナップ・思い出横丁・バスタ新宿の作例。" },
    ],
  },
  {
    id:"shibuya", name:"渋谷駅", ward:"渋谷区", lat:35.6580, lng:139.7016,
    walk:"徒歩1〜10分", bestTime:"夜", bestTimeLabel:"夜・雨の日がおすすめ", difficulty:"★",
    intro:"スクランブル交差点は見下ろし構図が定番だが、地面レベルや雨の路面反射で別次元の作品になる。再開発で光がどんどん増えている。",
    genres:["street","night","modern"],
    articles:[
      { title:"スナップで街めぐり。Vol.2｜渋谷", site:"ShaSha / カメラのキタムラ", url:"https://www.kitamura.jp/shasha/article/485268415-20220121/", note:"ステンレス反射など「写り込み」活用テクニック。" },
      { title:"渋谷のストリートスナップおすすめスポット（作例付き）", site:"note (ficklog)", url:"https://note.com/ficklog/n/n528d0fe6bb9e", note:"宮下公園高架下・スクスク間の道など時間帯別作例。" },
      { title:"渋谷周辺の写真スポット14選", site:"note (Nocchi)", url:"https://note.com/nocchi_24/n/n76b757a918de", note:"定番から穴場まで14スポットを撮影テクニック付きで。" },
      { title:"原宿〜渋谷フォトウォーク｜キャットストリートを歩く", site:"Frame by Frame", url:"https://framebyframe.tokyo/harajuku-shibuya-photowalk/834/", note:"夜18〜20時推奨。キャットストリートの光源解説。" },
    ],
  },
  {
    id:"asakusa", name:"浅草駅", ward:"台東区", lat:35.7119, lng:139.7980,
    walk:"徒歩2〜8分", bestTime:"早朝", bestTimeLabel:"早朝・夕方がおすすめ", difficulty:"★",
    intro:"雷門・仲見世・ホッピー通り。早朝は観光客が少なく石畳が静かで最高。夜は提灯のオレンジ光が温かみのある作品になる。",
    genres:["shitamachi","retro"],
    articles:[
      { title:"着物×フィルムで巡る！浅草レトロスポット10選", site:"NICO STOP / ニコン", url:"https://nij.nikon.com/nicostop/entry/spot/asakusa/2022/11/15/1", note:"傘越しの光やボケ活用など構図解説が丁寧。" },
      { title:"写真を撮りながら浅草を巡る旅へ", site:"MATCHA", url:"https://matcha-jp.com/jp/12237", note:"仲見世〜五重塔を巡る撮影コース紹介。" },
      { title:"みんなが撮影した浅草の写真（4000作品以上）", site:"GANREF", url:"https://ganref.jp/spot/photo/jpn/lp/asakusa.html", note:"吾妻橋からのスカイツリーなど投稿作品多数。" },
      { title:"浅草の街をモノクロでスナップ", site:"タカフォトズログ", url:"https://takaphotoslog.com/photograph/monochrome-photography-of-asakusa-in-2019", note:"人力車や路面電車をモノクロで切り取る作例。" },
    ],
  },
  {
    id:"tokyo", name:"東京駅", ward:"千代田区", lat:35.6812, lng:139.7671,
    walk:"徒歩1〜10分", bestTime:"夜", bestTimeLabel:"夜・平日夜がおすすめ", difficulty:"★",
    intro:"大正ロマンの赤レンガとオフィスビルのギャップ。平日夜はビルの明かりが灯り豪華な絵になる。雨上がりは水たまり反射が狙い目。",
    genres:["modern","retro","street"],
    articles:[
      { title:"東京駅周辺スナップ撮影ガイド｜駅舎から夜景まで", site:"Frame by Frame", url:"https://framebyframe.tokyo/tokyo-station-snap-photo-spots/1124/", note:"KITTEガーデン・和田倉噴水公園の長秒露光作例。" },
      { title:"大正ロマン漂う東京駅丸の内駅舎の撮影スポット6選", site:"note (Nocchi)", url:"https://note.com/nocchi_24/n/n91af188fe8c8", note:"広角での駅舎の撮り方を作例とともに丁寧に解説。" },
      { title:"丸の内〜有楽町エリアを撮り歩く", site:"カメラガールズ", url:"https://www.camera-girls.net/magazine/photospot/marunouchi_yurakucho/", note:"新旧建築を巡るフォトウォーク作例集。" },
      { title:"東京駅 撮影スポットガイド", site:"Nippon Photo Net", url:"https://nipponphoto.net/tokyo-tokyostation-spotguide/", note:"赤レンガの壁や水たまり反射作例が豊富。" },
    ],
  },
  {
    id:"ginza", name:"銀座駅", ward:"中央区", lat:35.6717, lng:139.7650,
    walk:"徒歩2〜10分", bestTime:"夜", bestTimeLabel:"夜・早朝がおすすめ", difficulty:"★★",
    intro:"洗練された街並みで反射・色遊びが映える。夜は銀座通りの長秒露光で光跡が撮れる。早朝は人がいなく静かな大人のスナップに。",
    genres:["street","modern"],
    articles:[
      { title:"心地よい街でスナップを愉しむ！銀座〜日本橋", site:"ShaSha / カメラのキタムラ", url:"https://www.kitamura.jp/shasha/sony/enjoy-snaps-in-comfortable-town-20230708/", note:"反射・ミラー越しの色遊び作例と撮影設定つき。" },
    ],
  },
  {
    id:"ikebukuro", name:"池袋駅", ward:"豊島区", lat:35.7295, lng:139.7109,
    walk:"徒歩2〜10分", bestTime:"夜", bestTimeLabel:"夜がおすすめ（夏は特に）", difficulty:"★",
    intro:"新宿・渋谷より「クセが強い」副都心。東京芸術劇場の美しい光、西口一番街のネオン、個性的なオブジェが揃う撮り甲斐ある街。",
    genres:["modern","night","street"],
    articles:[
      { title:"スナップで街めぐり。Vol.3｜池袋", site:"ShaSha / カメラのキタムラ", url:"https://www.kitamura.jp/shasha/article/485915982-20220311/", note:"露出補正の考え方を作例とともに詳しく解説。" },
      { title:"池袋西口夜スナップ完全ガイド｜45分コース", site:"Frame by Frame", url:"https://framebyframe.tokyo/ikebukuro-nishiguchi-night-snap-guide/1585/", note:"夕暮れから夜にかけてのルートと撮影テクニック。" },
      { title:"池袋周辺の撮影スポットまとめ13選", site:"note (Nocchi)", url:"https://note.com/nocchi_24/n/n3cd240bbe566", note:"東京芸術劇場など個性的なスポットを作例で網羅。" },
    ],
  },
  {
    id:"ueno", name:"上野駅", ward:"台東区", lat:35.7141, lng:139.7774,
    walk:"徒歩2〜10分", bestTime:"早朝", bestTimeLabel:"早朝・昼がおすすめ", difficulty:"★",
    intro:"近代建築と下町風情が共存。アメ横の喧騒は昼が楽しく、公園は雨上がりの朝に光芒が出ることも。年齢層の幅広さが写真に厚みを与える。",
    genres:["shitamachi","retro","street"],
    articles:[
      { title:"スナップで街めぐり。Vol.8｜上野", site:"ShaSha / カメラのキタムラ", url:"https://www.kitamura.jp/shasha/article/488981234-20220621/", note:"西洋美術館前・噴水・アメ横の作例と設定つき。" },
      { title:"上野の撮影スポット 近代建築と下町風情", site:"Nippon Photo Net", url:"https://nipponphoto.net/tokyo-ueno-spot/", note:"上野・日暮里・根津まで含めた下町エリア総まとめ。" },
      { title:"アメリカ横丁でスナップポートレート撮影｜作例有", site:"よろめき (wakajps)", url:"https://wakajps.com/blog/tokyo-america-street-portrait-photography-spot/", note:"高架下や路地の落書きを活かしたフィルム調作例。" },
    ],
  },
  {
    id:"nippori", name:"日暮里駅", ward:"荒川区", lat:35.7281, lng:139.7710,
    walk:"徒歩3〜8分", bestTime:"夕方", bestTimeLabel:"夕方がおすすめ", difficulty:"★",
    intro:"谷中銀座への入口「夕やけだんだん」は夕日撮影の名所。昭和のアーチ商店街と食べ歩きの煙が黄金光に染まる。",
    genres:["shitamachi","retro"],
    articles:[
      { title:"街ブラスナップ！都内オススメ商店街7選", site:"PhoLife", url:"https://pho-life.com/shop-tokyo/spot", note:"谷中銀座・神楽坂など下町商店街のスナップ作例集。" },
    ],
  },
  {
    id:"kagurazaka", name:"神楽坂駅", ward:"新宿区", lat:35.7021, lng:139.7401,
    walk:"徒歩1〜8分", bestTime:"夜", bestTimeLabel:"夜・雨の日がおすすめ", difficulty:"★★",
    intro:"石畳の路地「かくれんぼ横丁」「芸者小道」は雨の日に行燈が反射して幻想的。傘を差した人影を長秒露光で撮るのが定番。",
    genres:["retro","shitamachi"],
    articles:[
      { title:"みんなが撮影した神楽坂の写真（600作品以上）", site:"GANREF", url:"https://ganref.jp/spot/photo/jpn/lp/kagurazaka.html", note:"かくれんぼ横丁の石畳など投稿作品を閲覧可能。" },
      { title:"神楽坂 - 写真スポットで撮ってきました", site:"かめらとブログ。", url:"https://camera10.me/blog/photospot/kagurazaka", note:"赤城神社〜横丁を巡るカメラ散歩作例。" },
    ],
  },
  {
    id:"tsukishima", name:"月島駅", ward:"中央区", lat:35.6641, lng:139.7832,
    walk:"徒歩2〜10分", bestTime:"夜", bestTimeLabel:"夜・夕方がおすすめ", difficulty:"★",
    intro:"もんじゃストリートの提灯と高層マンション群のコントラストが独特。隅田川の橋と夕景も狙い目。下町と都市が混在する不思議な島。",
    genres:["shitamachi","modern","night"],
    articles:[
      { title:"佃・月島探訪 昔のレンズで「下町の摩天楼」を撮る", site:"日本経済新聞", url:"https://www.nikkei.com/article/DGXNASFK0700W_X00C14A2000000/", note:"プロと巡る相生橋・隅田川の夕景作例が充実。" },
      { title:"築地・勝どき・月島周辺の撮影スポットまとめ", site:"CameRife", url:"https://www.camerife.com/2022/08/tsukiji-kachidoki-tsukishima.html", note:"下町の街並みと再開発エリアを作例とともに網羅。" },
    ],
  },
  {
    id:"kiyosumi", name:"清澄白河駅", ward:"江東区", lat:35.6817, lng:139.8009,
    walk:"徒歩3〜12分", bestTime:"昼", bestTimeLabel:"昼・早朝がおすすめ", difficulty:"★★",
    intro:"コーヒーとアートの街として人気急上昇の穴場。隅田川沿いの散歩道、下町の路地裏、倉庫を改装したギャラリーが絵になる。",
    genres:["retro","street","art"],
    articles:[
      { title:"月島から清澄白河まで隅田川沿いを歩く（Kさんぽ）", site:"SPICE", url:"https://spice.eplus.jp/articles/201181", note:"下町と都会が交差する川沿いの散歩記。運河写真。" },
    ],
  },
  {
    id:"shinagawa", name:"品川駅", ward:"港区", lat:35.6284, lng:139.7387,
    walk:"徒歩5〜20分", bestTime:"夜", bestTimeLabel:"夜・昼がおすすめ", difficulty:"★★",
    intro:"近未来的なオフィス街と思わぬレトロが混在。駅のガラス張りエスカレーターや天王洲アイルの運河沿いウォールアートが隠れた名所。",
    genres:["modern","street"],
    articles:[
      { title:"品川付近の撮影スポットまとめ11選", site:"note (Nocchi)", url:"https://note.com/nocchi_24/n/nc3a95987775f", note:"港南口〜天王洲アイルまで雨でも撮れる場所を網羅。" },
      { title:"品川の撮影スポット 近未来的都市風景とレトロ", site:"Nippon Photo Net", url:"https://nipponphoto.net/tokyo-shinagawa-spot/", note:"エスカレーター反射・ウォールアートの作例。" },
    ],
  },
  {
    id:"akihabara", name:"秋葉原駅", ward:"千代田区", lat:35.6984, lng:139.7731,
    walk:"徒歩1〜10分", bestTime:"夜", bestTimeLabel:"夜・日曜昼（歩行者天国）がおすすめ", difficulty:"★",
    intro:"ネオンと電子部品とサブカルが溢れるカオスな街。夜の路面反射、日曜の歩行者天国、万世橋の赤レンガ高架下が撮影ポイント。",
    genres:["night","street","retro"],
    articles:[
      { title:"秋葉原周辺のおすすめ撮影スポット9選", site:"PhotoDou", url:"https://photodou.com/spot-akiba", note:"電気雑貨屋街・万世橋・アキバらしい作例を地図付きで。" },
      { title:"秋葉原周辺のマニアック撮影スポット", site:"アールイーカメラマガジン", url:"https://magazine.re-camera-shop.com/%E6%92%AE%E5%BD%B1%E3%82%B9%E3%83%9D%E3%83%83%E3%83%88/%E5%86%99%E7%9C%9F%E5%A5%BD%E3%81%8D%E5%BF%85%E8%A6%8B%EF%BC%81%E7%A7%8B%E8%91%89%E5%8E%9F%E5%91%A8%E8%BE%BA%E3%81%AE%E3%83%9E%E3%83%8B%E3%82%A2%E3%83%83%E3%82%AF%E6%92%AE%E5%BD%B1%E3%82%B9%E3%83%9D/", note:"神田明神・2k540など時間帯別の回り方も解説。" },
    ],
  },
  {
    id:"daikanyama", name:"代官山駅", ward:"渋谷区", lat:35.6487, lng:139.7033,
    walk:"徒歩3〜10分", bestTime:"昼", bestTimeLabel:"昼・夕方がおすすめ", difficulty:"★★",
    intro:"おしゃれな路地と緑豊かなT-SITE。廃線跡のログロード、蔦屋書店周辺の光と影が洗練された作品を生む。夜は静かで雰囲気がある。",
    genres:["modern","street","art"],
    articles:[
      { title:"代官山カメラ散歩コース", site:"カメラガールズ", url:"https://www.camera-girls.net/magazine/photospot/daikanyamawalk/", note:"代官山T-SITEや路地の作例。お手頃スポット付き。" },
      { title:"みんなが撮影した代官山の写真（400作品以上）", site:"GANREF", url:"https://ganref.jp/spot/photo/jpn/lp/daikanyama.html", note:"T-SITE緑豊かな敷地の撮影作品多数。" },
    ],
  },
  {
    id:"nakameguro", name:"中目黒駅", ward:"目黒区", lat:35.6444, lng:139.6989,
    walk:"徒歩1〜10分", bestTime:"夕方", bestTimeLabel:"夕方・夜・春（桜）がおすすめ", difficulty:"★",
    intro:"目黒川沿いの桜は都内屈指のスナップスポット。春は格別だが、川沿いのカフェや倉庫跡の路地は年中絵になる。夜のネオン反射も美しい。",
    genres:["nature","street","modern"],
    articles:[
      { title:"中目黒〜広尾 フォトジェニックな街歩き", site:"SPOT", url:"https://travel.spot-app.jp/chiputaso_instagram/", note:"別所坂など隠れた路地や壁面の発見が楽しい記事。" },
      { title:"祐天寺〜中目黒〜代官山フォトウォーク", site:"パスマーケット", url:"https://passmarket.yahoo.co.jp/event/show/detail/01p1iwxm7fic.html", note:"プロカメラマン主催のルート解説。3エリアの特色。" },
    ],
  },
  {
    id:"harajuku", name:"原宿駅", ward:"渋谷区", lat:35.6702, lng:139.7027,
    walk:"徒歩2〜15分", bestTime:"昼", bestTimeLabel:"昼・夕方がおすすめ", difficulty:"★",
    intro:"竹下通りのカラフルな喧騒と、表参道の洗練された並木道が一駅で体験できる。代々木公園の木漏れ日は早朝が格別。",
    genres:["street","nature","modern"],
    articles:[
      { title:"原宿〜渋谷フォトウォーク｜感性が刺激されるストリート", site:"Frame by Frame", url:"https://framebyframe.tokyo/harajuku-shibuya-photowalk/834/", note:"キャットストリートから渋谷まで1.5kmの作例ルート。" },
    ],
  },
];

const GENRE_LABELS = { street:'ストリート', nature:'自然', night:'夜景', retro:'レトロ', modern:'モダン', shitamachi:'下町', art:'アート' };
const GENRE_COLORS = { street:'#7aafd4', nature:'#7ac487', night:'#b07ad4', retro:'#d4a87a', modern:'#7ac4d4', shitamachi:'#d47a7a', art:'#7ad4c4' };
const TIME_ICONS = { 早朝:'🌅', 昼:'☀️', 夕方:'🌇', 夜:'🌙' };

const map = L.map('map', { center:[35.685, 139.745], zoom:12 });
L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png', {
  attribution:'&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors &copy; <a href="https://carto.com/attributions">CARTO</a>',
  subdomains:'abcd', maxZoom:19
}).addTo(map);

let activeGenre='all', activeTime='all', selectedId=null;
const markers={};

function makeIcon(genres, selected) {
  const col = GENRE_COLORS[genres[0]] || '#C8A96E';
  const r = selected ? 10 : 8;
  const ring = selected ? `<circle cx="18" cy="18" r="15" fill="none" stroke="${col}" stroke-width="1.5" opacity="0.4"/>` : '';
  const glow = selected ? ' filter="url(#g)"' : '';
  const html = `<svg xmlns="http://www.w3.org/2000/svg" width="36" height="36" viewBox="0 0 36 36">
    <defs><filter id="g"><feGaussianBlur stdDeviation="2.5" result="b"/><feMerge><feMergeNode in="b"/><feMergeNode in="SourceGraphic"/></feMerge></filter></defs>
    ${ring}<circle cx="18" cy="18" r="${r}"${glow} fill="${col}" opacity="${selected?1:0.85}"/>
    <text x="18" y="22" font-size="9" text-anchor="middle" fill="#0a0a0a">🚉</text>
  </svg>`;
  return L.divIcon({ html, className:'', iconSize:[36,36], iconAnchor:[18,18], popupAnchor:[0,-20] });
}

function render() {
  const panel = document.getElementById('list-panel');
  panel.innerHTML = '';
  Object.values(markers).forEach(m => map.removeLayer(m));
  for (const k in markers) delete markers[k];

  const visible = STATIONS.filter(s =>
    (activeGenre==='all' || s.genres.includes(activeGenre)) &&
    (activeTime==='all'  || s.bestTime===activeTime)
  );
  const total = visible.reduce((n,s)=>n+s.articles.length,0);
  document.getElementById('count-badge').textContent=`${visible.length}駅 · ${total}記事`;

  visible.forEach(s => {
    const sel = s.id===selectedId;
    const genreTags = s.genres.map(g=>`<span class="genre-tag ${g}">${GENRE_LABELS[g]}</span>`).join('');
    const articleCards = s.articles.map(a=>`
      <a class="article-card" href="${a.url}" target="_blank" rel="noopener">
        <div class="article-title">${a.title}</div>
        <div class="article-meta">
          <span class="article-site">${a.site}</span>
          <span class="article-arrow">記事を見る ↗</span>
        </div>
        <div class="article-note">${a.note}</div>
      </a>`).join('');

    const card = document.createElement('div');
    card.className='station-card'+(sel?' selected':'');
    card.dataset.id=s.id;
    card.innerHTML=`
      <div class="station-head">
        <div class="station-top">
          <span class="station-name">🚉 ${s.name}</span>
          <span class="station-ward">${s.ward}</span>
        </div>
        <div class="station-meta">
          <div class="meta-item"><span class="meta-icon">🚶</span><b>${s.walk}</b></div>
          <div class="meta-item"><span class="meta-icon">${TIME_ICONS[s.bestTime]||'📷'}</span><b>${s.bestTimeLabel}</b></div>
          <div class="meta-item"><span class="meta-icon">📷</span>難易度 <b>${s.difficulty}</b></div>
        </div>
        <p class="station-intro">${s.intro}</p>
        <div class="station-genres">${genreTags}<span class="article-count">${s.articles.length}記事</span></div>
      </div>
      <div class="articles">
        <div class="articles-label">参考記事・作例</div>
        ${articleCards}
      </div>`;
    card.querySelector('.station-head').addEventListener('click',()=>selectStation(s.id));
    panel.appendChild(card);

    const popup=L.popup({maxWidth:270,minWidth:210,closeButton:true})
      .setContent(`<div class="popup-body">
        <div class="popup-name">🚉 ${s.name}</div>
        <div class="popup-ward">${s.ward} · ${s.articles.length}件の記事</div>
        <div class="popup-meta">
          <span class="popup-meta-item">🚶 ${s.walk}</span>
          <span class="popup-meta-item">${TIME_ICONS[s.bestTime]||''} ${s.bestTimeLabel}</span>
        </div>
        <p class="popup-intro">${s.intro}</p>
        <span class="popup-cta" onclick="selectStation('${s.id}')">記事一覧を開く →</span>
      </div>`);
    const marker=L.marker([s.lat,s.lng],{icon:makeIcon(s.genres,sel)}).addTo(map).bindPopup(popup);
    marker.on('click',()=>selectStation(s.id));
    markers[s.id]=marker;
    if(sel) marker.openPopup();
  });

  const disc=document.createElement('div');
  disc.className='disclaimer';
  disc.innerHTML='※ 作例写真は各記事の著作者に帰属します。本サービスは記事へのリンク誘導のみを行い、画像の転載は一切していません。';
  panel.appendChild(disc);
}

function selectStation(id){
  selectedId=selectedId===id?null:id;
  render();
  if(selectedId){
    const s=STATIONS.find(x=>x.id===selectedId);
    map.setView([s.lat,s.lng],Math.max(map.getZoom(),14),{animate:true});
    document.querySelector(`.station-card[data-id="${id}"]`)?.scrollIntoView({behavior:'smooth',block:'start'});
  }
}

function fitVisible(){
  const pts=STATIONS.filter(s=>
    (activeGenre==='all'||s.genres.includes(activeGenre))&&
    (activeTime==='all'||s.bestTime===activeTime)
  ).map(s=>[s.lat,s.lng]);
  if(pts.length) map.fitBounds(L.latLngBounds(pts),{padding:[40,40],maxZoom:14,animate:true});
}

document.getElementById('genre-filters').addEventListener('click',e=>{
  if(!e.target.matches('.filter-btn')) return;
  document.querySelectorAll('#genre-filters .filter-btn').forEach(b=>b.classList.remove('active'));
  e.target.classList.add('active');
  activeGenre=e.target.dataset.genre;
  selectedId=null; render(); fitVisible();
});
document.getElementById('time-filters').addEventListener('click',e=>{
  if(!e.target.matches('.filter-btn')) return;
  document.querySelectorAll('#time-filters .filter-btn').forEach(b=>b.classList.remove('active'));
  e.target.classList.add('active');
  activeTime=e.target.dataset.time;
  selectedId=null; render(); fitVisible();
});

window.selectStation=selectStation;
render();
</script>
</body>
</html>
