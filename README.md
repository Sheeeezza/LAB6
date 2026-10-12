# CSE325 – Lab 06: Design, Coding, Testing, Reuse & Quality

**Scenario:** A university fee system generates invoices in text and JSON without duplicating fee-calculation logic.
**CLOs:** CLO-2, CLO-5, CLO-6

## Setup / Run / Test
```
python -m venv .venv && .\.venv\Scripts\activate
python -m pip install pytest coverage
python -m activities.activity1_separation   # Activity 1
python -m activities.activity2_serializer   # Activity 2
python -m activities.activity3_tradeoff     # Activity 3
python -m invoice_app.main                  # Activity 4 demo
python -m pytest -q                         # 19 tests
python -m coverage run -m pytest && python -m coverage report
```

## Architecture (Activity 4)
```
main.py (I/O shell: only place that prints)
   |
InvoiceService  --->  calculator.py (pure rules: subtotal, discount, tax)
   |
   +--->  formatters.py (to_text / to_json, reuses stdlib json)
```
Calculation, formatting and I/O are three separate layers; text and JSON both reuse the same `compute_totals`.

## Business rules (tested)
| # | Rule |
|---|------|
| 1 | Discount = subtotal x discount_rate |
| 2 | Tax is charged on the *discounted* subtotal |
| 3 | Rates must be within 0..1 (else `InvalidRateError`) |
| 4 | unit_price >= 0 and qty > 0 (else `InvalidItemError`) |
| 5 | Empty invoice totals 0 |

## Graded Lab Tasks
1. **Notification formatter** – `notify_app/formatter.py`: `EmailFormatter` and `SmsFormatter` share the `NotificationFormatter` interface; `notify()` accepts any formatter.
2. **Tests for >= 4 business rules** – `tests/test_invoice.py` (discount, tax-on-discounted, rate bounds, item validation, empty invoice) and `tests/test_notify.py` (SMS 160-char limit, required fields).
3. **Separate calculation, formatting, I/O** – `calculator.py` / `formatters.py` / `main.py`; in `notify`, I/O is injected via `sender=` so tests capture output instead of printing.
4. **Quality trade-off** – `calculate_total` uses a generator expression with `sum()` rather than a manual loop. Measured: ~0.0084s vs ~0.0088s for 200k items, so the clearer version costs nothing. A hand-optimised version would add complexity for no measured gain, so readability wins.
5. **Reusable standard-library feature** – `json.dumps` (serialisation) and `dataclasses.asdict` / `abc.ABC`, instead of hand-building JSON strings.


## Suggested Git commits
```
git init
git add activities/activity1_separation.py && git commit -m "feat: separate fee calculation from invoice text"
git add activities/activity2_serializer.py  && git commit -m "feat: add JSON invoice using stdlib json"
git add activities/activity3_tradeoff.py    && git commit -m "docs: add quality trade-off benchmark"
git add invoice_app && git commit -m "feat: add InvoiceService with discount and tax"
git add notify_app  && git commit -m "feat: add email/SMS notification formatters"

```
<img width="726" height="226" alt="SR2" src="https://github.com/user-attachments/assets/f2b1e024-648b-488e-85ba-098211783826" />
<img width="892" height="556" alt="SR4" src="https://github.com/user-attachments/assets/71b19ab6-bc9b-4349-aa91-ec08283d4068" />
<img width="832" height="281" alt="sd1" src="https://github.com/user-attachments/assets/833ddd25-e94b-4fa4-806f-b7d444dcf5f5" />




