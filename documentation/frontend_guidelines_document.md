# Frontend Guideline Document

This document explains, in everyday language, how the frontend of **brain2train1.1** is built, styled, organized, and tested. Whether you’re a new team member or a stakeholder, you’ll see how all the pieces fit together.

## 1. Frontend Architecture

**Frameworks and Libraries**
- **React (Create React App)**: The backbone of our user interface. It lets us build reusable pieces (components) like buttons, cards, and charts.
- **Tailwind CSS**: A utility-first CSS framework that provides small, single-purpose classes so we can style components quickly and consistently.
- **React Router**: Handles navigation between different pages without reloading the browser.
- **Recharts** (or Chart.js): Draws progress charts and graphs (line charts, bar graphs) in a simple, customizable way.
- **ESLint & Prettier**: Automatic code checking and formatting tools that keep our code clean and consistent.

**Why This Architecture Works**
- **Scalability**: React’s component system means we can add new exercises or pages without rewriting existing code.
- **Maintainability**: Small, meaningful files and clear folder structure make it easy to find and fix bugs or add features.
- **Performance**: React updates only what changes on the page, and Tailwind’s utility classes keep our CSS bundle small.

## 2. Design Principles

We follow three core ideas when building interfaces:

1. **Usability**
   - Clear, self-explanatory buttons and labels.
   - Logical layout: sidebar for navigation, main area for content.
2. **Accessibility**
   - WCAG 2.1 AA standards: sufficient color contrast, keyboard navigation, and screen-reader support.
   - ARIA labels on interactive elements.
3. **Responsiveness**
   - Mobile-first approach: layouts adapt from small phone screens to large desktops.
   - Tailwind’s responsive utilities (`sm:`, `md:`, `lg:`) make breakpoints easy to manage.

These principles guide every UI decision: from font sizes and spacing to button states and error messages.

## 3. Styling and Theming

**Styling Approach**
- We use **Tailwind CSS** for styling, avoiding large custom CSS files. Classes like `bg-primary` or `text-base` apply single responsibilities.
- Utility-first means we rarely write raw CSS; most tweaks happen in the component’s `className`.

**Theming**
- All colors, fonts, and spacing values live in `tailwind.config.js`, ensuring a single source of truth.
- Dark mode support via the `dark:` prefix in Tailwind.

**Visual Style**
- **Overall Look**: Modern and flat design with subtle shadows and rounded corners.
- **Glassmorphism Touches**: Lightly frosted backgrounds for modals and cards to create depth.

**Color Palette**
- **Primary**: #4F46E5 (Indigo)
- **Secondary**: #10B981 (Green)
- **Accent**: #F59E0B (Amber)
- **Neutral Light**: #F3F4F6 (Gray-100)
- **Neutral Dark**: #374151 (Gray-700)
- **Text Primary**: #111827 (Gray-900)
- **Error**: #EF4444 (Red-500)

**Font**
- **Inter**: A clean, modern sans-serif available via Google Fonts. Used for all headings and body text.

## 4. Component Structure

**Folder Layout**
```
/src
  /components   ← Reusable UI pieces (buttons, cards, headers)
  /pages        ← Top-level views (Dashboard, Training, Progress)
  /layouts      ← Shared page layouts (SidebarLayout, AuthLayout)
  /context      ← React Context providers (AuthContext, ThemeContext)
  /services     ← API calls and helper functions
  /hooks        ← Custom React hooks
  /assets       ← Images, icons, fonts
  /styles       ← Tailwind config and global styles
```

**Reuse and Consistency**
- Each component lives in its own folder with a `.jsx` (or `.tsx`) file and a test file.
- Props are clearly typed (using PropTypes or TypeScript), making reuse predictable.

**Why Component-Based?**
- Changes in one place update everywhere it’s used.
- Encourages small, focused components that are easier to test and understand.

## 5. State Management

**Approach**
- **React Context API** for global data: authentication status, user profile, theme mode.
- **Local React state** (`useState`) for component-specific data: form inputs, toggle states.
- **Custom Hooks** (`useAuth`, `useTrainingData`) bundle related state and logic into reusable functions.

**Sharing State**
1. **AuthContext** wraps the app and provides `{ user, token, signIn, signOut }`.
2. **TrainingContext** holds the current exercise, difficulty level, and points earned.
3. Components call `useContext` to read or update shared data without prop drilling.

This keeps global data in one place and local state close to where it’s used.

## 6. Routing and Navigation

**Library**
- **React Router v6** handles URL-based navigation.

**Structure**
```
<BrowserRouter>
  <AuthProvider>
    <Routes>
      <Route path="/login" element={<LoginPage />} />
      <Route path="/signup" element={<SignUpPage />} />
      <Route element={<ProtectedLayout />}>  ← checks for auth, shows sidebar
        <Route path="/dashboard" element={<DashboardPage />} />
        <Route path="/training/:moduleId" element={<TrainingPage />} />
        <Route path="/progress" element={<ProgressPage />} />
      </Route>
    </Routes>
  </AuthProvider>
</BrowserRouter>
```

**Navigation Flow**
- Users must sign in to access protected routes.
- Sidebar links and header menus use `<NavLink>` for active-state styling.
- Lazy-loaded pages (`React.lazy` + `Suspense`) improve initial load time.

## 7. Performance Optimization

We aim for a quick, smooth experience:

1. **Code Splitting & Lazy Loading**
   - Break up large pages into separate chunks.
   - Wrap heavy components (charts, exercise screens) in `React.lazy`.
2. **Tree Shaking**
   - Only import the Tailwind utility classes and chart components we actually use.
3. **Asset Optimization**
   - Compress images (SVGs for icons, optimized PNG/JPEG).
   - Serve assets via a CDN (Netlify or Vercel) for global reach.
4. **Memoization**
   - `React.memo` and `useMemo` to avoid unnecessary re-renders of static components.
5. **Efficient Charts**
   - Only render visible data points; unload old data when not in view.

These steps keep page weight low and interactions snappy.

## 8. Testing and Quality Assurance

**Unit Tests**
- **Jest** + **React Testing Library** for component-level tests.
- Focus on rendering, user interactions, and output of custom hooks.

**Integration Tests**
- Combine related components (e.g., form + validation) to ensure they work together as expected.

**End-to-End (E2E) Tests**
- **Cypress** for simulating real user flows: sign up, log in, complete an exercise, view progress.

**Linting and Formatting**
- **ESLint** enforces code quality rules (no unused variables, consistent returns).
- **Prettier** auto-formats code on save or before each commit.

**Continuous Integration**
- **GitHub Actions** runs lint, tests, and type checks on every pull request.
- Merge only when all checks pass, ensuring high code quality.

## 9. Conclusion and Overall Frontend Summary

This document lays out a clear, everyday-language guide to our frontend:
- **Architecture**: React + Tailwind + React Router + Recharts.
- **Design**: Usable, accessible, and responsive by design.
- **Styling**: Modern flat look with Tailwind and a coherent color palette.
- **Components**: Well-organized, reusable, each in its own folder.
- **State**: Local state plus Context API for shared data.
- **Routing**: Protected routes with lazy loading for performance.
- **Performance**: Code splitting, memoization, CDN delivery.
- **Testing**: Unit, integration, and end-to-end tests with automated checks.

By following these guidelines, everyone on the team can build, maintain, and scale the **brain2train1.1** frontend in a consistent, efficient way. Feel free to reference this document whenever you need a quick reminder of our processes, patterns, and tools.