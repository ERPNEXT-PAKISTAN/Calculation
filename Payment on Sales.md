<p align="center">
  <img width="420" height="120" alt="Dashboard Banner" src="https://github.com/user-attachments/assets/b6b12445-4a2b-4f34-92c4-7819e5c491cc" />
</p>

<h1 align="center">
  <img src="https://img.icons8.com/fluency/48/combo-chart.png" width="36" alt="analytics"/> 
  <span style="vertical-align:middle"> Template </span>
  <img src="https://img.icons8.com/fluency/48/speed.png" width="36" alt="performance"/>
</h1>


<!-- KPI badges (uniform) -->
<p align="center">
  <img src="https://img.shields.io/badge/Revenue-%24125k-0ea5e9?style=for-the-badge&logo=google" alt="Revenue" />
  <img src="https://img.shields.io/badge/Orders-3.2k-0892ff?style=for-the-badge" alt="Orders" />
  <img src="https://img.shields.io/badge/Avg%20Order%20Value-%2459-06b6d4?style=for-the-badge" alt="AOV" />
  <img src="https://img.shields.io/badge/Conversion-2.8%25-00c853?style=for-the-badge" alt="Conversion" />
</p>

<p align="center">
  <b>Analyze, View & Export Your Reports Easily</b>
</p>

<!-- Small visual row -->
<p align="center">
  <img src="https://img.icons8.com/fluency/48/combo-chart--v1.png" width="32" alt="combo chart"/> &nbsp;
  <img src="https://img.icons8.com/fluency/48/pie-chart.png" width="32" alt="pie chart"/> &nbsp;
  <img src="https://img.icons8.com/fluency/48/bar-chart.png" width="32" alt="bar chart"/> &nbsp;
  <img src="https://img.icons8.com/fluency/48/data-in-both-directions.png" width="32" alt="data flow"/> &nbsp;
  <img src="https://img.icons8.com/color/48/microsoft-excel-2019--v1.png" width="32" alt="excel"/> &nbsp;
  <img src="https://img.icons8.com/fluency/48/pdf.png" width="32" alt="pdf"/> &nbsp;
  <img src="https://img.icons8.com/fluency/48/print.png" width="32" alt="print"/>
</p>


---


## <img src="https://img.icons8.com/color/48/000000/javascript--v1.png" width="35"/> JavaScript Code

<details>
<summary><img src="https://img.icons8.com/color/48/000000/code-file.png" width="22"/> Copy JavaScript</summary>

```javascript
frappe.ui.form.on("Sales Invoice", {
  refresh(frm) {
    // ---- SETTINGS ----
    // Put your real role(s) here (must match exactly)
    const allowed_roles = ["Accounts User", "Accounts Manager"];

    const can_pay = allowed_roles.some(r => frappe.user.has_role(r));
    const is_submitted = frm.doc.docstatus === 1;
    const has_outstanding = flt(frm.doc.outstanding_amount) > 0;

    // Hide standard "Create -> Payment Entry" (best-effort)
    setTimeout(() => {
      try { frm.page.remove_inner_button(__("Payment Entry"), __("Create")); } catch (e) {}
      try { frm.page.remove_inner_button(__("Payment"), __("Create")); } catch (e) {}
    }, 0);

    if (!can_pay || !is_submitted || !has_outstanding) return;

    // Visible button (not inside dropdown)
    frm.add_custom_button(__("Pay & Submit"), () => open_pay_dialog(frm));
  }
});

// ---------------- UI: Dialog ----------------

function open_pay_dialog(frm) {
  const d = new frappe.ui.Dialog({
    title: __("Take Payment"),
    size: "large",
    fields: [
      { fieldname: "header_html", fieldtype: "HTML" },

      { fieldtype: "Section Break", label: __("Payment") },
      {
        fieldname: "posting_date",
        fieldtype: "Date",
        label: __("Posting Date"),
        reqd: 1,
        default: frappe.datetime.get_today()
      },
      {
        fieldname: "mode_of_payment",
        fieldtype: "Link",
        options: "Mode of Payment",
        label: __("Mode of Payment"),
        reqd: 1
      },

      { fieldtype: "Column Break" },
      {
        fieldname: "paid_amount",
        fieldtype: "Currency",
        label: __("Paid Amount"),
        reqd: 1,
        default: flt(frm.doc.outstanding_amount)
      },
      { fieldname: "remarks", fieldtype: "Small Text", label: __("Remarks") },

      { fieldtype: "Section Break", label: __("Reference") },
      { fieldname: "reference_no", fieldtype: "Data", label: __("Reference No") },
      { fieldname: "reference_date", fieldtype: "Date", label: __("Reference Date") },

      { fieldtype: "Section Break", label: __("Previous Receipts") },
      { fieldname: "previous_html", fieldtype: "HTML" }
    ],
    primary_action_label: __("Pay & Submit"),
    primary_action: async (values) => {
      try {
        d.disable_primary_action();
        await create_and_submit_payment_entry(frm, values);
        d.hide();
      } catch (err) {
        console.error(err);
        frappe.msgprint({
          title: __("Payment Failed"),
          indicator: "red",
          message: `<div style="line-height:1.6">
            <div style="font-size:16px">❌ <b>${frappe.utils.escape_html(err?.message || err)}</b></div>
            <div style="color:#6b7280;margin-top:6px">Please check Mode of Payment accounts and permissions.</div>
          </div>`
        });
      } finally {
        d.enable_primary_action();
      }
    }
  });

  // Pretty header
  const currency = frm.doc.currency || "";
  d.fields_dict.header_html.$wrapper.html(`
    <style>
      /* Hover animation for previous receipts cards */
      .tc-pe-card { transition: transform .15s ease, box-shadow .15s ease, border-color .15s ease; }
      .tc-pe-card:hover { transform: translateY(-2px); box-shadow: 0 10px 22px rgba(0,0,0,.08); border-color: #c7d2fe; }
      .tc-badge { display:inline-flex; align-items:center; gap:6px; padding:2px 8px; border-radius:999px; font-size:11px; font-weight:800; border:1px solid transparent; }
      .tc-badge.submitted { background:#ecfdf5; color:#065f46; border-color:#bbf7d0; }
      .tc-badge.cancelled { background:#fef2f2; color:#991b1b; border-color:#fecaca; }
      .tc-btn-link { display:inline-flex; align-items:center; gap:6px; font-size:11px; padding:4px 8px; border-radius:10px;
                     background:#eef2ff; color:#4338ca; text-decoration:none; border:1px solid #c7d2fe; }
      .tc-btn-link:hover { filter: brightness(0.98); }
    </style>

    <div style="display:flex;gap:12px;align-items:center;padding:10px 12px;border-radius:12px;background:#f6f8ff;border:1px solid #e6eaff;margin-bottom:10px">
      <div style="width:40px;height:40px;border-radius:12px;display:flex;align-items:center;justify-content:center;background:#4f46e5;color:white;font-size:20px">₹</div>
      <div style="flex:1">
        <div style="font-size:16px;font-weight:700">POS Payment</div>
        <div style="color:#6b7280;font-size:12px;margin-top:2px">
          Invoice: <b>${frm.doc.name}</b> • Customer: <b>${frappe.utils.escape_html(frm.doc.customer_name || frm.doc.customer || "")}</b>
        </div>
      </div>
      <div style="text-align:right">
        <div style="color:#6b7280;font-size:12px">Outstanding</div>
        <div style="font-size:16px;font-weight:800">${format_currency(flt(frm.doc.outstanding_amount), currency)}</div>
      </div>
    </div>
  `);

  d.show();
  load_previous_receipts(frm, d);
}

// ---------------- Previous receipts ----------------
// NOTE: We avoid querying "Payment Entry Reference" directly (permission issue).
// We list recent Payment Entries for the customer, then read each PE (if allowed)
// and keep only those that reference this Sales Invoice.

async function load_previous_receipts(frm, d) {
  const wrap = d.fields_dict.previous_html.$wrapper;
  wrap.html(`<div style="color:#6b7280">Loading previous receipts…</div>`);

  try {
    // Fetch recent Payment Entries for this customer (include cancelled too)
    const pes = await frappe.call({
      method: "frappe.client.get_list",
      args: {
        doctype: "Payment Entry",
        fields: ["name", "docstatus", "posting_date", "paid_amount", "mode_of_payment"],
        filters: {
          party_type: "Customer",
          party: frm.doc.customer
        },
        order_by: "posting_date desc",
        limit_page_length: 25
      }
    });

    const list = pes.message || [];
    if (!list.length) {
      wrap.html(`<div style="color:#6b7280">No previous receipts found.</div>`);
      return;
    }

    const matches = [];
    for (const pe of list) {
      try {
        const full = await frappe.call({
          method: "frappe.client.get",
          args: { doctype: "Payment Entry", name: pe.name }
        });

        const doc = full.message;
        const refs = doc.references || [];
        const ref_row = refs.find(r =>
          r.reference_doctype === "Sales Invoice" && r.reference_name === frm.doc.name
        );

        if (ref_row) {
          matches.push({
            name: doc.name,
            posting_date: doc.posting_date,
            mode_of_payment: doc.mode_of_payment,
            allocated_amount: flt(ref_row.allocated_amount),
            paid_amount: flt(doc.paid_amount),
            docstatus: doc.docstatus
          });
        }
      } catch (e) {
        // ignore docs you can't read
      }
    }

    if (!matches.length) {
      wrap.html(`<div style="color:#6b7280">No receipts linked to this invoice were found (or permission restricted).</div>`);
      return;
    }

    // Newest first (posting_date desc)
    matches.sort((a, b) => (a.posting_date < b.posting_date ? 1 : -1));

    render_previous_receipts(frm, wrap, matches);

  } catch (e) {
    console.error(e);
    wrap.html(`<div style="color:#b91c1c">Could not load previous receipts (permission restricted).</div>`);
  }
}

function render_previous_receipts(frm, wrap, matches) {
  const currency = frm.doc.currency || "";

  const html = matches.map(x => {
    const pe_link = `/app/payment-entry/${x.name}`;
    // Ledger URL: ERPNext General Ledger report with voucher filter (common pattern)
    // If your site uses different report route/params, tell me and I’ll adjust.
    const ledger_link = `/app/query-report/General Ledger?voucher_no=${encodeURIComponent(x.name)}`;

    const badge =
      x.docstatus === 1
        ? `<span class="tc-badge submitted">● Submitted</span>`
        : x.docstatus === 2
          ? `<span class="tc-badge cancelled">● Cancelled</span>`
          : `<span class="tc-badge" style="background:#f3f4f6;color:#374151;border-color:#e5e7eb">● Draft</span>`;

    return `
      <div class="tc-pe-card" style="display:flex;justify-content:space-between;gap:12px;padding:12px;border:1px solid #e0e7ff;border-radius:14px;background:#f9faff;margin-bottom:10px">
        <div style="flex:1;min-width:0">
          <div style="display:flex;align-items:center;gap:10px;flex-wrap:wrap">
            <span style="font-size:16px">💳</span>

            <a href="${pe_link}" target="_blank"
               style="font-weight:900;color:#2563eb;text-decoration:none;font-size:14px">
              ${x.name}
            </a>

            ${badge}
          </div>

          <div style="color:#6b7280;font-size:12px;margin-top:4px">
            ${x.posting_date || ""} • ${frappe.utils.escape_html(x.mode_of_payment || "")}
          </div>

          <div style="display:flex;gap:10px;flex-wrap:wrap;margin-top:8px">
            <span style="font-size:12px;padding:4px 10px;border-radius:999px;background:#ffffff;border:1px solid #e5e7eb">
              <span style="color:#6b7280">Paid:</span>
              <b>${format_currency(flt(x.paid_amount), currency)}</b>
            </span>
            <span style="font-size:12px;padding:4px 10px;border-radius:999px;background:#ffffff;border:1px solid #e5e7eb">
              <span style="color:#6b7280">Allocated:</span>
              <b>${format_currency(flt(x.allocated_amount), currency)}</b>
            </span>
          </div>
        </div>

        <div style="text-align:right;display:flex;flex-direction:column;gap:8px;align-items:flex-end;justify-content:center">
          <a class="tc-btn-link" href="${pe_link}" target="_blank">🔗 Open</a>
          <a class="tc-btn-link" href="${ledger_link}" target="_blank">📒 View Ledger</a>
        </div>
      </div>
    `;
  }).join("");

  wrap.html(html);
}

// ---------------- Create + Submit Payment Entry (no open form) ----------------

async function create_and_submit_payment_entry(frm, values) {
  const amt = flt(values.paid_amount);
  if (!amt || amt <= 0) throw new Error(__("Paid Amount must be greater than 0."));
  if (amt > flt(frm.doc.outstanding_amount)) {
    throw new Error(__("Paid Amount cannot be greater than Outstanding Amount."));
  }

  // Bank/Cash account from Mode of Payment mapping (required to submit)
  const bank_cash = await frappe.call({
    method: "erpnext.accounts.doctype.sales_invoice.sales_invoice.get_bank_cash_account",
    args: { mode_of_payment: values.mode_of_payment, company: frm.doc.company },
    freeze: true,
    freeze_message: __("Preparing payment…")
  });

  const paid_to_account = bank_cash?.message?.account;
  if (!paid_to_account) {
    throw new Error(__(
      "No Bank/Cash account found for this Mode of Payment.\nPlease set Mode of Payment Accounts for company {0}.",
      [frm.doc.company]
    ));
  }

  // Build draft PE from SI (v15 safe: do NOT pass party_amount)
  const r = await frappe.call({
    method: "erpnext.accounts.doctype.payment_entry.payment_entry.get_payment_entry",
    args: { dt: "Sales Invoice", dn: frm.doc.name },
    freeze: true,
    freeze_message: __("Creating Payment Entry…")
  });

  if (!r.message) throw new Error(__("Could not create Payment Entry draft."));

  const pe = r.message;

  // Apply values
  pe.posting_date = values.posting_date || frappe.datetime.get_today();
  pe.mode_of_payment = values.mode_of_payment;
  pe.paid_to = paid_to_account;

  pe.paid_amount = amt;
  pe.received_amount = amt;

  if (values.reference_no) pe.reference_no = values.reference_no;
  if (values.reference_date) pe.reference_date = values.reference_date;
  if (values.remarks) pe.remarks = values.remarks;

  if (pe.references && pe.references.length) {
    pe.references[0].allocated_amount = Math.min(amt, flt(frm.doc.outstanding_amount));
  }

  // Insert then Submit
  const inserted = await frappe.call({
    method: "frappe.client.insert",
    args: { doc: pe },
    freeze: true,
    freeze_message: __("Saving Payment Entry…")
  });

  const saved = inserted.message;
  if (!saved?.name) throw new Error(__("Payment Entry was not saved."));

  const submitted = await frappe.call({
    method: "frappe.client.submit",
    args: { doc: saved },
    freeze: true,
    freeze_message: __("Submitting Payment Entry…")
  });

  const submitted_pe = submitted.message;

  // Success message (styled)
  const currency = frm.doc.currency || "";
  const pe_link = `/app/payment-entry/${submitted_pe.name}`;

  frappe.msgprint({
    title: __("Payment Successful"),
    indicator: "green",
    message: `
      <div style="display:flex;gap:12px;align-items:flex-start;line-height:1.6">
        <div style="width:42px;height:42px;border-radius:12px;background:#16a34a;color:white;display:flex;align-items:center;justify-content:center;font-size:22px">✓</div>
        <div style="flex:1">
          <div style="font-size:16px;font-weight:800;margin-bottom:4px">Payment Successful</div>
          <div style="color:#111827">
            Payment Entry
            <b><a href="${pe_link}" target="_blank" style="color:#2563eb;text-decoration:none">${submitted_pe.name}</a></b>
            submitted.
          </div>
          <div style="margin-top:6px;padding:10px 12px;border-radius:12px;background:#f0fdf4;border:1px solid #bbf7d0">
            <div style="display:flex;justify-content:space-between;gap:10px">
              <span style="color:#065f46">Paid Amount</span>
              <b style="color:#065f46">${format_currency(amt, currency)}</b>
            </div>
            <div style="display:flex;justify-content:space-between;gap:10px;margin-top:4px">
              <span style="color:#065f46">Posting Date</span>
              <b style="color:#065f46">${frappe.utils.escape_html(values.posting_date || "")}</b>
            </div>
          </div>
        </div>
      </div>
    `
  });

  // Refresh invoice (updates outstanding/payment status)
  await frm.reload_doc();
  frappe.show_alert({ message: __("Sales Invoice updated."), indicator: "green" }, 5);
}

```
</details>


> **Note:**  
> Copy and use the JavaScript snippet above.

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
