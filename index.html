/**
 * ═══════════════════════════════════════════════════════════════
 *  RIDE PROFIT TRACKER – SCRIPT.JS
 *  PickMe Bike Rider Financial Tool · Sri Lanka
 * ═══════════════════════════════════════════════════════════════
 *
 *  CONSTANTS
 *  ─────────
 *  COMMISSION_RATE  : 8% taken by PickMe on every trip
 *  COST_PER_KM      : LKR 9 per km (fuel + maintenance estimate)
 *
 *  DATA MODEL (one trip object)
 *  ────────────────────────────
 *  {
 *    id         : unique timestamp string
 *    date       : 'YYYY-MM-DD'
 *    startTime  : 'HH:MM'
 *    endTime    : 'HH:MM'
 *    duration   : minutes (number)
 *    price      : LKR (number)
 *    distance   : KM (number)
 *    commission : LKR (number)
 *    cost       : LKR (number)
 *    profit     : LKR (number)
 *    note       : string
 *  }
 */

'use strict';

/* ──────────────────────────────────────────────────────────────
   CONFIGURATION
────────────────────────────────────────────────────────────── */
const COMMISSION_RATE = 0.08;   // 8%
const COST_PER_KM     = 9;      // LKR per km

/* ──────────────────────────────────────────────────────────────
   STATE
────────────────────────────────────────────────────────────── */
let trips       = [];          // All trips array
let currentPeriod = 'all';     // Filter period
let pendingDeleteId = null;    // For delete confirm modal
let clearPending    = false;   // For clear-all confirm modal
let charts      = {};          // Chart.js instances

/* ──────────────────────────────────────────────────────────────
   DOM REFERENCES
────────────────────────────────────────────────────────────── */
const $ = id => document.getElementById(id);

const tabBtns     = document.querySelectorAll('.tab-btn');
const tabPanels   = document.querySelectorAll('.tab-content');
const filterBtns  = document.querySelectorAll('.filter-btn');

// Form inputs
const inDate      = $('tripDate');
const inPrice     = $('tripPrice');
const inDist      = $('tripDistance');
const inStart     = $('tripStart');
const inEnd       = $('tripEnd');
const inNote      = $('tripNote');

// Preview
const prevPrice   = $('prev-price');
const prevComm    = $('prev-commission');
const prevCost    = $('prev-cost');
const prevProfit  = $('prev-profit');
const prevDur     = $('prev-duration');

// Dashboard stats
const sEarnings   = $('s-earnings');
const sProfit     = $('s-profit');
const sCommission = $('s-commission');
const sCost       = $('s-cost');
const sTrips      = $('s-trips');
const sHours      = $('s-hours');
const sAvgTrip    = $('s-avgtrip');
const sAvgHour    = $('s-avghour');
const todayDetail = $('todayDetail');

// History
const tripTableBody = $('tripTableBody');
const emptyState    = $('emptyState');
const searchInput   = $('searchInput');

// Modal
const modalOverlay  = $('modalOverlay');
const modalTitle    = $('modalTitle');
const modalMsg      = $('modalMsg');
const modalConfirm  = $('modalConfirmBtn');
const modalCancel   = $('modalCancelBtn');

// Toast
const toast = $('toast');

/* ══════════════════════════════════════════════════════════════
   HELPERS
══════════════════════════════════════════════════════════════ */

/** Format LKR number → "LKR 1,234.00" */
const fmtLKR = n =>
  'LKR ' + Number(n).toLocaleString('en-LK', { minimumFractionDigits: 2, maximumFractionDigits: 2 });

/** Format minutes → "1h 23m" */
const fmtMins = m => {
  if (!m && m !== 0) return '—';
  const h = Math.floor(m / 60);
  const min = m % 60;
  if (h === 0) return `${min}m`;
  return `${h}h ${min}m`;
};

/** Today's date as YYYY-MM-DD */
const todayStr = () => new Date().toISOString().split('T')[0];

/** Calculate duration in minutes between two "HH:MM" strings */
const calcDuration = (start, end) => {
  if (!start || !end) return null;
  const [sh, sm] = start.split(':').map(Number);
  const [eh, em] = end.split(':').map(Number);
  let diff = (eh * 60 + em) - (sh * 60 + sm);
  if (diff < 0) diff += 1440; // handles midnight crossover
  return diff;
};

/** Show a brief toast notification */
const showToast = (msg, isError = false) => {
  toast.textContent = msg;
  toast.style.borderColor = isError ? 'var(--red)' : 'var(--accent)';
  toast.classList.add('show');
  setTimeout(() => toast.classList.remove('show'), 2800);
};

/** Show the confirm modal */
const showModal = (title, msg, onConfirm) => {
  modalTitle.textContent = title;
  modalMsg.textContent   = msg;
  modalOverlay.classList.add('open');
  modalConfirm.onclick = () => { closeModal(); onConfirm(); };
};
const closeModal = () => modalOverlay.classList.remove('open');

/* ══════════════════════════════════════════════════════════════
   LOCAL STORAGE
══════════════════════════════════════════════════════════════ */

/** Load trips array from localStorage */
const loadTrips = () => {
  try {
    const raw = localStorage.getItem('rpt_trips');
    trips = raw ? JSON.parse(raw) : [];
  } catch {
    trips = [];
  }
};

/** Save trips array to localStorage */
const saveTrips = () => {
  localStorage.setItem('rpt_trips', JSON.stringify(trips));
};

/* ══════════════════════════════════════════════════════════════
   CALCULATIONS
══════════════════════════════════════════════════════════════ */

/**
 * Calculate derived financial values for a trip.
 * @param {number} price    – gross trip price in LKR
 * @param {number} distance – trip distance in KM
 * @returns {{ commission, cost, profit }}
 */
const calcTrip = (price, distance) => {
  const commission = price * COMMISSION_RATE;          // 8% of price
  const cost       = distance * COST_PER_KM;           // LKR 9/km
  const profit     = price - commission - cost;        // net profit
  return { commission, cost, profit };
};

/* ══════════════════════════════════════════════════════════════
   FILTER HELPERS
══════════════════════════════════════════════════════════════ */

/** Return trips for the currently selected period */
const filteredTrips = () => {
  const today  = todayStr();
  const now    = new Date();

  return trips.filter(t => {
    if (currentPeriod === 'all')   return true;
    if (currentPeriod === 'today') return t.date === today;
    if (currentPeriod === 'week') {
      const d = new Date(t.date);
      const diff = Math.floor((now - d) / 86400000);
      return diff <= 6;
    }
    if (currentPeriod === 'month') {
      const d = new Date(t.date);
      return d.getFullYear() === now.getFullYear() && d.getMonth() === now.getMonth();
    }
    return true;
  });
};

/** Aggregate summary from a trip array */
const aggregate = arr => {
  const totals = arr.reduce((acc, t) => {
    acc.earnings   += t.price;
    acc.commission += t.commission;
    acc.cost       += t.cost;
    acc.profit     += t.profit;
    acc.mins       += (t.duration || 0);
    acc.trips      += 1;
    return acc;
  }, { earnings:0, commission:0, cost:0, profit:0, mins:0, trips:0 });
  return totals;
};

/* ══════════════════════════════════════════════════════════════
   DASHBOARD RENDER
══════════════════════════════════════════════════════════════ */

const renderDashboard = () => {
  const arr    = filteredTrips();
  const totals = aggregate(arr);

  const totalHoursDecimal = totals.mins / 60;

  sEarnings.textContent   = fmtLKR(totals.earnings);
  sProfit.textContent     = fmtLKR(totals.profit);
  sCommission.textContent = fmtLKR(totals.commission);
  sCost.textContent       = fmtLKR(totals.cost);
  sTrips.textContent      = totals.trips;
  sHours.textContent      = fmtMins(totals.mins);

  const avgTrip = totals.trips > 0 ? totals.profit / totals.trips : 0;
  const avgHour = totalHoursDecimal > 0 ? totals.earnings / totalHoursDecimal : 0;

  sAvgTrip.textContent = fmtLKR(avgTrip);
  sAvgHour.textContent = fmtLKR(avgHour);

  // Today quick summary (always shows today, regardless of filter)
  const todayArr     = trips.filter(t => t.date === todayStr());
  const todayTotals  = aggregate(todayArr);
  if (todayArr.length === 0) {
    todayDetail.textContent = 'No trips yet today.';
  } else {
    todayDetail.innerHTML =
      `<span>${todayArr.length} trip${todayArr.length > 1 ? 's' : ''} &nbsp;·&nbsp;</span>` +
      `<span style="color:var(--accent)">Profit: ${fmtLKR(todayTotals.profit)}</span>` +
      `&nbsp;·&nbsp;<span style="color:var(--yellow)">Earnings: ${fmtLKR(todayTotals.earnings)}</span>` +
      `&nbsp;·&nbsp;<span style="color:var(--text-muted)">⏱ ${fmtMins(todayTotals.mins)}</span>`;
  }
};

/* ══════════════════════════════════════════════════════════════
   LIVE PREVIEW (while user types in Add Trip form)
══════════════════════════════════════════════════════════════ */

const updatePreview = () => {
  const price = parseFloat(inPrice.value);
  const dist  = parseFloat(inDist.value);
  const start = inStart.value;
  const end   = inEnd.value;

  const hasPrice = !isNaN(price) && price > 0;
  const hasDist  = !isNaN(dist)  && dist  >= 0;

  if (!hasPrice && !hasDist) {
    prevPrice.textContent  = '—';
    prevComm.textContent   = '—';
    prevCost.textContent   = '—';
    prevProfit.textContent = '—';
    prevDur.textContent    = '—';
    return;
  }

  const p = hasPrice ? price : 0;
  const d = hasDist  ? dist  : 0;
  const { commission, cost, profit } = calcTrip(p, d);
  const dur = calcDuration(start, end);

  prevPrice.textContent  = hasPrice ? fmtLKR(p) : '—';
  prevComm.textContent   = hasPrice ? `- ${fmtLKR(commission)}` : '—';
  prevCost.textContent   = hasDist  ? `- ${fmtLKR(cost)}`       : '—';
  prevProfit.textContent = (hasPrice || hasDist) ? fmtLKR(profit) : '—';
  prevDur.textContent    = dur !== null ? fmtMins(dur) : '—';

  // Colour profit red if negative
  prevProfit.className = profit < 0 ? 'red bold' : 'green bold';
};

/* ══════════════════════════════════════════════════════════════
   ADD TRIP
══════════════════════════════════════════════════════════════ */

const addTrip = () => {
  const fb = $('formFeedback');

  // ── Validation ──
  const price    = parseFloat(inPrice.value);
  const distance = parseFloat(inDist.value);
  const date     = inDate.value;
  const start    = inStart.value;
  const end      = inEnd.value;
  const note     = inNote.value.trim();

  if (!date) {
    fb.textContent = '⚠ Please select a date.';
    fb.className   = 'form-feedback error';
    return;
  }
  if (isNaN(price) || price <= 0) {
    fb.textContent = '⚠ Enter a valid trip price.';
    fb.className   = 'form-feedback error';
    return;
  }
  if (isNaN(distance) || distance < 0) {
    fb.textContent = '⚠ Enter a valid distance.';
    fb.className   = 'form-feedback error';
    return;
  }

  const duration = calcDuration(start, end); // may be null if times not given

  // ── Calculations ──
  const { commission, cost, profit } = calcTrip(price, distance);

  // ── Build trip object ──
  const trip = {
    id:         Date.now().toString(),
    date,
    startTime:  start  || '—',
    endTime:    end    || '—',
    duration:   duration ?? 0,
    price,
    distance,
    commission,
    cost,
    profit,
    note
  };

  trips.unshift(trip);   // add to front (newest first)
  saveTrips();

  // ── Feedback ──
  fb.textContent = `✅ Trip added! Profit: ${fmtLKR(profit)}`;
  fb.className   = 'form-feedback success';
  showToast(`Trip added · ${fmtLKR(profit)} profit`, profit < 0);

  // ── Reset form ──
  inPrice.value    = '';
  inDist.value     = '';
  inStart.value    = '';
  inEnd.value      = '';
  inNote.value     = '';
  updatePreview();

  // ── Refresh UI ──
  renderDashboard();
  renderTable(searchInput.value);
  renderCharts();

  setTimeout(() => { fb.textContent = ''; }, 3000);
};

/* ══════════════════════════════════════════════════════════════
   HISTORY TABLE RENDER
══════════════════════════════════════════════════════════════ */

const renderTable = (query = '') => {
  const q   = query.toLowerCase().trim();
  const arr = trips.filter(t => {
    if (!q) return true;
    return (
      t.date.includes(q)          ||
      (t.note && t.note.toLowerCase().includes(q)) ||
      t.price.toString().includes(q)
    );
  });

  tripTableBody.innerHTML = '';

  if (arr.length === 0) {
    emptyState.classList.add('show');
    return;
  }
  emptyState.classList.remove('show');

  arr.forEach((t, i) => {
    const profitClass = t.profit >= 0 ? 'td-profit' : 'td-red';
    const row = document.createElement('tr');
    row.innerHTML = `
      <td class="td-num">${arr.length - i}</td>
      <td>${t.date}</td>
      <td>${t.startTime}</td>
      <td>${t.endTime}</td>
      <td>${fmtMins(t.duration)}</td>
      <td>${fmtLKR(t.price)}</td>
      <td>${t.distance} km</td>
      <td class="td-red">${fmtLKR(t.commission)}</td>
      <td class="td-red">${fmtLKR(t.cost)}</td>
      <td class="${profitClass}">${fmtLKR(t.profit)}</td>
      <td class="td-note" title="${t.note}">${t.note || '—'}</td>
      <td><button class="btn-del" data-id="${t.id}" title="Delete">🗑</button></td>
    `;
    tripTableBody.appendChild(row);
  });
};

/* ══════════════════════════════════════════════════════════════
   DELETE TRIP
══════════════════════════════════════════════════════════════ */

const deleteTrip = id => {
  trips = trips.filter(t => t.id !== id);
  saveTrips();
  renderDashboard();
  renderTable(searchInput.value);
  renderCharts();
  showToast('Trip deleted.');
};

/* ══════════════════════════════════════════════════════════════
   CHARTS (Chart.js)
══════════════════════════════════════════════════════════════ */

/**
 * Build a map of date → aggregate for the last N days.
 */
const last14DaysMap = () => {
  const map = {};
  for (let i = 13; i >= 0; i--) {
    const d = new Date();
    d.setDate(d.getDate() - i);
    const key = d.toISOString().split('T')[0];
    map[key] = { profit: 0, earnings: 0, trips: 0, mins: 0 };
  }
  trips.forEach(t => {
    if (map[t.date]) {
      map[t.date].profit   += t.profit;
      map[t.date].earnings += t.price;
      map[t.date].trips    += 1;
      map[t.date].mins     += (t.duration || 0);
    }
  });
  return map;
};

/** Destroy an existing chart so we can rebuild it */
const destroyChart = key => {
  if (charts[key]) { charts[key].destroy(); charts[key] = null; }
};

/** Chart colour helpers */
const rgba  = (hex, a) => hex + Math.round(a * 255).toString(16).padStart(2, '0');
const GREEN  = '#00d4aa';
const RED    = '#ff4d6a';
const BLUE   = '#4d8cff';
const YELLOW = '#ffc947';

const renderCharts = () => {
  const map    = last14DaysMap();
  const labels = Object.keys(map).map(d => d.slice(5)); // "MM-DD"
  const profits   = Object.values(map).map(v => v.profit);
  const earnings  = Object.values(map).map(v => v.earnings);
  const tripsArr  = Object.values(map).map(v => v.trips);
  const avgHours  = Object.values(map).map(v =>
    v.mins > 0 ? +(v.earnings / (v.mins / 60)).toFixed(2) : 0
  );

  const gridColor  = 'rgba(255,255,255,0.06)';
  const tickColor  = '#7a82a0';
  const defaults = {
    responsive: true,
    maintainAspectRatio: true,
    plugins: { legend: { display: false } },
    scales: {
      x: { grid: { color: gridColor }, ticks: { color: tickColor, font: { size: 10 } } },
      y: { grid: { color: gridColor }, ticks: { color: tickColor, font: { size: 10 } } }
    }
  };

  /* ─ Chart 1: Daily Profit ─ */
  destroyChart('daily');
  charts.daily = new Chart($('chartDailyProfit'), {
    type: 'bar',
    data: {
      labels,
      datasets: [{
        label: 'Profit (LKR)',
        data: profits,
        backgroundColor: profits.map(v => v >= 0 ? GREEN + '80' : RED + '80'),
        borderColor:     profits.map(v => v >= 0 ? GREEN       : RED),
        borderWidth: 1,
        borderRadius: 4
      }]
    },
    options: { ...defaults }
  });

  /* ─ Chart 2: Earnings Breakdown (Donut) ─ */
  destroyChart('breakdown');
  const totals = aggregate(trips);
  charts.breakdown = new Chart($('chartBreakdown'), {
    type: 'doughnut',
    data: {
      labels: ['Net Profit', 'Commission', 'Fuel & Maint.'],
      datasets: [{
        data: [
          Math.max(0, totals.profit),
          totals.commission,
          totals.cost
        ],
        backgroundColor: [GREEN + 'cc', RED + 'cc', YELLOW + 'cc'],
        borderColor:     [GREEN, RED, YELLOW],
        borderWidth: 1,
        hoverOffset: 6
      }]
    },
    options: {
      responsive: true,
      maintainAspectRatio: true,
      cutout: '65%',
      plugins: {
        legend: {
          display: true,
          position: 'bottom',
          labels: { color: tickColor, font: { size: 11 }, padding: 12 }
        }
      }
    }
  });

  /* ─ Chart 3: Trips Per Day ─ */
  destroyChart('trips');
  charts.trips = new Chart($('chartTripsPerDay'), {
    type: 'line',
    data: {
      labels,
      datasets: [{
        label: 'Trips',
        data: tripsArr,
        borderColor: BLUE,
        backgroundColor: BLUE + '22',
        fill: true,
        tension: 0.4,
        pointRadius: 4,
        pointBackgroundColor: BLUE
      }]
    },
    options: { ...defaults }
  });

  /* ─ Chart 4: Avg Earnings / Hour ─ */
  destroyChart('avghour');
  charts.avghour = new Chart($('chartAvgHour'), {
    type: 'bar',
    data: {
      labels,
      datasets: [{
        label: 'LKR / hr',
        data: avgHours,
        backgroundColor: YELLOW + '80',
        borderColor: YELLOW,
        borderWidth: 1,
        borderRadius: 4
      }]
    },
    options: { ...defaults }
  });
};

/* ══════════════════════════════════════════════════════════════
   EXPORT – PDF
══════════════════════════════════════════════════════════════ */

const exportPDF = () => {
  if (trips.length === 0) { showToast('No trips to export.', true); return; }

  const { jsPDF } = window.jspdf;
  const doc = new jsPDF({ orientation: 'landscape' });

  const totals = aggregate(trips);

  doc.setFontSize(16);
  doc.setTextColor(0, 180, 130);
  doc.text('Ride Profit Tracker – Trip Report', 14, 16);

  doc.setFontSize(10);
  doc.setTextColor(80, 80, 80);
  doc.text(`Generated: ${new Date().toLocaleString('en-LK')}`, 14, 23);
  doc.text(
    `Trips: ${totals.trips}  |  Total Earnings: LKR ${totals.earnings.toFixed(2)}  |  Net Profit: LKR ${totals.profit.toFixed(2)}`,
    14, 30
  );

  // Table header
  const headers = ['#','Date','Start','End','Duration','Price (LKR)','KM','Commission','Cost','Profit','Note'];
  const colW    = [8, 22, 14, 14, 18, 24, 12, 24, 20, 24, 30];

  let y = 38;
  doc.setFillColor(30, 30, 50);
  doc.rect(14, y - 5, 267, 8, 'F');
  doc.setFontSize(8);
  doc.setTextColor(200, 200, 200);

  let x = 14;
  headers.forEach((h, i) => { doc.text(h, x, y); x += colW[i]; });
  y += 6;

  doc.setTextColor(40, 40, 40);
  trips.forEach((t, idx) => {
    if (y > 185) { doc.addPage(); y = 20; }
    const row = [
      String(trips.length - idx),
      t.date, t.startTime, t.endTime, fmtMins(t.duration),
      t.price.toFixed(2), t.distance.toFixed(1),
      t.commission.toFixed(2), t.cost.toFixed(2), t.profit.toFixed(2),
      t.note || ''
    ];
    x = 14;
    if (idx % 2 === 0) {
      doc.setFillColor(245, 247, 255);
      doc.rect(14, y - 5, 267, 7, 'F');
    }
    doc.setFontSize(7.5);
    doc.setTextColor(t.profit >= 0 ? 0 : 200, t.profit >= 0 ? 120 : 0, t.profit >= 0 ? 90 : 0);
    row.forEach((cell, i) => {
      if (i !== 9) doc.setTextColor(50, 50, 80);
      doc.text(String(cell).slice(0, 20), x, y);
      x += colW[i];
    });
    y += 7;
  });

  doc.save(`ride-profit-report-${todayStr()}.pdf`);
  showToast('PDF exported!');
};

/* ══════════════════════════════════════════════════════════════
   EXPORT – EXCEL
══════════════════════════════════════════════════════════════ */

const exportExcel = () => {
  if (trips.length === 0) { showToast('No trips to export.', true); return; }

  const rows = trips.map((t, i) => ({
    '#':           trips.length - i,
    'Date':        t.date,
    'Start Time':  t.startTime,
    'End Time':    t.endTime,
    'Duration':    fmtMins(t.duration),
    'Price (LKR)': t.price,
    'Distance (KM)': t.distance,
    'Commission (LKR)': +t.commission.toFixed(2),
    'Cost (LKR)':   +t.cost.toFixed(2),
    'Profit (LKR)': +t.profit.toFixed(2),
    'Note':         t.note || ''
  }));

  const ws = XLSX.utils.json_to_sheet(rows);
  const wb = XLSX.utils.book_new();
  XLSX.utils.book_append_sheet(wb, ws, 'Trips');
  XLSX.writeFile(wb, `ride-profit-report-${todayStr()}.xlsx`);
  showToast('Excel exported!');
};

/* ══════════════════════════════════════════════════════════════
   TAB SWITCHING
══════════════════════════════════════════════════════════════ */

const switchTab = tabId => {
  tabBtns.forEach(b => b.classList.toggle('active', b.dataset.tab === tabId));
  tabPanels.forEach(p => p.classList.toggle('active', p.id === `tab-${tabId}`));
  if (tabId === 'charts') renderCharts();
};

/* ══════════════════════════════════════════════════════════════
   EVENT LISTENERS
══════════════════════════════════════════════════════════════ */

// Tab navigation
tabBtns.forEach(b => b.addEventListener('click', () => switchTab(b.dataset.tab)));

// Period filter buttons
filterBtns.forEach(b => b.addEventListener('click', () => {
  filterBtns.forEach(x => x.classList.remove('active'));
  b.classList.add('active');
  currentPeriod = b.dataset.period;
  renderDashboard();
}));

// Live preview on form input changes
[inPrice, inDist, inStart, inEnd].forEach(el =>
  el.addEventListener('input', updatePreview)
);

// Add trip button
$('addTripBtn').addEventListener('click', addTrip);

// Delete trip (event delegation on tbody)
tripTableBody.addEventListener('click', e => {
  const btn = e.target.closest('.btn-del');
  if (!btn) return;
  const id = btn.dataset.id;
  showModal('Delete Trip', 'Remove this trip from your records?', () => deleteTrip(id));
});

// Clear all data
$('clearDataBtn').addEventListener('click', () => {
  showModal(
    '🗑 Clear All Data',
    `This will permanently delete all ${trips.length} trip(s). Are you sure?`,
    () => {
      trips = [];
      saveTrips();
      renderDashboard();
      renderTable();
      renderCharts();
      showToast('All data cleared.', true);
    }
  );
});

// Search / filter history
searchInput.addEventListener('input', e => renderTable(e.target.value));

// Export buttons
$('exportPdfBtn').addEventListener('click', exportPDF);
$('exportExcelBtn').addEventListener('click', exportExcel);

// Modal cancel
modalCancel.addEventListener('click', closeModal);
modalOverlay.addEventListener('click', e => {
  if (e.target === modalOverlay) closeModal();
});

// Toggle light/dark theme
$('toggleThemeBtn').addEventListener('click', () => {
  document.body.classList.toggle('light-theme');
  const isLight = document.body.classList.contains('light-theme');
  $('toggleThemeBtn').textContent = isLight ? '☀️' : '🌙';
  localStorage.setItem('rpt_theme', isLight ? 'light' : 'dark');
});

/* ══════════════════════════════════════════════════════════════
   INIT
══════════════════════════════════════════════════════════════ */

const init = () => {
  // Set today's date as default
  inDate.value = todayStr();

  // Restore theme preference
  if (localStorage.getItem('rpt_theme') === 'light') {
    document.body.classList.add('light-theme');
    $('toggleThemeBtn').textContent = '☀️';
  }

  // Load persisted trips
  loadTrips();

  // Initial renders
  renderDashboard();
  renderTable();
  // Charts render lazily when the Charts tab is opened
};

// Boot the app
init();
