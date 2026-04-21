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