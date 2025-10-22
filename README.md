# Studio Freight Dropdown Component

A Vue 3 implementation of a custom dropdown/select component with a drawer interface, created as a frontend development assignment for Studio Freight.

## Features

- **Custom Dropdown Component**: Fully accessible custom select with keyboard navigation
- **Drawer Interface**: Right-side sliding drawer with smooth animations
- **Responsive Design**: Mobile-friendly layout that adapts to different screen sizes
- **Accessibility**: ARIA attributes, focus management, and keyboard support
- **Animations**: Subtle GSAP-enhanced animations with CSS fallbacks
- **TypeScript**: Full TypeScript support for type safety

## Tech Stack

- Vue 3 with Composition API
- TypeScript
- Vue Router
- GSAP for animations
- Vite for build tooling

## Project Setup

```sh
npm install
```

### Development

```sh
npm run dev
```

### Build for Production

```sh
npm run build
```

### Type Checking

```sh
npm run type-check
```

### Linting

```sh
npm run lint
```

## Project Structure

```
src/
├── components/
│   ├── Drawer.vue          # Right-side drawer component
│   └── CustomSelect.vue    # Custom dropdown/select component
├── views/
│   ├── HomeView.vue        # Landing page
│   ├── AboutView.vue       # About page
│   └── ContactView.vue     # Contact page with drawer form
├── utils/
│   └── animations.ts       # GSAP animation utilities
├── router/
│   └── index.ts            # Vue Router configuration
├── assets/
│   └── main.css            # Global styles
└── App.vue                 # Root component with navigation
```

## Component Features

### CustomSelect Component

- Keyboard navigation (Arrow keys, Enter, Escape, Tab)
- ARIA attributes for screen readers
- Click outside to close
- Custom styling with smooth animations
- Option highlighting and selection

### Drawer Component

- Right-side slide-in animation
- Backdrop click to close
- Escape key to close
- Focus trap and restoration
- Responsive design

## Deployment

### Vercel

1. Connect your repository to Vercel
2. Vercel will automatically detect the Vue.js framework
3. Configure build settings:
   - **Build Command**: `npm run build`
   - **Output Directory**: `dist`
   - **Install Command**: `npm install`
4. Deploy!

### Netlify

1. Connect your repository to Netlify
2. Configure build settings:
   - **Build command**: `npm run build`
   - **Publish directory**: `dist`
3. Add environment variables if needed
4. Deploy!

### Manual Deployment

```sh
# Build the project
npm run build

# The dist/ folder contains the production-ready files
# Upload this folder to your hosting provider
```

## Development Notes

- The project uses CSS animations as primary animation method with GSAP for enhanced effects
- All components are fully accessible with proper ARIA attributes
- The implementation follows Vue 3 best practices with Composition API
- TypeScript is used throughout for better type safety and developer experience

## Browser Support

- Chrome/Edge (latest)
- Firefox (latest)
- Safari (latest)
- Mobile browsers

## License

MIT License - feel free to use this project for learning or reference.
