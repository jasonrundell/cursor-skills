# Design Patterns Reference

Detailed patterns for marketing sites and SaaS platforms. Referenced from [SKILL.md](SKILL.md) when deeper guidance is needed.

---

## Marketing Site Patterns

### Hero Section

**Purpose**: First impression, communicate value, drive primary action.

**Layout options**:
- **Split hero**: Headline + CTA left, image/illustration right (50/50 or 60/40)
- **Centered hero**: Centered headline + CTA, full-width background image or gradient
- **Video hero**: Background video with overlay text and CTA

**Required elements**:
- Headline: 1 sentence, benefit-focused (40-60 chars)
- Subheadline: Supporting detail (80-120 chars)
- Primary CTA button: High contrast, action verb ("Start Free Trial", "Get Started")
- Optional secondary CTA: Lower emphasis ("Learn More", "Watch Demo")
- Hero image/illustration or background treatment

**Typography**:
- Headline: 48-72px, bold/extrabold
- Subheadline: 18-24px, regular/medium
- CTA button text: 16-18px, medium/semibold

**Spacing**:
- Section padding: 80-120px vertical
- Headline to subheadline: 16-24px
- Subheadline to CTA: 32-40px
- Between CTA buttons: 16px

### Value Propositions

**Purpose**: Communicate key benefits or features.

**Layout options**:
- **3-column grid**: Icon + heading + description per card
- **4-column grid**: Compact version for more features
- **Alternating rows**: Image left/right with text on opposite side

**Card structure**:
- Icon or illustration (48-64px)
- Heading: 20-24px, semibold
- Description: 16px, regular, 2-3 lines max
- Optional link: "Learn more" with arrow

**Spacing**:
- Section padding: 80-100px vertical
- Card gap: 24-32px
- Icon to heading: 16px
- Heading to description: 8-12px

### Social Proof

**Purpose**: Build trust through evidence.

**Pattern options**:
- **Logo bar**: Row of client/partner logos (grayscale, uniform height ~32px)
- **Testimonial cards**: Quote + name + title + avatar
- **Stats row**: 3-4 key metrics ("10K+ users", "99.9% uptime")
- **Case study preview**: Image + title + result metric + link

**Testimonial card structure**:
- Quote text: 16px, italic or regular
- Quote marks or icon
- Avatar: 48px circle
- Name: 16px, semibold
- Title/company: 14px, muted color

### Pricing Section

**Purpose**: Convert visitors by presenting clear pricing tiers.

**Layout**: 2-4 tier cards side by side, one highlighted as "recommended"

**Card structure**:
- Tier name: 20px, semibold
- Price: 36-48px, bold (with period/unit smaller)
- Billing period: 14px, muted
- Feature list: checkmarks + feature text, 14-16px
- CTA button: full width within card
- Highlighted tier: accent border/background, "Most Popular" badge

**Spacing**:
- Card gap: 24px
- Price to features: 24-32px
- Feature items: 12-16px between
- Features to CTA: 32px

### Call-to-Action (Final)

**Purpose**: Reinforce conversion at page end.

**Layout**: Centered, often with background color/gradient

**Elements**:
- Reinforcement headline: 32-40px, bold
- Brief supporting text: 18px
- Primary CTA button
- Optional trust signal (guarantee, no credit card required)

**Spacing**:
- Section padding: 80-100px vertical
- Headline to text: 16px
- Text to CTA: 32px

### Footer

**Purpose**: Navigation, legal, trust signals.

**Layout**: Multi-column (4-5 columns) with bottom bar

**Columns**:
- Brand: Logo + tagline + social icons
- Product: Feature links
- Company: About, careers, contact
- Resources: Blog, docs, help
- Legal: Terms, privacy, cookies

**Typography**: 14px for links, 12px for copyright

---

## SaaS UI Patterns

### Sidebar Navigation

**Purpose**: Primary app navigation.

**Structure**:
- Logo/brand mark at top (collapsed: icon only)
- Navigation groups with section labels
- Each item: icon (20px) + label (14px)
- Active state: accent background or left border
- Collapse toggle at bottom
- User avatar/menu at bottom

**Dimensions**:
- Expanded width: 240-280px
- Collapsed width: 64-72px
- Item height: 40-44px
- Item padding: 12px horizontal
- Group gap: 24px
- Section label: 12px, uppercase, muted

### Top Header Bar

**Purpose**: Contextual actions and global controls.

**Structure**:
- Breadcrumb or page title (left)
- Search bar (center, optional)
- Actions: notifications, help, user menu (right)

**Dimensions**:
- Height: 56-64px
- Horizontal padding: 24-32px

### Dashboard Layout

**Purpose**: Overview of key metrics and activity.

**Structure**:
- Metric cards row (top): 3-4 KPI cards
- Charts section (middle): 1-2 charts side by side
- Activity/table section (bottom): recent activity or data table

**Metric card**:
- Label: 14px, muted
- Value: 28-32px, bold
- Trend indicator: up/down arrow + percentage, colored (green/red)
- Sparkline (optional)
- Card padding: 20-24px

**Grid**:
- 12-column grid base
- Card gap: 16-24px
- Section gap: 24-32px

### Data Table

**Purpose**: Display and manage structured data.

**Structure**:
- Table toolbar: search, filters, bulk actions, column toggle
- Column headers: sortable, resizable
- Data rows: alternating background (subtle) or divider lines
- Pagination: page numbers or load more
- Empty state: illustration + message + action

**Dimensions**:
- Row height: 48-56px
- Header height: 44px
- Cell padding: 12-16px horizontal
- Toolbar height: 56px

**States**:
- Default, hover (row highlight), selected (checkbox + accent background)
- Loading: skeleton rows
- Empty: centered illustration + text + CTA
- Error: error message + retry action

### Form Layout

**Purpose**: Data input and editing.

**Structure**:
- Section grouping with headings
- Label + input pairs (stacked or inline)
- Validation messages below inputs
- Action buttons at bottom (primary + secondary)

**Input dimensions**:
- Height: 40-44px
- Label: 14px, medium weight, 4-8px above input
- Input text: 16px
- Helper text: 12-14px, muted
- Error text: 12-14px, red/destructive

**Spacing**:
- Field gap: 20-24px
- Section gap: 32-40px
- Label to input: 6-8px
- Input to helper/error: 4px

**States**: Default, focus (accent border), error (red border + message), disabled (muted background)

### Settings Panel

**Purpose**: User/system configuration.

**Layout**: Sidebar categories (left, 200-240px) + content panel (right)

**Content structure**:
- Section heading
- Description text (muted)
- Form fields or toggle rows
- Save/cancel buttons per section or floating

**Toggle row**: Label + description (left) + toggle switch (right), 56-64px height

### Empty States

**Purpose**: Guide users when no data exists.

**Structure**:
- Illustration or icon (120-200px)
- Heading: 20-24px, semibold
- Description: 14-16px, muted, 1-2 sentences
- Primary action button

**Placement**: Centered in the content area both horizontally and vertically.

### Modal / Dialog

**Purpose**: Focused task without leaving context.

**Dimensions**:
- Small: 400px wide (confirmations)
- Medium: 560px wide (forms)
- Large: 720px wide (complex content)
- Max height: 80vh with scroll

**Structure**:
- Header: title + close button
- Body: content with padding 24px
- Footer: action buttons (right-aligned: secondary then primary)
- Overlay: semi-transparent dark background

---

## Typography Scale

| Level    | Size  | Weight    | Line Height | Use Case                    |
| -------- | ----- | --------- | ----------- | --------------------------- |
| Display  | 48-72px | Bold     | 1.1-1.2     | Marketing hero headlines    |
| H1       | 36-40px | Bold     | 1.2         | Page titles                 |
| H2       | 28-32px | Semibold | 1.3         | Section headings            |
| H3       | 22-24px | Semibold | 1.4         | Subsection headings         |
| H4       | 18-20px | Medium   | 1.4         | Card headings               |
| Body     | 16px    | Regular  | 1.5-1.6     | Paragraphs, descriptions    |
| Body SM  | 14px    | Regular  | 1.5         | Table cells, form labels    |
| Caption  | 12px    | Regular  | 1.4         | Helper text, timestamps     |
| Overline | 12px    | Medium   | 1.3         | Section labels, uppercase   |

---

## Spacing Scale

Base unit: 4px. Use multiples for consistency.

| Token | Value | Use Case                          |
| ----- | ----- | --------------------------------- |
| xs    | 4px   | Icon-to-text inline gap           |
| sm    | 8px   | Tight element spacing             |
| md    | 12px  | Input padding, compact card gap   |
| base  | 16px  | Default gap, card padding         |
| lg    | 24px  | Section inner spacing, card gap   |
| xl    | 32px  | Section padding, large gaps       |
| 2xl   | 48px  | Page section separation           |
| 3xl   | 64px  | Major section separation          |
| 4xl   | 80px  | Marketing section vertical padding|
| 5xl   | 120px | Hero section vertical padding     |

---

## Color Usage Guidelines

### Semantic Colors

| Role        | Usage                                      | Contrast Requirement |
| ----------- | ------------------------------------------ | -------------------- |
| Primary     | CTAs, active states, links                 | 4.5:1 on background  |
| Secondary   | Secondary actions, supporting elements     | 4.5:1 on background  |
| Destructive | Delete, error states, warnings             | 4.5:1 on background  |
| Success     | Confirmations, positive trends             | 4.5:1 on background  |
| Warning     | Caution states, pending items              | 4.5:1 on background  |
| Muted       | Helper text, placeholders, disabled states | 4.5:1 on background  |

### Background Hierarchy

| Layer       | Purpose                            |
| ----------- | ---------------------------------- |
| Background  | Page/app background                |
| Surface     | Cards, panels, elevated content    |
| Elevated    | Modals, dropdowns, popovers       |
| Overlay     | Behind modals (semi-transparent)   |

### Marketing Color Tips

- Limit palette to 2-3 colors max (primary, accent, neutral)
- Use accent color sparingly — only for CTAs and key highlights
- White/light backgrounds for readability; dark sections for contrast breaks
- Ensure hero text is legible over images (use overlays if needed)

### SaaS Color Tips

- Neutral-heavy palette with accent for actions and status
- Avoid bright colors in data-dense areas — reserve for alerts
- Status colors must be distinguishable by colorblind users (use icons alongside color)
- Dark mode: invert backgrounds, keep semantic colors recognizable

---

## Responsive Breakpoints

| Name     | Min Width | Columns | Gutter | Margin | Target Device    |
| -------- | --------- | ------- | ------ | ------ | ---------------- |
| Mobile   | 0px       | 4       | 16px   | 16px   | Phones           |
| Tablet   | 640px     | 8       | 24px   | 24px   | Tablets          |
| Desktop  | 1024px    | 12      | 24px   | 32px   | Laptops          |
| Wide     | 1440px    | 12      | 32px   | auto   | Large monitors   |

### Responsive Rules

**Marketing sites** (mobile-first):
- Stack all columns on mobile
- 2-column grid at tablet
- Full layout at desktop
- Max content width: 1200-1280px, centered

**SaaS platforms** (desktop-first):
- Sidebar collapses to icons at tablet
- Sidebar becomes bottom nav or hamburger on mobile
- Tables switch to card layout on mobile
- Max content width: fluid within sidebar + content structure
