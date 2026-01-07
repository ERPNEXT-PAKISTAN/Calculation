<p align="center">
  <img width="420" height="120" alt="Dashboard Banner" src="https://github.com/user-attachments/assets/b6b12445-4a2b-4f34-92c4-7819e5c491cc" />
</p>

<h1 align="center">
  <img src="https://img.icons8.com/fluency/48/combo-chart.png" width="36" alt="analytics"/> 
  <span style="vertical-align:middle"> Calculator in ERPNext </span>
  <img src="https://img.icons8.com/fluency/48/speed.png" width="36" alt="performance"/>
</h1>


<!-- KPI badges (uniform) -->
<p align="center">
  <img src="https://img.shields.io/badge/Revenue-%24125k-0ea5e9?style=for-the-badge&logo=google" alt="Sales Order" />
  <img src="https://img.shields.io/badge/Orders-3.2k-0892ff?style=for-the-badge" alt="Sales Invoice" />
  <img src="https://img.shields.io/badge/Avg%20Order%20Value-%2459-06b6d4?style=for-the-badge" Purchase Invoice" />
  <img src="https://img.shields.io/badge/Conversion-2.8%25-00c853?style=for-the-badge" alt="Payment Entry" />
</p>

<p align="center">
  <b>Sales Order | Sales Invoice | Purchase Order | Purchase Receipt | Payment Entry | Stock Entry</b>
</p>


<p align="center">
  <img src="https://github.com/user-attachments/assets/2368d9b6-f32f-44a0-9462-75675ee07b9b"
       alt="image"
       width="220" height="340"
       style="border-radius:8px; box-shadow:0 4px 12px rgba(0,0,0,0.12); object-fit:cover;" />
</p>





---





## <img src="https://img.icons8.com/color/48/000000/javascript--v1.png" width="35"/> JavaScript Code

<details>
<summary><img src="https://img.icons8.com/color/48/000000/code-file.png" width="22"/> Copy JavaScript</summary>

```javascript
// ===== Change only this DocType name for other screens =====

frappe.ui.form.on("Expense Claim", {
//frappe.ui.form.on("Salary Slip", {
//frappe.ui.form.on("Payment Entry", {
//frappe.ui.form.on("Sales Invoice", {
//frappe.ui.form.on("Delivery Note", {
//frappe.ui.form.on("Purchase Order", {
//frappe.ui.form.on("Quotation", {
//frappe.ui.form.on("Purchase Receipt", {
//frappe.ui.form.on("Purchase Invoice", {
//frappe.ui.form.on("Stock Entry", {
//frappe.ui.form.on("Material Request", {
//frappe.ui.form.on("Sales Order", {
  refresh(frm) {
    window.pinmx_calc && window.pinmx_calc.attach(frm);
  },
  onhide(frm) {
    window.pinmx_calc && window.pinmx_calc.remove();
  }
});

// ===== Shared code (safe to paste in many doctypes) =====
(function () {
  if (window.pinmx_calc) return; // already loaded in another doctype

  window.pinmx_calc = {
    attach(frm) {
      if (frm.__pin_calc_btn_added) return;
      frm.__pin_calc_btn_added = true;

      frm.add_custom_button(__("Calculator"), () => {
        window.pinmx_calc.open();
      });

      // Keep YOUR purple toolbar button style
      setTimeout(() => {
        const $btn = frm.page.wrapper
          .find(".btn")
          .filter(function () {
            return (($(this).text() || "").trim() === __("Calculator"));
          })
          .last();

        $btn.removeClass("btn-default").addClass("btn-primary");
        $btn.css({
          "background-color": "#a10394",
          "border-color": "#a10394",
          "color": "#ffffff"
        });
      }, 200);
    },

    remove() {
      const el = document.getElementById("pinmx-floating-calc");
      if (el) el.remove();
    },

    open() {
      let el = document.getElementById("pinmx-floating-calc");
      if (el) {
        el.style.display = "block";
        el.style.zIndex = 99999;
        const inp = el.querySelector(".pinmx-calc-input");
        inp && inp.focus();
        return;
      }

      el = document.createElement("div");
      el.id = "pinmx-floating-calc";

      // ✅ YOUR layout + YOUR colors + buttons etc.
      el.innerHTML = `
        <div class="pinmx-calc-header">
          <div class="pinmx-calc-title">${__("Calculator")}</div>
          <div class="pinmx-calc-actions">
            <button type="button" class="btn btn-xs btn-default pinmx-min" title="${__("Minimize")}">—</button>
            <button type="button" class="btn btn-xs btn-default pinmx-close" title="${__("Close")}">✕</button>
          </div>
        </div>

        <div class="pinmx-calc-body">
          <div class="pinmx-screens">
            <textarea rows="2" class="pinmx-calc-input" placeholder="Enter expression..."></textarea>
            <input type="text" class="pinmx-calc-result" placeholder="Result" readonly />
          </div>

          <div class="pinmx-calc-grid">
            <button type="button" data-k="C"  class="btn btn-default">C</button>
            <button type="button" data-k="BS" class="btn btn-default">⌫</button>
            <button type="button" data-k="("  class="btn btn-default">(</button>
            <button type="button" data-k=")"  class="btn btn-default">)</button>

            <button type="button" data-k="7" class="btn btn-default">7</button>
            <button type="button" data-k="8" class="btn btn-default">8</button>
            <button type="button" data-k="9" class="btn btn-default">9</button>
            <button type="button" data-k="/" class="btn btn-default">÷</button>

            <button type="button" data-k="4" class="btn btn-default">4</button>
            <button type="button" data-k="5" class="btn btn-default">5</button>
            <button type="button" data-k="6" class="btn btn-default">6</button>
            <button type="button" data-k="*" class="btn btn-default">×</button>

            <button type="button" data-k="1" class="btn btn-default">1</button>
            <button type="button" data-k="2" class="btn btn-default">2</button>
            <button type="button" data-k="3" class="btn btn-default">3</button>
            <button type="button" data-k="-" class="btn btn-default">-</button>

            <button type="button" data-k="0" class="btn btn-default pinmx-zero">0</button>
            <button type="button" data-k="." class="btn btn-default">.</button>
            <button type="button" data-k="=" class="btn btn-primary pinmx-equals">=</button>
            <button type="button" data-k="+" class="btn btn-default pinmx-plus">+</button>
          </div>
        </div>

        <style>
          #pinmx-floating-calc{
            position: fixed;
            top: 72px;
            right: 14px;
            width: 280px;
            background:rgb(180, 36, 199);
            border: none;
            border-radius: 16px;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15), 0 0 0 1px rgba(0, 0, 0, 0.05);
            z-index: 99999;
            overflow: hidden;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
          }

          #pinmx-floating-calc .pinmx-calc-header{
            display:flex;
            align-items:center;
            justify-content:space-between;
            padding: 9px 16px;
            background: #a10394;
            cursor: move;
            user-select: none;
          }

          #pinmx-floating-calc .pinmx-calc-title{
            font-weight: 600;
            font-size: 10.5px;
            color: #ffffff;
            letter-spacing: 0.3px;
          }

          #pinmx-floating-calc .pinmx-calc-actions{
            display:flex;
            gap: 4px;
          }

          #pinmx-floating-calc .pinmx-calc-actions .btn{
            border: none;
            background: rgba(255, 255, 255, 0.2);
            padding: 3px 6px;
            color: #ffffff;
            border-radius: 6px;
            font-size: 9px;
            min-width: 18px;
            height: 18px;
            transition: all 0.2s ease;
          }

          #pinmx-floating-calc .pinmx-calc-actions .btn:hover{
            background: rgba(255, 255, 255, 0.3);
            transform: scale(1.05);
          }

          #pinmx-floating-calc .pinmx-calc-body{
            padding: 12px;
            background: #ffffff;
          }

          #pinmx-floating-calc .pinmx-screens{
            display: grid;
            gap: 7.5px;
            margin-bottom: 12px;
          }

          #pinmx-floating-calc .pinmx-calc-input{
            width:100%;
            font-size: 16px;
            padding: 9px 10.5px;
            text-align:right;
            border: 2px solid #e5e7eb;
            border-radius: 12px;
            color: #111827;
            outline: none;
            resize: none;
            line-height: 1.5;
            background: #fafafa;
            transition: all 0.2s ease;
            font-family: 'Courier New', monospace;
          }

          #pinmx-floating-calc .pinmx-calc-input:focus{
            border-color: #667eea;
            background: #ffffff;
            box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
          }

          #pinmx-floating-calc .pinmx-calc-result{
            width:100%;
            font-size: 15px;
            padding: 9px 10.5px;
            text-align:right;
            border: 2px solid #e5e7eb;
            border-radius: 12px;
            background: linear-gradient(135deg, #f8fafc 0%, #f1f5f9 100%);
            font-weight: 700;
            color: #0f172a;
            outline: none;
            font-family: 'Courier New', monospace;
          }

          #pinmx-floating-calc .pinmx-calc-grid{
            display:grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 6px;
          }

          #pinmx-floating-calc .pinmx-calc-grid .btn{
            padding: 10.5px 0;
            border-radius: 12px;
            font-size: 12px;
            font-weight: 600;
            background: #ffffff;
            border: 2px solid #e5e7eb;
            color: #374151;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.04);
            transition: all 0.15s ease;
            cursor: pointer;
          }

          #pinmx-floating-calc .pinmx-calc-grid .btn:hover{
            background: #f9fafb;
            border-color: #d1d5db;
            transform: translateY(-1px);
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.08);
          }

          #pinmx-floating-calc .pinmx-calc-grid .btn:active{
            transform: translateY(0);
            box-shadow: 0 1px 2px rgba(0, 0, 0, 0.06);
          }

          #pinmx-floating-calc .pinmx-calc-grid .btn[data-k="C"]{
            background: #fee2e2;
            border-color: #fecaca;
            color: #dc2626;
          }

          #pinmx-floating-calc .pinmx-calc-grid .btn[data-k="C"]:hover{
            background: #fecaca;
            border-color: #fca5a5;
          }

          #pinmx-floating-calc .pinmx-calc-grid .btn[data-k="BS"]{
            background: #fef3c7;
            border-color: #fde68a;
            color: #d97706;
          }

          #pinmx-floating-calc .pinmx-calc-grid .btn[data-k="BS"]:hover{
            background: #fde68a;
            border-color: #fcd34d;
          }

          #pinmx-floating-calc .pinmx-calc-grid .btn[data-k="+"],
          #pinmx-floating-calc .pinmx-calc-grid .btn[data-k="-"],
          #pinmx-floating-calc .pinmx-calc-grid .btn[data-k="*"],
          #pinmx-floating-calc .pinmx-calc-grid .btn[data-k="/"]{
            background: #ede9fe;
            border-color: #ddd6fe;
            color: #7c3aed;
          }

          #pinmx-floating-calc .pinmx-calc-grid .btn[data-k="("],
          #pinmx-floating-calc .pinmx-calc-grid .btn[data-k=")"]{
            background: #e0f2fe;
            border-color: #bae6fd;
            color: #0369a1;
          }

          #pinmx-floating-calc .pinmx-calc-grid .btn.btn-primary{
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            border-color: #667eea;
            color: #ffffff;
            box-shadow: 0 4px 12px rgba(102, 126, 234, 0.35);
          }

          #pinmx-floating-calc.pinmx-minimized .pinmx-calc-body{ display:none; }
        </style>
      `;

      document.body.appendChild(el);

      const input = el.querySelector(".pinmx-calc-input");
      const result = el.querySelector(".pinmx-calc-result");

      function sanitize(expr) {
        return (expr || "").replace(/[^0-9+\-*/().\s]/g, "");
      }

      function normalizeExpression(expr) {
        let s = (expr || "").replace(/,/g, "");
        s = sanitize(s);
        s = s.replace(/(^|[^\d.])0+(\d+)/g, (m, p1, p2) => `${p1}${p2}`);
        return s;
      }

      function formatWithCommas(val) {
        const s = String(val);
        if (!s || s === "…" || s === "NaN" || s === "Infinity" || s === "-Infinity") return s;

        const neg = s.startsWith("-");
        const clean = neg ? s.slice(1) : s;

        const parts = clean.split(".");
        const intPart = (parts[0] || "0").replace(/\B(?=(\d{3})+(?!\d))/g, ",");
        const decPart = parts.length > 1 ? "." + parts[1] : "";
        return (neg ? "-" : "") + intPart + decPart;
      }

      function stripLeadingZerosInToken(token) {
        if (token.includes(".")) {
          const [a, b] = token.split(".");
          const left = a.replace(/^0+(?=\d)/, "");
          return (left === "" ? "0" : left) + "." + b;
        }
        return token.replace(/^0+(?=\d)/, "");
      }

      function formatExpressionWithCommas(expr) {
        if (!expr) return "";
        let clean = (expr || "").replace(/,/g, "");
        clean = sanitize(clean);

        return clean.replace(/(\d+(?:\.\d+)?)/g, (match) => {
          const fixed = stripLeadingZerosInToken(match);
          const parts = fixed.split(".");
          const intPart = (parts[0] || "0").replace(/\B(?=(\d{3})+(?!\d))/g, ",");
          const decPart = parts.length > 1 ? "." + parts[1] : "";
          return intPart + decPart;
        });
      }

      function evalSafe(expr) {
        const safe = normalizeExpression(expr);
        if (!safe.trim()) return "";
        // eslint-disable-next-line no-new-func
        return Function(`"use strict"; return (${safe})`)();
      }

      function updateResult() {
        try {
          let v = input.value || "";
          if (!v.trim()) {
            result.value = "";
            return;
          }

          const cursorPos = input.selectionStart ?? v.length;
          const beforeCursor = v.substring(0, cursorPos);

          const formatted = formatExpressionWithCommas(v);
          if (formatted !== v) {
            input.value = formatted;

            const beforeFormatted = formatExpressionWithCommas(beforeCursor);
            const commaDiff = beforeFormatted.length - beforeCursor.length;
            let newCursorPos = cursorPos + commaDiff;

            newCursorPos = Math.min(newCursorPos, formatted.length);
            input.setSelectionRange(newCursorPos, newCursorPos);

            v = formatted;
          }

          const raw = v.replace(/,/g, "");
          const out = evalSafe(raw);

          if (out === undefined || out === null || Number.isNaN(out)) {
            result.value = "";
            return;
          }
          result.value = formatWithCommas(out);
        } catch (e) {
          result.value = "…";
        }
      }

      function press(key) {
        let v = input.value || "";

        if (key === "C") {
          input.value = "";
          result.value = "";
          return;
        }
        if (key === "BS") {
          let raw = v.replace(/,/g, "");
          raw = raw.slice(0, -1);
          input.value = formatExpressionWithCommas(raw);
          updateResult();
          setTimeout(() => input.setSelectionRange(input.value.length, input.value.length), 0);
          return;
        }
        if (key === "=") {
          updateResult();
          if (result.value && result.value !== "…") {
            input.value = String(result.value); // keep commas
            updateResult();
            setTimeout(() => input.setSelectionRange(input.value.length, input.value.length), 0);
          }
          return;
        }

        let raw = v.replace(/,/g, "");
        raw = raw + key;
        input.value = formatExpressionWithCommas(raw);
        updateResult();
        setTimeout(() => input.setSelectionRange(input.value.length, input.value.length), 0);
      }

      el.querySelectorAll(".pinmx-calc-grid button").forEach((btn) => {
        btn.addEventListener("click", () => {
          press(btn.getAttribute("data-k"));
          input.focus();
        });
      });

      el.querySelector(".pinmx-close").addEventListener("click", () => el.remove());
      el.querySelector(".pinmx-min").addEventListener("click", () => {
        el.classList.toggle("pinmx-minimized");
      });

      input.addEventListener("input", updateResult);
      input.addEventListener("keydown", (e) => {
        if (e.key === "Enter") { e.preventDefault(); press("="); }
        if (e.key === "Escape") { e.preventDefault(); el.remove(); }
      });

      // Dragging (header)
      const header = el.querySelector(".pinmx-calc-header");
      let dragging = false, startX = 0, startY = 0, startLeft = 0, startTop = 0;

      header.addEventListener("mousedown", (e) => {
        dragging = true;
        const rect = el.getBoundingClientRect();
        el.style.right = "auto";
        startLeft = rect.left;
        startTop = rect.top;
        startX = e.clientX;
        startY = e.clientY;
        el.style.left = `${startLeft}px`;
        el.style.top = `${startTop}px`;
      });

      document.addEventListener("mousemove", (e) => {
        if (!dragging) return;
        el.style.left = `${startLeft + (e.clientX - startX)}px`;
        el.style.top  = `${startTop + (e.clientY - startY)}px`;
      });

      document.addEventListener("mouseup", () => { dragging = false; });

      setTimeout(() => input.focus(), 50);
    }
  };
})();

```
</details>


> **Note:**  
> Create Calculator on Entry Screen in ERPNext.

---








##### Contact
<p align="center">
  <img src="https://github.com/ERPNEXT-PAKISTAN.png" width="96" height="96" alt="Taimoor"
       style="border-radius:50%; object-fit:cover; display:block; border:3px solid rgba(255,255,255,0.95); box-shadow:0 6px 18px rgba(0,0,0,0.25);" />
</p>

<h2 align="center">Taimoor</h2>
<p align="center"><em>ERPNext | Integrations & Automation</em></p>

<p align="center">
  <!-- Phone / WhatsApp -->
  <a href="https://wa.me/923009808900" title="WhatsApp" target="_blank" rel="noopener noreferrer">
    <img src="https://img.icons8.com/fluency/48/whatsapp.png" width="28" alt="WhatsApp"/> 
  </a>
  &nbsp;
  <a href="tel:+923009808900" title="Call">
    <img src="https://img.icons8.com/fluency/48/phone.png" width="28" alt="Phone"/>
  </a>
  &nbsp;&nbsp;
  <!-- Email -->
  <a href="mailto:taimoor986@gmail.com" title="Email">
    <img src="https://img.icons8.com/fluency/48/new-post.png" width="28" alt="Email"/>
  </a>
  &nbsp;&nbsp;
  <!-- LinkedIn -->
  <a href="https://www.linkedin.com/in/taimoor-khan-45b548137" title="LinkedIn" target="_blank" rel="noopener noreferrer">
    <img src="https://img.icons8.com/fluency/48/linkedin.png" width="28" alt="LinkedIn"/>
  </a>
  &nbsp;&nbsp;
  <!-- GitHub -->
  <a href="https://github.com/ERPNEXT-PAKISTAN" title="GitHub" target="_blank" rel="noopener noreferrer">
    <img src="https://img.icons8.com/fluency/48/github.png" width="28" alt="GitHub"/>
  </a>
  &nbsp;&nbsp;
  <!-- YouTube -->
  <a href="https://www.youtube.com/@ERPNEXT-PAKISTAN" title="YouTube" target="_blank" rel="noopener noreferrer">
    <img src="https://img.icons8.com/fluency/48/youtube-play.png" width="28" alt="YouTube"/>
  </a>
</p>


<p align="center">
  <b>Contact</b><br>
  📞 +92 300 9808900 &nbsp;|&nbsp; ✉️ <a href="mailto:taimoor986@gmail.com">taimoor986@gmail.com</a>
</p>

<p align="center">
  <a href="https://www.linkedin.com/in/taimoor-khan-45b548137" target="_blank">LinkedIn</a> • 
  <a href="https://github.com/ERPNEXT-PAKISTAN" target="_blank">GitHub</a> • 
  <a href="https://www.youtube.com/@ERPNEXT-PAKISTAN" target="_blank">YouTube</a> • 
  <a href="https://wa.me/923009808900" target="_blank">WhatsApp</a>
</p>

---

> ✨ **Tip:**  
> You can copy, share, or export this contact card using the icons above.
>
> <small>
> - Click the phone/WhatsApp icon to message or call.  
> - Click the mail icon to open your email client.  
> - Use the social icons to visit profiles and channels.
> </small>

<details>
  <summary><img src="https://img.icons8.com/fluency/48/copy.png" width="18" alt="Copy"/> Copy vCard</summary>

```vcf
BEGIN:VCARD
VERSION:3.0
FN:Taimoor
TEL;TYPE=CELL:+923009808900
EMAIL:taimoor986@gmail.com
URL;TYPE=LinkedIn:https://www.linkedin.com/in/taimoor-khan-45b548137
URL;TYPE=GitHub:https://github.com/ERPNEXT-PAKISTAN
URL;TYPE=YouTube:https://www.youtube.com/@ERPNEXT-PAKISTAN
END:VCARD
```
</details>

---

<!-- Action buttons (links) -->
<p align="center">
  <a href="#reports" style="text-decoration:none">
    <img src="https://img.shields.io/badge/View%20Report-➡️-0892ff?style=for-the-badge" alt="view report"/>
  </a>
  &nbsp;
  <a href="#export" style="text-decoration:none">
    <img src="https://img.shields.io/badge/Download%20PDF-⬇️-00aaff?style=for-the-badge" alt="download pdf"/>
  </a>
  &nbsp;
  <a href="#scripts" style="text-decoration:none">
    <img src="https://img.shields.io/badge/Server%20Scripts-⚙️-06b6d4?style=for-the-badge" alt="server scripts"/>
  </a>
</p>
