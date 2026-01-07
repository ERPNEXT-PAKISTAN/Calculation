<p align="center">
  <img src="https://raw.githubusercontent.com/ERPNEXT-PAKISTAN/<REPO>/main/assets/calculation-banner.png" alt="Calculation Suite Banner" width="100%" />
</p>

<h1 align="center">Hi 👋, Welcome to Calculation Suite — Calculator & Math Utilities for ERPNext</h1>
<p align="center">A lightweight, extensible calculation engine for ERPNext: calculators, formula fields, bulk computations, unit & currency conversions, taxes, discounts and business math utilities.</p>

<p align="center">
  <a href="https://github.com/ERPNEXT-PAKISTAN">
    <img width="225" height="225" alt="Calculator Icon" src="https://img.icons8.com/fluent/240/calculator.png" />
  </a>
</p>

<p align="center">
  <!-- Social badges -->
  <a href="https://www.youtube.com/@ERPNEXT-PAKISTAN">
    <img src="https://img.shields.io/badge/YouTube-Subscribe-FF0000?style=flat-square&logo=youtube&logoColor=white" alt="YouTube">
  </a>
  <a href="mailto:taimoor986@gmail.com">
    <img src="https://img.shields.io/badge/Email-taimoor986%40Gmail.com-EA4335?style=flat-square&logo=gmail&logoColor=white" alt="email">
  </a>
  <a href="https://www.linkedin.com/in/taimoor-khan-45b548137/">
    <img src="https://img.shields.io/badge/LinkedIn-Taimoor-0A66C2?style=flat-square&logo=linkedin&logoColor=white" alt="linkedin">
  </a>
  <a href="https://github.com/ERPNEXT-PAKISTAN">
    <img src="https://img.shields.io/badge/GitHub-ERPNEXT--PAKISTAN-181717?style=flat-square&logo=github&logoColor=white" alt="GitHub">
  </a>
</p>

---

## About
- 🔢 Purpose: Provide reusable calculation building blocks inside ERPNext — from simple calculators to complex, stored formulas used in DocTypes and reports.
- 🎯 Use cases:
  - Price, discount, and tax calculations
  - Commission and margin calculators
  - Unit & currency conversions
  - Saved/formula fields for documents (Invoice, Sales Order, Project)
  - Bulk recalculation jobs and audit history

---

## Key Features
- ⚙️ Flexible Formula Engine  
  - Store formulas as expressions (example: `(price - discount) * qty + tax`) and evaluate them safely on server or client-side.
- 📄 Calculator Doctype  
  - Create reusable calculators with input definitions, precision, default units and result metadata.
- 🧾 Auto-fields & Doc Integration  
  - Map calculation results to ERPNext DocType fields (Invoice, Item, Opportunity).
- 🔁 Bulk & Scheduled Recalculations  
  - Recompute historical documents after rate/logic changes.
- 🔒 Safe Evaluation  
  - Expression sandboxing to avoid executing arbitrary code.
- 🌐 Unit & Currency Conversion  
  - Built-in conversion utilities with configurable rates.
- 🧾 History & Auditing  
  - Store calculation inputs, outputs and timestamps for traceability.

---

## How It Works
1. Add the Calculation Suite app to your bench and install it on your site.
2. Create a Calculator record:
   - Define input fields (name, type, unit), formula expression, precision and output mapping.
3. Use calculator in DocType:
   - Call from client script (frappe.call) or server (Python) to compute results.
4. For bulk updates, schedule a background job that runs calculators over documents and stores results or updates fields.
5. Audit entries are logged each time a calculation runs for traceability.

---

## Quick Setup (example)
- Install app (example commands):
```bash
# clone to bench apps
bench get-app https://github.com/ERPNEXT-PAKISTAN/<REPO>
bench --site your-site install-app <app-name>
bench restart
```
- Create Calculator in ERPNext > Tools > Calculations (or Module you choose).
- Map calculators in DocType customizations or call via API.

---

## Example: Simple Discount Calculator
- Inputs:
  - `price` (Float)
  - `discount_percent` (Float)
  - `qty` (Int)
- Formula:
  - `(price * (1 - discount_percent / 100)) * qty`
- Example usage (client-side pseudocode):
```js
frappe.call({
  method: "your_app.calculations.evaluate",
  args: {
    formula: "(price * (1 - discount_percent / 100)) * qty",
    inputs: { price: 100, discount_percent: 10, qty: 2 },
    precision: 2
  },
  callback: (r) => {
    if (r.message) {
      console.log("Result:", r.message.result); // 180.00
    }
  }
});
```

---

## Example Fields (Calculator Doctype)
- `Calculator Name` — Data
- `Inputs` — Table of input definitions (label, key, type, default, unit)
- `Formula` — Small Text (expression)
- `Precision` — Int
- `Output Field` — Link to DocType field to update (optional)
- `Last Run` — Datetime
- `Active` — Checkbox

---

## API / Server Usage (Python)
```python
from your_app.calculations import evaluate_formula

result = evaluate_formula(
    formula="(price - discount) * qty + tax",
    inputs={"price": 120.0, "discount": 10.0, "qty": 3, "tax": 5.0},
    precision=2
)
# result == 365.00
```

---

## Tech / Tools
<p>
  <img src="https://img.shields.io/badge/ERPNext-%23007ACC.svg?style=flat-square&logo=erpnext&logoColor=white" alt="ERPNext" />
  <img src="https://img.shields.io/badge/Frappe-%23007ACC.svg?style=flat-square&logo=frappe&logoColor=white" alt="Frappe" />
  <img src="https://img.shields.io/badge/Python-%233776AB.svg?style=flat-square&logo=python&logoColor=white" alt="Python" />
  <img src="https://img.shields.io/badge/JavaScript-%23F7DF1E.svg?style=flat-square&logo=javascript&logoColor=black" alt="JavaScript" />
  <img src="https://img.shields.io/badge/MathJS-%23000000.svg?style=flat-square&logo=mathdotjs&logoColor=white" alt="MathJS (optional)" />
</p>

---

## Usage Tips & Best Practices
- Keep formulas readable: prefer named inputs instead of positional arguments.
- Use precision and rounding consistently to avoid floating-point surprises.
- Protect sensitive operations — never allow user-provided code to execute directly.
- Use versioned calculators if business logic changes (so older docs keep original results).

---

## Contributing
- Found a bug or want a feature? Open an issue or submit a PR to the repository.
- Please include:
  - steps to reproduce
  - sample data
  - expected vs actual behavior

---

## Contact
<p align="center">
  <img src="https://github.com/ERPNEXT-PAKISTAN.png" width="96" height="96" alt="ERPNEXT-PAKISTAN"
       style="border-radius:50%; object-fit:cover; display:block; border:3px solid rgba(255,255,255,0.95); box-shadow:0 6px 18px rgba(0,0,0,0.25);" />
</p>

<h2 align="center">ERPNEXT-PAKISTAN</h2>
<p align="center"><em>Your guide to ERPNext integrations and business automation</em></p>

<p align="center">
  <a href="https://wa.me/923009808900" title="WhatsApp" target="_blank" rel="noopener noreferrer">
    <img src="https://img.icons8.com/fluency/48/whatsapp.png" width="28" alt="WhatsApp"/> 
  </a>
  &nbsp;
  <a href="tel:+923009808900" title="Call">
    <img src="https://img.icons8.com/fluency/48/phone.png" width="28" alt="Phone"/>
  </a>
  &nbsp;&nbsp;
  <a href="mailto:taimoor986@gmail.com" title="Email">
    <img src="https://img.icons8.com/fluency/48/new-post.png" width="28" alt="Email"/>
  </a>
  &nbsp;&nbsp;
  <a href="https://www.linkedin.com/in/taimoor-khan-45b548137" title="LinkedIn" target="_blank" rel="noopener noreferrer">
    <img src="https://img.icons8.com/fluency/48/linkedin.png" width="28" alt="LinkedIn"/>
  </a>
  &nbsp;&nbsp;
  <a href="https://github.com/ERPNEXT-PAKISTAN" title="GitHub" target="_blank" rel="noopener noreferrer">
    <img src="https://img.icons8.com/fluency/48/github.png" width="28" alt="GitHub"/>
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

<p align="center">
  Made with ❤️ · <sub>ERPNEXT-PAKISTAN</sub>
</p>
