# INH_Record_Revision
บันทึกการส่ง Fab sheet V.
<!DOCTYPE html>
<html lang="th">
<head>
  <meta charset="UTF-8">
  <title>Drawing Submission Smart Form</title>
  <style>
    body {
      font-family: sans-serif;
      background: #f4f6fb;
      max-width: 650px;
      margin: auto;
      padding: 25px;
    }
    h2 { text-align: center; }
    label { display:block; margin-top:10px; font-weight:bold; }
    input, select, textarea {
      width:100%; padding:8px; border:1px solid #ccc; border-radius:5px; margin-top:5px;
    }
    textarea { height: 100px; resize: vertical; }
    button {
      margin-top:15px; width:100%; padding:10px;
      border:none; border-radius:5px;
      font-size:16px; color:white;
      background:#007bff;
    }
    .info {
      background:#e9f1ff;
      border:1px solid #bcd3ff;
      padding:10px;
      border-radius:6px;
      margin-top:10px;
    }
  </style>
</head>
<body>
  <h2>Drawing Submission Smart Form</h2>

  <form id="recordForm">
    <label>วันที่ส่ง</label>
    <input type="date" name="DateSent" required>

    <label>Drawing Number (สามารถวางหลายบรรทัดได้)</label>
    <textarea id="DrawingNo" name="DrawingNo" placeholder="เช่น AC-001&#10;AC-001R&#10;AC-002" required></textarea>

    <button type="button" id="checkBtn" style="background:#28a745;">ตรวจสอบข้อมูล</button>

    <div id="infoBox" class="info" style="display:none;">
      <p><b>ผลการตรวจสอบ:</b></p>
      <ul id="resultList"></ul>
    </div>

    <label>สถานะการส่ง</label>
    <select name="Status" id="Status" required>
      <option value="">-- เลือกสถานะ --</option>
      <option value="แนบเอกสารซ้ำ">แนบเอกสารซ้ำ (Rev เพิ่ม / R. เดิม)</option>
      <option value="แก้ไขเพิ่ม">แก้ไขเพิ่ม (Rev + R. เพิ่ม)</option>
      <option value="แผ่นใหม่">แผ่นใหม่ (เริ่ม Rev และ R. ใหม่)</option>
    </select>

    <button type="submit">บันทึกข้อมูล</button>
  </form>

  <script>
    const baseURL = "https://script.google.com/macros/s/AKfycbztfkvUF1QGGzMQF-6VBGy7HOCp6ir15iMMhQkOzMB6Kc-ZqxvukmHqu3wmcbLlcgIf/exec";

    const form = document.getElementById("recordForm");
    const infoBox = document.getElementById("infoBox");
    const resultList = document.getElementById("resultList");
    const checkBtn = document.getElementById("checkBtn");

    checkBtn.addEventListener("click", async () => {
      const drawingText = document.getElementById("DrawingNo").value.trim();
      if (!drawingText) return alert("กรุณากรอก Drawing Number ก่อน");

      const drawings = drawingText.split(/\n+/).map(d => d.trim()).filter(d => d);
      if (drawings.length === 0) return alert("ไม่มี Drawing Number ที่ถูกต้อง");

      resultList.innerHTML = "";
      infoBox.style.display = "block";
      resultList.innerHTML = "<li>⏳ กำลังตรวจสอบ...</li>";

      const results = [];

      for (const drawingNo of drawings) {
        try {
          const res = await fetch(`${baseURL}?action=get&DrawingNo=${encodeURIComponent(drawingNo)}`);
          const data = await res.json();
          results.push(`<li><b>${drawingNo}</b> → Rev: ${data.Rev || "-"}, R: ${data.R || "-"}</li>`);
        } catch {
          results.push(`<li><b>${drawingNo}</b> → ❌ ไม่พบข้อมูล</li>`);
        }
      }

      resultList.innerHTML = results.join("");
    });

    form.addEventListener("submit", e => {
      e.preventDefault();
      fetch(baseURL, { method: "POST", body: new FormData(form) })
        .then(res => res.text())
        .then(msg => alert(msg))
        .catch(err => alert("เกิดข้อผิดพลาด: " + err.message));
      form.reset();
      infoBox.style.display = "none";
    });
  </script>
</body>
</html>
