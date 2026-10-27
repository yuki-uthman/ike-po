# Ike PO

A single static page showing **how many cases of CV Duta stock MRH Investment
has sold so far** — one big number, a bar per day, and every product ranked.
Same look as [ike-sales](https://github.com/yuki-uthman/ike-sales). Static
only: no secret, no cron, no build step.

## The unit is the case

Every CV Duta product exists in Odoo up to three ways: a loose **pcs**, a
**bundle** (for the wafer and biscuit lines), and a **case** kit. This page
counts only the case product, in the case unit. A piece or a bundle sold on its
own is **not** a case and is left out; so are draft and sent quotations (shown
instead as a quiet "N more cases quoted, not confirmed yet" line).

What counts as sold: confirmed sale orders, plus paid POS lines that don't sit
behind a sale order, minus credit notes against sale-order lines. The
products are the 30 rows of `Indonesia CV Duta 07-2026 Price List v1`
(every product linked to the vendor *CV Duta* in the case unit), so a product
nobody has bought yet still appears, under **Not sold yet**.

Days are Maldives days (UTC+5). Tap a bar to see only that day's products;
tap it again, **All days ✕**, or press Esc to go back.

## One-time setup

**Enable GitHub Pages**: Settings → Pages → Source: "Deploy from a branch" →
Branch: `main`, folder `/ (root)`. The page will be at
`https://<your-username>.github.io/ike-po/`.

Nothing else is configured here. The numbers come from
[ike-data](https://github.com/yuki-uthman/ike-data), which owns the Odoo
credential.

## How it gets its data

`index.html` fetches
`https://raw.githubusercontent.com/yuki-uthman/ike-data/main/data/duta-cases.json`
in the browser on load, every 60 seconds, and whenever the tab becomes visible.
That file is rewritten every 15 minutes by `ike-data/scripts/fetch_duta_cases.py`
(its own step in the shared refresh workflow, allowed to fail without stopping
the other dashboards' data). The counting rules are tested offline by
`ike-data/scripts/test_duta_cases.py`.

To try the page against a local file, put a copy of `duta-cases.json` beside
`index.html`, serve the folder, and open `index.html?data=duta-cases.json`.

## Profit tabs

Beside **Sales** there is one **Profit** tab per container (Profit 1, Profit 2 …;
`#ship1`, `#ship2` in the URL). Each lists every product on that container with
its profit if all the cases sell, and a **Total profit** at the bottom:

    profit = cases in the container x (net price - landed cost)

Net price is before GST: what was actually charged for a product that has sold,
the list price for one that has not (italic note on the row). Landed cost is the
product's cost in Odoo. A product priced below cost shows a **negative** profit
in red, and the total includes it. (The % beside products on the Sales tab is
floored at 0%; these money figures are not, or the total would be overstated.)

Containers come from `shipments` in `duta-cases.json`: each confirmed CV Duta
purchase order in Odoo, plus the **proforma** container kept by hand in
`ike-data/shipments/next-container.json` until its order exists in Odoo. A
proforma container assumes the same prices, freight and markup as before, so each
product costs what Odoo says today. The exception is a product whose invoice price
changed (the two Maitos): its cost is the invoice USD price x 21.61 x 1.35, the same
conversion and landed markup as the first container. Selling prices are unchanged.

## Visibility

GitHub Pages on a free plan needs a **public** repository, so the page is
reachable by anyone with the URL. It shows product names, case counts and, on
the Profit tabs, **landed costs, selling prices and profit amounts**. That was
the owner's decision. Customer names are never shown. Do not commit the
supplier's proforma invoice spreadsheet (prices, bank details) to this repo.
