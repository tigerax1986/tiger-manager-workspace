# MEMORY.md - Long-Term Memory

## Development Standards & Best Practices

### Theme System (Created April 2026)
- **Global CSS file**: Use `src/assets/css/theme.css` for all global styling
- **CSS Variables**: Always use CSS custom properties (`var(--color-primary)`) instead of hardcoded values
- **Reusable Components**: Use `.panel`, `.card`, `.table` classes for consistent UI components
- **Utility Classes**: Leverage utility classes for spacing, colors, flexbox (`mt-3`, `text-primary`, `flex gap-2`)
- **Button System**: Use `.btn` classes with variants (`.btn-primary`, `.btn-secondary`, `.btn-danger`)
- **Single Source**: All colors, spacing, typography defined in `theme.css` only

### SVG Editor Development Patterns
- **Database Storage**: SVG files stored as XML TEXT in PostgreSQL database, not as separate files
- **Stadium-Centric Design**: All tables keyed by `stadium_code` with foreign key relationships
- **API Response Format**: Services return raw arrays/objects, not `ApiResponse` wrappers
- **Smart Save Logic**: Creates new record if description changes, updates existing if unchanged
- **Pattern Fill Preservation**: SVG patterns stored in `<defs>` with `data-image-url` attribute mapping
- **Text Tool Support**: Text shapes with font properties (fontFamily, fontSize, fontWeight, fontStyle, textAnchor) stored as shape attributes, editable via properties panel
- **Shape Labels**: Labels as properties of shapes (rectangle, circle, polygon) with font controls, relative positioning, rotation, and center alignment
- **Multi‑Select**: Support Ctrl/Cmd+click for selecting multiple shapes, with group movement via mouse drag or arrow keys, and property editing disabled for multi‑selection

### Frontend Architecture
- **Component Organization**: Page components in `src/pages/`, shared components in `src/components/`
- **API Integration**: Use services from `@shared` package for backend communication
- **State Management**: Use reactive refs/computed for component state, Pinia for app-wide state
- **Error Handling**: Try/catch blocks with user-friendly alerts and console logging for debugging

### Git & Repository Management
- **Main Repository**: `ticketing-system` - Primary application code
- **Agent Workspace**: `tiger-manager-workspace` - Agent configuration and memory
- **Commit Messages**: Use descriptive commit messages with feat/fix/docs prefixes
- **Branch Strategy**: Main branch for production-ready code, feature branches for development

### UI/UX Principles
- **Consistent Spacing**: Use CSS variable spacing (`--space-sm`, `--space-md`, `--space-lg`)
- **Color Harmony**: Primary blue (`#3b82f6`), Secondary gray (`#f1f5f9`), Danger red (`#dc2626`)
- **Accessible Contrast**: Ensure text has sufficient contrast against backgrounds
- **Responsive Design**: Use CSS Grid/Flexbox with media queries for mobile support
- **Avoid Redundant Text**: Place detailed instructions in help documentation, not cluttering UI space

## Project History

### April 2026 - SVG Editor Development
- Renamed seatmap page to "SVG Editor" with complete database integration
- Implemented stadium-centric database schema with SVG storage
- Created comprehensive theme system with CSS variables and reusable components
- Added polygon position editing and background image support
- Established development standards for consistent UI across pages
- Implemented Text Tool with font controls, position editing, and SVG save/load support
- Added Shape Labels as properties of shapes (rectangle, circle, polygon) for labeling areas with font controls and relative positioning

### Key Decisions
1. **Single Theme File**: Consolidated all global styles into `theme.css` for easy maintenance
2. **CSS Variables**: Adopted CSS custom properties for theming consistency
3. **Reusable Patterns**: Created `.panel`, `.card`, `.table` classes for consistent components
4. **Button System**: Standardized button styling with variants and sizes
5. **SVG Storage**: Database storage over file system for better data management
6. **Age Calc for Price Bands**: Boolean flag auto-assigns price band based on customer age during online registration
7. **Paid vs Free Band Split**: A–Z for paid bands, 0–9 for free-of-charge bands, stored in same table with sort_order
8. **Overlay System**: Reusable pricing/restriction configs applied to seat subsets, with sentinel values ('exclude' / null) for DB storage
9. **Turnstile Columns**: `turnstile_from`/`turnstile_to` added to seats for entry gate ranges, validated by formatTurnstileInput

### Lessons Learned
- Early investment in theme system pays off in long-term maintenance
- CSS variables enable rapid theme changes without hunting for color values
- Reusable component classes reduce duplication and ensure consistency
- Polygon position editing requires translating all points together, not just one coordinate
- Database migrations should be idempotent with `IF NOT EXISTS` clauses

### System Cleanup & Process Management (April 2026)
- Multiple Vite dev servers can accumulate on ports 3002–3006 if processes aren't terminated properly
- Cleanup procedure: identify extra boxoffice instances via `ss -tlnp` and kill by PID
- Keep essential services: websales (3000), boxoffice (3001), API (8000)
- Always verify remaining processes with `ss -tlnp | grep node` after cleanup

### May 2026 - Price Band Maintenance Development
- Created PriceBandMaintenance.vue following all development standards
- **Database**: `price_bands` table with columns: code, description, age_from, age_to, web_visible, active, age_calc, sort_order
- **26 paid bands (A–Z)**: Only A (Adult 18-64), J (Junior 4-15), O (OAP 65+) active + web_visible + age_calc by default
- **10 free bands (0–9)**: All inactive by default, intended for carers, complimentary, etc.
- **Age Calc**: Boolean flag enabling auto-assignment of price band during online registration based on customer's birth date
- **Drag-to-reorder**: Click-and-drag reordering with sort_order persisted via `POST /api/price-bands/reorder`
- **Two-panel UI**: Paid bands (A–Z) shown first, Free of Charge bands (0–9) below, each in separate panel
- **Inline editing**: Click Edit to edit description, age_from, age_to, web_visible, active, age_calc inline
- **API endpoints**: `GET /api/price-bands`, `PUT /api/price-bands/:id`, `POST /api/price-bands/reorder`
- **No Help button originally**: Added 📘 Help button with help doc in this session (May 4)
- **Migration**: `price_bands.001.sql` with ON CONFLICT DO NOTHING seed data
- **Charge type removed**: Dropped obsolete `charge_type` column during migration

### April-May 2026 - Stadium Maintenance Updates (Post-April 22)
- **Overlay System**: Full CRUD for overlays via OverlayManagerModal, applied per seat subset
  - Overlay code + description + price code + restriction + category + turnstile range
  - 'No Overlay' sentinel writes 'exclude' to DB; 'Keep Current' writes null
  - Deleting overlay cascades to delete all overlay seats
  - Offset column hidden in overlay mode (seat-global, not overlay-specific)
  - Tree shows asterisks (*) on stands/areas with overlay data
- **Turnstile Fields**: `turnstile_from`/`turnstile_to` VARCHAR(10) on seats table
  - Accepts A–Z letters or 0–200 numbers, validated by `formatTurnstileInput`
  - Used on row definitions, single seat, batch edit
- **Available Boolean**: Replaced `status` VARCHAR with `available` BOOLEAN
  - TRUE = for sale, FALSE = unavailable/blocked (UA colour)
  - In overlay mode preserves null for unchanged
- **Row Code**: Renamed from `row_label` to `row_code` for clarity
- **Delete All button removed**: From main UI, replaced with "⋮" menu; cascading deletes handle overlay seats
- **Atomic sequence reorder**: `POST /api/seats/batch/reorder-sequence` prevents race conditions
- **Add from Selection**: Row definitions modal with auto-calc "To Seat", contiguity validation, uppercase enforcement
- **Seat grid refinements**: Auto-fit at 0.95 margin, zoom in both modes, user-select none, expandable row groups
- **Colour key/legend**: Swatches by type (restriction, category, default), selected seats get blue override

### April 2026 - Email Designer Development
- Created EmailDesigner page following all development standards
- **Removed stadium dependency**: Modified email_templates table to drop stadium_code column (migration 006), making templates globally scoped
- Implemented placeholder service with master list consistent across all designers (updated to remove AdultPrice, ConcessionPrice, RefundDate; added PriceBand)
- Two‑view editor: HTML code, Live preview (Rich text removed due to functionality issues)
- Smart save logic: overwrites template if same name/type, creates new otherwise
- Used theme system (CSS variables, reusable components, utility classes)
- Added API endpoints for CRUD operations on email templates (global)
- **No stadium association**: Email templates are not stadium‑specific; stadium selection removed from UI
- Integrated with existing shared package services
- Placeholder format: <<PlaceholderName>> as per specification
- **Layout** (April 20): Three‑column design – left: tools (save/delete/clear/download/view mode), middle: canvas with template name/type/load dropdown, right: placeholders
- **Placeholders**: Categorized expandable sections (Product Details, Customer Details, Payment Details, Delivery & Refunds) with vertical button lists, collapsed by default
- **UI fixes**: Help button height matched to SVG editor; font options removed
- **Checked insert scripts**: SVG data table migration uses ON CONFLICT clauses to prevent duplicate inserts during development

### April 2026 - Stadium Maintenance Development
- **Goal**: Replace old flat‑file system (StadiumData/StadiumVisual) with hierarchical database structure (Stadium → Stand → Area → Seat).
- **Hierarchy**: Removed Seat Blocks level; seats generated directly from row definitions.
- **UI Components**: Created StadiumMaintenance.vue with tree navigation (left panel) and grid‑based seat editor (right panel).
- **Modal Components**: StadiumModal, StandModal, AreaModal, SeatModal, AddRowsModal.
- **Database Migrations**:
  - `004_stadium_maintenance.sql` – creates stands, areas, seats, seat_blocks tables.
  - `005_add_seat_offset.sql` – adds `offset_x` column for per‑seat horizontal offset.
  - `011_add_row_label.sql` – adds `row_label` column for custom row labels (A, B, AA, ZZ).
- **API Endpoints**: Full CRUD for stadiums, stands, areas, seats; seat‑generation via `POST /api/seats/generate‑from‑block`.
- **Row‑Based Seat Generation**:
  - User defines rows via AddRowsModal (label, sequence, seat range).
  - Row definitions stored locally until seats are generated.
  - Each row becomes a seat‑block with `rows: 1`.
- **Grid Canvas**:
  - Displays seats aligned by seat number across rows.
  - Empty slots shown as dashed boxes for missing seat numbers.
  - Row ordering: higher `row_sequence` appears at top (closer to back), lower sequence at bottom (closer to pitch).
- **UI Simplification (April 22)**:
  - Removed old bulk seat‑generation form.
  - Moved “Add Rows” button to panel header.
  - Fixed overlapping buttons in row‑definitions editor.
- **Schema Simplification (Completed – April 22)**:
  - Dropped `row_number` column (migration 012).
  - New unique constraint on `(stadium_code, stand_code, area_code, row_label, seat_number)`.
  - `row_label` set to NOT NULL, default `'Row ' || row_sequence`.
  - Index on `(row_sequence, seat_number)` for sorting.
  - API & frontend updated accordingly.
- **Seat Colours System (Completed – April 22)**:
  - Created `seat_colours` table (UK spelling) with hex colour values.
  - Migration `015_seat_colours_table.sql` inserts default colours for boxoffice stadium‑maintenance.
  - Colours stored as hex codes (e.g., `#f1f5f9`) not CSS variables for portability.
  - API endpoints at `/api/seat-colours` with CRUD operations.
  - Frontend service `seatColourService` fetches colours for dynamic seat styling.
  - Seat legend/key displays colour swatches grouped by type (restriction, category, default).
  - Selected seats override base colours with consistent blue (`var(--color-primary)`).
- **Development Standards**:
  - Follows theme system (`theme.css`, CSS variables, reusable components).
  - Uses `@shared` services for API communication.
  - TypeScript interfaces for type safety.
  - Consistent error handling with toast notifications.
- **Testing Context**:
  - Test sequence: 1=A (bottom), 2=B, 3=C (top).
  - Seat ranges: Row A seats 1‑5, Row B seats 3‑5, Row C seats 3‑5.
  - Expected grid alignment with empty slots for missing seat numbers.

### April 2026 - TicketDesigner Development

**Goal**: Create a ticket‑layout editor for Print‑at‑Home (PDF) and BOCA printer output, modeled on the existing SVG Editor page.

**Core Features**:
- **Canvas with rulers & grid**: A4 default, paper‑size options (A4, A5, A6, Letter, Custom), orientation toggle.
- **Placeholder drag‑and‑drop**: Left‑side panel with placeholders (MatchDescription, DateTime, SeatInfo, TicketNumber, Price, Barcode, QRCode).
- **Output‑mode toggle**: Switch between Print‑at‑Home (PDF) and BOCA (printer command).
- **Real‑time preview**: Grid lines, rulers in mm, live placeholder rendering with sample data.
- **Properties panel**: Right‑side panel for editing selected placeholder properties (position, size, rotation, font, colors).
- **Smart save/load/delete**: Layouts stored in `ticket_layouts` table, filtered by output mode.
- **Background images**: URL‑based background images with CORS handling and local‑image support (`public/images/`).
- **Advanced placeholder features**:
  - Auto‑sizing based on text length and font properties.
  - Drag‑and‑drop movement with arrow‑key nudging.
  - Multi‑select (Ctrl/Cmd+click) for group movement.
  - QR‑code size field with live debounced updates.
  - Barcode/QR‑code graphic generation using `jsbarcode` and `qrcode` libraries.

**Database Schema** (`ticket_layouts` table):
- `id` UUID PRIMARY KEY
- `description` TEXT NOT NULL
- `paper_size` TEXT NOT NULL
- `orientation` TEXT NOT NULL
- `output_mode` TEXT NOT NULL ("print‑at‑home" or "boca")
- `layout_data` JSONB NOT NULL (stores canvas properties, placeholders, background image URL)
- Unique constraint: `(description, output_mode)`
- **No stadium dependency**: Originally had `stadium_code` column; removed via migration 007.

**Migrations**:
- `005_ticket_layout_tables.sql` – initial table creation (with stadium_code).
- `007_remove_stadium_from_ticket_layouts.sql` – drops stadium_code column, adds unique constraint.

**API Endpoints** (`/api/ticket‑layouts`):
- `GET /` – returns layouts filtered by `output_mode` query param.
- `GET /:id` – returns single layout.
- `POST /` – creates new layout.
- `PUT /:id` – updates existing layout.
- `DELETE /:id` – deletes layout.
- **Smart‑save logic**: If description unchanged, update existing; if changed, create new.

**Frontend Architecture**:
- **Page**: `apps/boxoffice/src/pages/TicketDesigner.vue`
- **Structure**: Three‑column layout (left: placeholders, middle: canvas, right: properties).
- **State management**: Vue 3 Composition API with reactive refs, computed properties.
- **Styling**: Full adherence to theme system (`theme.css`, CSS variables, reusable components).
- **Services**: `ticketLayoutService` in `@shared` package.
- **External libraries**: `qrcode`, `jsbarcode` for graphic generation.

**Key Implementation Details**:
1. **Grid & rulers**: Ticks generated in mm, converted to pixels (1mm = 3.779528px at 96 DPI).
2. **Placeholder auto‑size**: Uses off‑screen canvas to measure text dimensions.
3. **Numeric input handling**: Raw string bindings with validation on blur to avoid Vue `v‑model.number` rounding loops.
4. **QR‑code sizing**: Exact mm↔px conversions, formatted display (1 decimal place), debounced live updates.
5. **Background image CORS**: Image preloading with `crossOrigin='anonymous'`, error states, local‑image support.
6. **Multi‑select**: Global mouse/keyboard listeners for dragging and arrow‑key movement.
7. **Undo/redo**: History stack limited to 50 actions.

**Lessons Learned**:
- **CORS issues**: Free image hosts (Imgbox) block cross‑origin requests; provide local‑image alternative.
- **Vue numeric inputs**: `type="number"` with `v‑model.number` causes rounding loops; use text inputs with validation.
- **QR‑code pixel rounding**: Exact conversions prevent values like "60" becoming "60.1".
- **Declaration order**: Computed properties that depend on reactive refs must be declared after those refs.
- **Grid alignment**: Use CSS Grid layout (not absolute positioning) for precise axis‑canvas alignment.

**Known Limitations / Future Enhancements**:
- Multi‑select property editing is basic (only movement).
- Print preview buttons are stubs (no actual PDF/BOCA generation).
- Placeholder rotation is not visually rendered (only property).
- BOCA example is static; no live BOCA‑command generation.
- No master placeholder service yet (shared across TicketDesigner, EmailDesigner).

**Testing Notes**:
- **Local image test**: `http://localhost:3001/images/placeholder.svg`
- **CORS‑friendly external test**: `https://picsum.photos/800/600`
- **Layout filtering**: Changing output mode clears current layout selection.
- **Smart save**: Try saving same description twice (updates) vs. new description (creates new).

**Related Files**:
- `apps/boxoffice/src/pages/TicketDesigner.vue` – main component.
- `apps/api/src/migrations/005_ticket_layout_tables.sql` – initial schema.
- `apps/api/src/migrations/007_remove_stadium_from_ticket_layouts.sql` – stadium removal.
- `apps/api/src/index.ts` – API endpoints.
- `packages/shared/src/services/api.ts` – `ticketLayoutService`.
- `packages/shared/src/types/index.ts` – `TicketLayout` interface.
- `apps/boxoffice/public/images/placeholder.svg` – local test image.

**Servers**:
- Frontend: `http://localhost:3001/ticket‑designer`
- API: `http://localhost:8000/api/ticket‑layouts`

---

## Future Development Plans

### Shared – Placeholders & Database Foundations (April 2026)
**Purpose**: Foundational work supporting TicketDesigner, EmailDesigner, and Stadium Maintenance.

**Key Requirements**:
- Follow all development standards from MEMORY.md (theme system, reusable components, TypeScript, Vue 3 Composition API)
- Stadium-centric design with foreign key relationships
- API services return raw arrays/objects (no ApiResponse wrappers)
- Smart save logic: create new record if description changes, update if unchanged
- Pattern fill preservation for SVGs
- Text tool support with full font properties and shape labels
- Multi-select support (Ctrl/Cmd+click)

**Tasks**:
1. **Master Placeholder Service**
   - Create central service in @shared package
   - Return 100% consistent master list across all designers
   - Include placeholders: MatchDescription, PayRef, Stand, Area, Row, Seat, CustomerName, Price, Date, Time, K/O, TicketType, PriceBand, EventName, Venue, SeatNumber, CustomerEmail, OrderNumber, TotalAmount, Quantity, PurchaseDate, DeliveryMethod, RefundAmount, etc.
   - Each placeholder must provide sample rendering text (e.g., MatchDescription → "Manchester United vs Arsenal")

2. **Database Tables**
   - Design and create migration scripts for:
     - `ticket_designs` (TicketDesigner layouts)
     - `email_templates` (EmailDesigner, with type + is_default columns)
     - `stadiums`, `stands`, `areas`, `seats` (normalized hierarchy to replace old StadiumData/StadiumVisual files)
   - Follow stadium-centric patterns and smart-save logic exactly

**Notes**:
- This is a shared foundation job (assigned to separate agent sessions via Discord)
- All new code must adhere to established development standards
- Expandable placeholder list for future requirements

## Personal Preferences

### Development Style
- Prefer concise, direct communication
- Focus on practical solutions over theoretical perfection
- Value maintainability and consistency in code
- Appreciate clean, organized UI with clear visual hierarchy

### Technical Preferences
- TypeScript for type safety
- Vue 3 Composition API
- CSS Variables for theming
- PostgreSQL for relational data
- Git for version control with descriptive commits

## Relationships & Context

### Luke (User)
- Working on ticketing system project
- Prefers quick, concise, direct replies
- Values maintainable, well-documented code
- Interested in SVG editing for stadium layouts
- Planning to delegate work to multiple agents
- Using Discord channels (agent1, agent2, agent3) for parallel agent sessions with isolated contexts

### Project Goals
- Complete SVG editor for stadium layout creation
- Database integration for SVG storage and retrieval
- Consistent UI across all application pages
- Scalable architecture for future features
- Well-documented code for maintainability
- Master placeholder service for consistent templating across TicketDesigner, EmailDesigner
- TicketDesigner system for ticket layout creation
- EmailDesigner system for email templates
- Normalized stadium hierarchy (stadiums, stands, areas, seats) to replace legacy files
- Multi-agent parallel workflow via Discord channels (agent1, agent2, agent3)
### Help Documentation Split (May 2026)
- **User view**: Functional, operational docs for end users — what the page does, how to use it, workflows, troubleshooting
- **Developer view**: Schema definitions, API endpoints, migration details, seed data, indexes/triggers, code conventions
- **Toggle**: Small 🔒/🔓 toggle bottom-left of HelpModal footer (same row as Close). Defaults to user view. No password yet.
- **Markers**: Use `<!-- devinfo -->` / `<!-- /devinfo -->` HTML comment markers in `.md` files to wrap developer-only content
- **Filtering**: HelpModal's `filteredContent` computed strips devinfo blocks when `showDevInfo = false`, removes markers when `true`
- **Applied to**: All 11 help docs in `apps/boxoffice/src/docs/`
- **Removed from user view**: Default active band lists, migration details, seed data, schema tables, API request/response formats
