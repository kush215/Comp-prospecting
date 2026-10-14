# Extracting a Sales Navigator results page

The results list is virtualized: rows render only when near the viewport, so one JS read won't get all 25.

1. Navigate the rep's Sales Nav tab to the search URL with `&page=N`. Wait ~3s.
2. Define a collector in the page (javascript tool):

```js
window.__leads = {};
window.__grab = () => {
  document.querySelectorAll('li.artdeco-list__item').forEach(li => {
    const a = li.querySelector('a[href*="/sales/lead/"]');
    if (!a) return;
    const id = a.getAttribute('href').split('/sales/lead/')[1].split(',')[0];
    const q = s => { const el = li.querySelector(`[data-anonymize="${s}"]`); return el && el.innerText.trim(); };
    window.__leads[id] = { n: q('person-name'), t: q('title'), c: q('company-name') };
  });
  return Object.keys(window.__leads).length;
};
__grab();
```

3. Use **real** mouse-wheel scrolls (computer `scroll` at the results column, e.g. x≈1000, y≈450): 5 ticks, then 10 ticks × 4, waiting ~1s and calling `__grab()` after each. Batch these in one `browser_batch` when available.
4. If fewer than 25, find missing row indexes:
   ```js
   [...document.querySelectorAll('li.artdeco-list__item')].map((li,i)=>[i,!!li.querySelector('a[href*="/sales/lead/"]')]).filter(x=>!x[1]).map(x=>x[0])
   ```
   For each, `scrollIntoView()` that row, do a 1-tick real scroll, wait 1s, `__grab()`.
5. Print in slices of ~12 rows (tool output truncates around 1,000 chars), truncating title/company to ~25 chars.

Do not click any lead, Save, or Connect buttons. Leave the tab open on the page for the next run.
