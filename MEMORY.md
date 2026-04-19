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

## Project History

### April 2026 - SVG Editor Development
- Renamed seatmap page to "SVG Editor" with complete database integration
- Implemented stadium-centric database schema with SVG storage
- Created comprehensive theme system with CSS variables and reusable components
- Added polygon position editing and background image support
- Established development standards for consistent UI across pages
- Implemented Text Tool with font controls, position editing, and SVG save/load support

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

### Project Goals
- Complete SVG editor for stadium layout creation
- Database integration for SVG storage and retrieval
- Consistent UI across all application pages
- Scalable architecture for future features
- Well-documented code for maintainability