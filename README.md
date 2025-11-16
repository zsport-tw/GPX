<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <title>GPX 照片比對工具</title>
  <style>
    body { font-family: sans-serif; padding: 20px; background: #f9f9f9; }
    h1 { color: #333; }
    h2 { margin-top: 30px; color: #007bff; }
    .coord-btn { margin: 5px; padding: 8px 12px; background: #007bff; color: #fff; border: none; border-radius: 5px; cursor: pointer; }
    .coord-btn:hover { background: #0056b3; }
  </style>
</head>
<body>
  <h1>📍 GPX + 攝影師座標 → 查詢通過時間</h1>

  <p>📁 上傳你的 GPX 檔案：</p>
  <input type="file" id="gpxFile" accept=".gpx"><br><br>

  <div id="coords">
    <h4
  🚴 <a href="https://www.zsport.com.tw/activity/186" target="_blank" style="color: #007bff; text-decoration: none;">
    2025TBA 單車賽事←相簿
  </a>
</h2>


    <!-- TBA 賽事攝影師 -->
  </div>

  <div id="coords2">
    <h4>
  🚴 <a href="https://www.zsport.com.tw/activity/190" target="_blank" style="color: #007bff; text-decoration: none;">
    2025TWB 單車賽事←相簿
  </a>
</h2>

    <!-- TWB 賽事攝影師 -->
  </div>

  <h3>📷 查詢結果：</h3>
  <div id="result"></div>

  <script>
    const locationsTBA = [
      { photographer: "小得哥、甜心女王、星星、安格斯", place: "南鯤鯓", lat: 23.299510, lon: 120.145958 },
      { photographer: "森上梅友前", place: "後龍", lat: 24.5891150, lon: 120.7318792 },
      { photographer: "黑皮", place: "台17與170路口", lat: 23.466040, lon: 120.166971 },
      { photographer: "唐志清", place: "嘉義東石", lat: 23.449613, lon: 120.165399 },
      { photographer: "poy、yu", place: "香山", lat: 24.7828758, lon: 120.9153397 },
      { photographer: "poy、yu", place: "嘉義東石", lat: 23.4381698, lon: 120.1671996 },
      { photographer: "麥克大叔、GRACE", place: "彰化", lat: 23.918116, lon: 120.312961 },
      { photographer: "麥克大叔、GRACE", place: "將軍", lat: 23.230058, lon: 120.136017 },
      { photographer: "米香", place: "口湖", lat: 23.579468, lon: 120.149399 },
      { photographer: "米香", place: "灣裡", lat: 22.921706, lon: 120.175208 },
      { photographer: "村長攝影", place: "新竹", lat: 24.831559, lon: 120.926735 },
      { photographer: "村長攝影", place: "雲林成龍", lat: 23.559443, lon: 120.165412 },
      { photographer: "熊出沒-熊戰", place: "鳳鼻遂道", lat: 24.883703, lon: 120.955883 }
    ];

    const locationsTWB = [
      { photographer: "小得哥、甜心女王、星星", place: "南鯤鯓", lat: 23.299510, lon: 120.145958 },
      { photographer: "森上梅友前", place: "後龍", lat: 24.5891150, lon: 120.7318792 },
      { photographer: "黑皮", place: "大安", lat: 24.372753, lon: 120.587729 },
      { photographer: "黑皮", place: "東石", lat: 23.466314, lon: 120.167000 },
      { photographer: "唐志清", place: "福海宮前", lat: 23.953077, lon: 120.335895 },
      { photographer: "poy、yu", place: "香山", lat: 24.781917, lon: 120.915039 },
      { photographer: "poy、yu", place: "口湖", lat: 23.5436149, lon: 120.1864506 },
      { photographer: "麥克大叔、GRACE", place: "彰化", lat: 23.918116, lon: 120.312961 },
      { photographer: "麥克大叔、GRACE", place: "將軍", lat: 23.230058, lon: 120.136017 },
      { photographer: "村長攝影", place: "新竹", lat: 24.830144, lon: 120.926593 },
      { photographer: "村長攝影", place: "台西", lat: 23.726671, lon: 120.219177 },
      { photographer: "米香", place: "口湖", lat: 23.34462, lon: 120.08578 },
      { photographer: "無限任務", place: "口湖", lat: 23.559626, lon: 120.165043 },
      { photographer: "無限任務", place: "茄萣", lat: 22.880202, lon: 120.213925 },
      { photographer: "熊出沒-熊戰", place: "鳳鼻遂道", lat: 24.887729243480017, lon: 120.95716034899868 },
      { photographer: "熊出沒-熊戰", place: "芳苑普天宮", lat: 23.91796892015688, lon: 120.31283748583725 },
      { photographer: "熊出沒-熊戰", place: "台南國姓橋", lat: 23.094856049266873, lon: 120.13135725379318 }
    ];

    let gpxPoints = [];

    document.getElementById('gpxFile').addEventListener('change', function (e) {
      const file = e.target.files[0];
      const reader = new FileReader();
      reader.onload = function () {
        const parser = new DOMParser();
        const xml = parser.parseFromString(reader.result, "application/xml");
        const trkpts = xml.getElementsByTagName("trkpt");
        gpxPoints = [];
        for (let i = 0; i < trkpts.length; i++) {
          const lat = parseFloat(trkpts[i].getAttribute("lat"));
          const lon = parseFloat(trkpts[i].getAttribute("lon"));
          const time = new Date(trkpts[i].getElementsByTagName("time")[0].textContent);
          gpxPoints.push({ lat, lon, time });
        }
      alert("✅ GPX成功載入、選賽事點攝影師、下面顯示通過時間");

      };
      reader.readAsText(file);
    });

    function renderButtons(locations, containerId) {
      const container = document.getElementById(containerId);
      locations.forEach((loc) => {
        const btn = document.createElement("button");
        btn.className = "coord-btn";
        btn.textContent = `${loc.photographer}（${loc.place}）`;
        btn.onclick = () => checkTime(loc.lat, loc.lon, loc.photographer, loc.place);
        container.appendChild(btn);
      });
    }

    renderButtons(locationsTBA, "coords");
    renderButtons(locationsTWB, "coords2");

    function checkTime(targetLat, targetLon, photographer, place) {
      if (gpxPoints.length === 0) {
        alert("請先上傳 GPX 檔案！");
        return;
      }

      let closest = null;
      let minDist = Infinity;
      for (const pt of gpxPoints) {
        const d = getDistance(pt.lat, pt.lon, targetLat, targetLon);
        if (d < minDist) {
          minDist = d;
          closest = pt;
        }
      }

      const passTime = closest.time;
      const resultDiv = document.getElementById("result");
      resultDiv.innerHTML = `
        <p>📸 攝影師：<strong>${photographer}</strong></p>
        <p>📍 拍攝地點：<strong style="color: blue;">${place}</strong></p>
        <p>🚴 通過時間：<strong style="color: red;">${passTime.toLocaleTimeString()}</strong></p>
      `;
    }

    function getDistance(lat1, lon1, lat2, lon2) {
      const R = 6371e3;
      const φ1 = lat1 * Math.PI / 180;
      const φ2 = lat2 * Math.PI / 180;
      const Δφ = (lat2 - lat1) * Math.PI / 180;
      const Δλ = (lon2 - lon1) * Math.PI / 180;
      const a = Math.sin(Δφ/2) * Math.sin(Δφ/2) +
                Math.cos(φ1) * Math.cos(φ2) *
                Math.sin(Δλ/2) * Math.sin(Δλ/2);
      const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
      return R * c;
    }
  </script>
</body>
</html>
