# PCF Canvas Standard — Navigation (Multi-page)

## Rule: never use BrowserRouter

`BrowserRouter` (react-router-dom) manipulates the browser URL, which breaks Power Apps' URL.
Use `MemoryRouter` only if you need a real router (in-memory, no URL changes).

## Recommended: useState (SPA inside PCF)

Treat the PCF as a single-page app. Use React state to switch views.

```tsx
import * as React from 'react';
import { useState } from 'react';
import { DashboardPage } from './pages/DashboardPage';
import { StockInPage } from './pages/StockInPage';

export const MainApp: React.FC = () => {
  const [currentPage, setCurrentPage] = useState('dashboard');

  return (
    <div style={{ display: 'flex', width: '100vw', height: '100vh' }}>
      <aside style={{ width: '220px', backgroundColor: '#1b1a19', color: 'white' }}>
        <button onClick={() => setCurrentPage('dashboard')}>Dashboard</button>
        <button onClick={() => setCurrentPage('stock-in')}>Stock In</button>
      </aside>
      <main style={{ flex: 1, padding: '24px', overflowY: 'auto' }}>
        {currentPage === 'dashboard' && <DashboardPage />}
        {currentPage === 'stock-in' && <StockInPage />}
      </main>
    </div>
  );
};
```

Benefits: instant page switch (no reload), persistent sidebar/navbar, shared state via Context API.

## Alternative: Hybrid (Canvas Screens + PageName property)

Use when some screens need standard Power Apps controls mixed in.

1. Add `PageName` property (SingleLine.Text, usage="input") to manifest.
2. Read `context.parameters.PageName.raw` in React to switch views.
3. Place same PCF on multiple Canvas Screens with different PageName values.
4. Use `Navigate(Screen2)` in Power Apps to switch.

## MemoryRouter (if needed)

```tsx
import { MemoryRouter, Routes, Route } from 'react-router-dom';

<MemoryRouter>
  <Routes>
    <Route path="/" element={<Dashboard />} />
    <Route path="/stock-in" element={<StockIn />} />
  </Routes>
</MemoryRouter>
```

In-memory routing — no URL manipulation.
