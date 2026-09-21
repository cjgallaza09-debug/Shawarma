# Cash Register X Automatic V9.1 – Consolidated Build

This build combines the requested V9.1 updates:

- Customer Order Number textbox in Complete Sale; cashier types the customer/order number.
- Order number is saved with the sale, shown in Sales History/receipt, Excel export, and thermal receipt.
- Cross-platform thermal printer controls in Settings: Connect, Disconnect, Test Print, printer status, and automatic printing after a successful sale.
- Staff can access Settings for My Account, printer controls, and online status. Admin keeps the full Settings controls.
- Checkout saves the parent sale first and uses the returned Supabase sales.id for sale_items.
- Admin-only refunds with validation and Supabase refresh.
- Existing Admin/Staff permissions, PWD 20% discount, separate inventory, and online-only behavior are preserved.

## Supabase
Run `MASTER_DATABASE_FIX.sql` once in the Supabase SQL Editor. It is idempotent and includes the `sales.order_number` and `sales.pwd_discount` columns plus the V9.1 FK/RLS fixes.

## Thermal printer support
The printer panel now supports three browser connection paths:

- **Bluetooth LE / GATT:** for printers that expose a writable BLE characteristic.
- **USB / OTG:** WebUSB path for supported Chromium browsers and printer USB interfaces. Android can use this with a compatible USB-OTG connection.
- **USB or Bluetooth COM / Serial:** Web Serial path for Windows printers exposed as a COM/serial port, including paired Bluetooth Classic/SPP printers when Windows exposes them as a serial port.

The XP-58H label says USB + BT. Many 58H receipt printers use Bluetooth Classic/SPP rather than BLE/GATT, so the browser cannot assume that its Bluetooth connection is directly accessible through Web Bluetooth. The app therefore keeps USB/OTG and Serial/COM as alternatives instead of falsely treating every Bluetooth printer as BLE.

Use **HTTPS** (such as GitHub Pages) and a Chromium-based browser that exposes the required hardware API. Browser hardware APIs are permission-based and device/driver dependent.

### Recommended connection paths
- **Android:** USB/OTG first for the XP-58H; Bluetooth LE only if the printer actually exposes BLE/GATT.
- **Windows:** USB first; if the printer is paired as a COM port, choose Serial/COM and select the printer's baud rate.

The POS saves the sale before attempting automatic printing. A printer failure therefore does not delete the completed sale.


## NEW DATABASE SETUP

This build intentionally keeps the existing Cash Register X app, POS UI,
Bluetooth printer UI/functionality, and Staff/Admin application behavior.
The only application connection change is `config.js`, which now points to:

`https://typnceqlgrcpkokulnkp.supabase.co`

### Database setup
1. Open the NEW Supabase project.
2. Open SQL Editor.
3. Open `NEW_DATABASE_SETUP.sql`.
4. Run the whole file once.
5. Confirm the final query shows:
   - username: `cjgallaza09`
   - role: `admin`
6. Log out of Cash Register X and log in again.

### Important
The browser must contain only the Supabase publishable key. Never put a
service-role/secret key into `config.js`.

### Staff account creation
The app's Admin -> Staff Accounts uses the `create-staff` Edge Function.
Database SQL alone cannot deploy an Edge Function. If Staff creation is
needed, deploy `supabase/functions/create-staff/index.ts` to the same
Supabase project using the Supabase CLI/dashboard deployment workflow.

### What was NOT changed
- Bluetooth printer implementation
- Settings printer UI
- POS layout
- Products/Inventory/Sales/Expenses/Reports UI
- Cart/checkout behavior
- Admin/Staff navigation
