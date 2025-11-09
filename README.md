<!DOCTYPE html>
<html lang="th">
<head>
  <meta charset="UTF-8">
  <title>Drawing Status Manager</title>
  <style>
    body {
      font-family: "Segoe UI", sans-serif;
      background-color: #f2f2f2;
      padding: 20px;
      color: #333;
    }

    h2 {
      text-align: center;
      color: #444;
    }

    input, select, button {
      padding: 8px;
      margin: 6px 0;
      border-radius: 6px;
      border: 1px solid #ccc;
      font-size: 14px;
    }

    button {
      background-color: #4CAF50;
      color: white;
      border: none;
      cursor: pointer;
      transition: 0.2s;
    }

    button:hover {
      background-color: #45a049;
    }

    .container {
      background: white;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
      max-width: 600px;
      margin: 0 auto;
    }

    .btn-group {
      display: flex;
      gap: 10px;
      justify-content: center;
    }

    #result {
      background: #e9e9e9;
      padding: 10px;
      border-radius: 6px;
      margin-top: 15px;
    }
  </style>
</head>
<body>
  <div class="container">
    <h2>Drawing Status Manager</h2>

    <label>Drawing Number:</label><br>
    <input type="text" id="drawingNo" placeholder="เช่น AC-001"><br>

    <label>Date:</label><br>
    <input type="date" id="date"><br>

    <label>Status:</label><br>
    <input type="text" id="status" placeholder="เช่น Completed, Pending"><br>

    <div class="btn-group">
      <button onclick="searchDrawing()">🔍 ค้นหา</button>
      <button onclick="addDrawing()">💾 เพิ่มข้อมูลใหม่</button>
      <button onclick="updateDrawing()">🔁 แทนที่ข้อมูลเดิม</button>
    </div>

    <div id="result"></div>
  </div>

  <script>
    const scriptUrl = "PASTE_YOUR_DEPLOYMENT_URL_HERE"; // <<== วาง URL จาก Apps Script

    async function searchDrawing() {
      const drawingNo = document.getElementById("drawingNo").value.trim();
      if (!drawingNo) {
        alert("กรุณากรอก Drawing Number");
        return;
      }

      try {
        const res = await fetch(`${scriptUrl}?drawingNo=${encodeURIComponent(drawingNo)}`);
        const data = await res.json();
        if (data.error) {
          document.getElementById("result").innerHTML = `<b>ไม่พบข้อมูล</b>`;
        } else {
          document.getElementById("result").innerHTML =
            `<b>Drawing:</b> ${data.DrawingNo}<br>` +
            `<b>วันที่ล่าสุด:</b> ${data.Date}<br>` +
            `<b>Status:</b> ${data.Status}`;
        }
      } catch (err) {
        alert("เกิดข้อผิดพลาด: " + err.message);
      }
    }

    async function addDrawing() {
      const drawingNo = document.getElementById("drawingNo").value.trim();
      const date = document.getElementById("date").value;
      const status = document.getElementById("status").value.trim();

      if (!drawingNo || !date || !status) {
        alert("กรุณากรอกข้อมูลให้ครบ");
        return;
      }

      try {
        const res = await fetch(scriptUrl, {
          method: "POST",
          body: JSON.stringify({ drawingNo, date, status, action: "add" }),
          headers: { "Content-Type": "application/json" },
        });
        const data = await res.json();
        alert(data.message);
      } catch (err) {
        alert("เกิดข้อผิดพลาด: " + err.message);
      }
    }

    async function updateDrawing() {
      const drawingNo = document.getElementById("drawingNo").value.trim();
      const date = document.getElementById("date").value;
      const status = document.getElementById("status").value.trim();

      if (!drawingNo || !date || !status) {
        alert("กรุณากรอกข้อมูลให้ครบ");
        return;
      }

      try {
        const res = await fetch(scriptUrl, {
          method: "POST",
          body: JSON.stringify({ drawingNo, date, status, action: "update" }),
          headers: { "Content-Type": "application/json" },
        });
        const data = await res.json();
        alert(data.message);
      } catch (err) {
        alert("เกิดข้อผิดพลาด: " + err.message);
      }
    }
  </script>
</body>
</html>
