<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <title>نظام قطع الغيار AMD SPL 2025</title>
  <style>
    body { font-family: 'Segoe UI', sans-serif; background: #f2f2f2; margin: 0; padding: 20px; color: #333; }
    h1 { color: #0061a8; font-size: 28px; }
    .section { background: #fff; padding: 20px; margin-bottom: 20px; border-radius: 8px; box-shadow: 0 0 8px rgba(0,0,0,0.1); }
    input, select, button { margin: 5px; padding: 8px; border-radius: 4px; border: 1px solid #ccc; font-size: 14px; }
    table { width: 100%; border-collapse: collapse; margin-top: 10px; }
    th, td { padding: 8px; border-bottom: 1px solid #ccc; text-align: center; }
    th { background: #eee; }
    .profit-negative { background: #ffc0cb; }
    .gold { color: #d4af37; font-weight: bold; }
    .silver { color: #aaa; }
    .stats { font-size: 16px; margin-top: 10px; }
  </style>
</head>
<body>

  <h1>نظام إدارة قطع الغيار AMD SPL 2025</h1>

  <div class="section" id="inventory">
    <h2>📦 القطع المتوفرة</h2>
    <table id="partsTable">
      <thead>
        <tr>
          <th>رقم</th><th>الاسم</th><th>الوصف</th><th>السعر</th><th>التكلفة</th><th>الربح</th><th>المخزون</th><th>إجراء</th>
        </tr>
      </thead>
      <tbody></tbody>
    </table>
    <button onclick="addPart()">➕ إضافة قطعة جديدة</button>
  </div>

  <div class="section" id="invoice">
    <h2>🧾 إصدار فاتورة</h2>
    <select id="selectPart"></select>
    <input type="number" id="invoiceQty" placeholder="الكمية">
    <input type="text" id="clientName" placeholder="اسم العميل">
    <button onclick="generateInvoice()">🎯 إصدار</button>
    <div id="invoiceOutput"></div>
  </div>

  <div class="section" id="stats">
    <h2>📊 الإحصائيات</h2>
    <div class="stats" id="statsSummary"></div>
  </div>

  <script>
    let parts = [];

    function addPart() {
      const id = prompt("رقم القطعة:");
      const name = prompt("اسم القطعة:");
      const desc = prompt("وصف القطعة:");
      const price = parseFloat(prompt("السعر:"));
      const cost = parseFloat(prompt("التكلفة:"));
      const stock = parseInt(prompt("المخزون:"));
      const profit = price - cost;

      parts.push({ id, name, desc, price, cost, profit, stock });
      updateTable();
      updateStats();
      updateDropdown();
    }

    function updateTable() {
      const tbody = document.querySelector("#partsTable tbody");
      tbody.innerHTML = "";
      parts.forEach((p, i) => {
        const row = document.createElement("tr");
        if (p.profit < 0) row.classList.add("profit-negative");
        row.innerHTML = `
          <td>${p.id}</td><td>${p.name}</td><td>${p.desc}</td>
          <td>${p.price}</td><td>${p.cost}</td>
          <td>${p.profit}</td><td>${p.stock}</td>
          <td><button onclick="removePart(${i})">🗑️ حذف</button></td>
        `;
        tbody.appendChild(row);
      });
    }

    function removePart(index) {
      parts.splice(index, 1);
      updateTable();
      updateStats();
      updateDropdown();
    }

    function updateDropdown() {
      const select = document.getElementById("selectPart");
      select.innerHTML = "";
      parts.forEach((p, i) => {
        const option = document.createElement("option");
        option.value = i;
        option.text = `${p.name} (${p.stock})`;
        select.appendChild(option);
      });
    }

    function generateInvoice() {
      const index = parseInt(document.getElementById("selectPart").value);
      const qty = parseInt(document.getElementById("invoiceQty").value);
      const client = document.getElementById("clientName").value;
      const part = parts[index];
      if (!part || qty > part.stock) return alert("كمية غير متوفرة!");

      part.stock -= qty;
      updateTable();
      updateDropdown();
      updateStats();

      const total = qty * part.price;
      const profit = qty * (part.price - part.cost);
      const today = new Date().toLocaleDateString();

      document.getElementById("invoiceOutput").innerHTML = `
        <div class="section gold">
          <h3>فاتورة العميل: ${client}</h3>
          <p>القطعة: ${part.name}</p>
          <p>الكمية: ${qty}</p>
          <p>الإجمالي: ${total} ريال</p>
          <p>الربح المتوقع: ${profit} ريال</p>
          <p>تاريخ الإصدار: ${today}</p>
        </div>
      `;
    }

    function updateStats() {
      const total = parts.length;
      const avgProfit = (parts.reduce((acc, p) => acc + p.profit, 0) / total).toFixed(2);
      const inventoryValue = parts.reduce((acc, p) => acc + (p.stock * p.price), 0).toFixed(2);
      document.getElementById("statsSummary").innerHTML = `
        🧮 عدد القطع: ${total} — 💰 متوسط الربح: ${avgProfit} ريال  
        📦 إجمالي قيمة المخزون: ${inventoryValue} ريال
      `;
    }
  </script>

</body>
</html>
