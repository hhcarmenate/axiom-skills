---
name: Material UI
description: >
  Material Design with MUI v6: component library with theming, sx prop, and dark mode support.
  Trigger: Building Material Design UIs with MUI components in React applications.
license: MIT
metadata:
  author: Henry Carmenate
  version: "1.0"
  applies-to:
    - claude-code
    - cursor
    - codex
---

## When to Use
Use this skill when:
- Building React UIs following Material Design principles
- Using pre-built MUI components (Button, Card, Dialog, TextField, etc.)
- Implementing custom theming and dark mode
- Using the sx prop for responsive, theme-aware inline styles
- Creating data tables, forms, and complex layouts
- Ensuring accessibility with semantic HTML and ARIA

## Critical Patterns

### Pattern 1: Component API with Props
MUI components expose common props: variant, size, color, fullWidth, disabled.

```jsx
import { Button, TextField, Card, CardContent, Box } from '@mui/material';

export function ComponentExamples() {
  return (
    <Box sx={{ p: 2 }}>
      {/* Buttons with variants and sizes */}
      <Button variant="contained" color="primary">
        Contained Primary
      </Button>
      <Button variant="outlined" color="secondary" size="small">
        Outlined Small
      </Button>
      <Button variant="text" disabled>
        Disabled Text
      </Button>

      {/* TextField with variants and states */}
      <TextField
        label="Email"
        type="email"
        variant="outlined"
        fullWidth
        margin="normal"
        error
        helperText="Invalid email format"
      />

      {/* Card as container */}
      <Card>
        <CardContent>
          <h2>Card Title</h2>
          <p>Card content goes here</p>
        </CardContent>
      </Card>
    </Box>
  );
}
```

### Pattern 2: Theming with createTheme
Customize colors, typography, and component styles globally.

```jsx
import { createTheme, ThemeProvider } from '@mui/material/styles';
import CssBaseline from '@mui/material/CssBaseline';

const theme = createTheme({
  palette: {
    primary: {
      main: '#0ea5e9',
      light: '#06b6d4',
      dark: '#0284c7',
    },
    secondary: {
      main: '#ec4899',
      light: '#f472b6',
      dark: '#db2777',
    },
    success: {
      main: '#10b981',
    },
    error: {
      main: '#ef4444',
    },
    warning: {
      main: '#f59e0b',
    },
    background: {
      default: '#f3f4f6',
      paper: '#ffffff',
    },
    text: {
      primary: '#1f2937',
      secondary: '#6b7280',
      disabled: '#d1d5db',
    },
    mode: 'light',
  },
  typography: {
    fontFamily: '"Inter", sans-serif',
    h1: {
      fontSize: '2.5rem',
      fontWeight: 700,
      lineHeight: 1.2,
    },
    h2: {
      fontSize: '2rem',
      fontWeight: 600,
    },
    body1: {
      fontSize: '1rem',
      lineHeight: 1.5,
    },
    body2: {
      fontSize: '0.875rem',
      lineHeight: 1.5,
    },
  },
  shape: {
    borderRadius: 8,
  },
  components: {
    MuiButton: {
      styleOverrides: {
        root: {
          textTransform: 'none',
          fontWeight: 500,
          borderRadius: 6,
        },
      },
    },
    MuiTextField: {
      defaultProps: {
        variant: 'outlined',
      },
    },
  },
});

export function App() {
  return (
    <ThemeProvider theme={theme}>
      <CssBaseline />
      <YourApp />
    </ThemeProvider>
  );
}
```

### Pattern 3: sx Prop for Responsive, Theme-Aware Styles
Use sx prop instead of inline styles; it has access to theme values.

```jsx
import { Box, Typography, Button } from '@mui/material';

export function ResponsiveLayout() {
  return (
    <Box
      sx={{
        // Responsive padding
        p: { xs: 1, sm: 2, md: 3, lg: 4 },
        
        // Responsive display
        display: 'flex',
        flexDirection: { xs: 'column', md: 'row' },
        
        // Theme colors
        backgroundColor: 'background.default',
        color: 'text.primary',
        
        // Conditional styles
        '&:hover': {
          backgroundColor: 'action.hover',
        },
        
        // Media queries
        '@media (max-width: 600px)': {
          padding: '8px',
        },
      }}
    >
      <Box sx={{ flex: 1, mr: { xs: 0, md: 2 }, mb: { xs: 2, md: 0 } }}>
        <Typography variant="h2">Responsive Layout</Typography>
      </Box>

      <Box sx={{ display: 'flex', gap: 1, alignItems: 'center' }}>
        <Button variant="contained">Action</Button>
      </Box>
    </Box>
  );
}
```

### Pattern 4: Dark Mode Support
Implement light and dark themes with mode toggle.

```jsx
import { createTheme, ThemeProvider, useMediaQuery } from '@mui/material';
import { useMemo, useState } from 'react';

export function DarkModeApp() {
  const prefersDarkMode = useMediaQuery('(prefers-color-scheme: dark)');
  const [darkMode, setDarkMode] = useState(prefersDarkMode);

  const theme = useMemo(
    () => createTheme({
      palette: {
        mode: darkMode ? 'dark' : 'light',
        primary: {
          main: '#0ea5e9',
        },
        background: {
          default: darkMode ? '#111827' : '#f3f4f6',
          paper: darkMode ? '#1f2937' : '#ffffff',
        },
      },
    }),
    [darkMode]
  );

  return (
    <ThemeProvider theme={theme}>
      <CssBaseline />
      <AppContent onToggleDarkMode={() => setDarkMode(!darkMode)} />
    </ThemeProvider>
  );
}

function AppContent({ onToggleDarkMode }) {
  return (
    <Box sx={{ p: 2 }}>
      <Button onClick={onToggleDarkMode}>Toggle Dark Mode</Button>
    </Box>
  );
}
```

### Pattern 5: Forms with TextField and Validation
Build accessible forms with validation and error messages.

```jsx
import { useForm, Controller } from 'react-hook-form';
import { TextField, Button, Box, Alert } from '@mui/material';
import { z } from 'zod';
import { zodResolver } from '@hookform/resolvers/zod';

const schema = z.object({
  name: z.string().min(1, 'Name is required'),
  email: z.string().email('Invalid email address'),
  message: z.string().min(10, 'Message must be at least 10 characters'),
});

export function ContactForm() {
  const { control, handleSubmit, formState: { errors }, watch } = useForm({
    resolver: zodResolver(schema),
    defaultValues: {
      name: '',
      email: '',
      message: '',
    },
  });

  const onSubmit = (data) => {
    console.log('Form submitted:', data);
  };

  return (
    <Box sx={{ maxWidth: 600, mx: 'auto', py: 4 }}>
      <form onSubmit={handleSubmit(onSubmit)}>
        <Controller
          name="name"
          control={control}
          render={({ field }) => (
            <TextField
              {...field}
              label="Your Name"
              fullWidth
              margin="normal"
              error={!!errors.name}
              helperText={errors.name?.message}
            />
          )}
        />

        <Controller
          name="email"
          control={control}
          render={({ field }) => (
            <TextField
              {...field}
              label="Email Address"
              type="email"
              fullWidth
              margin="normal"
              error={!!errors.email}
              helperText={errors.email?.message}
            />
          )}
        />

        <Controller
          name="message"
          control={control}
          render={({ field }) => (
            <TextField
              {...field}
              label="Message"
              multiline
              rows={4}
              fullWidth
              margin="normal"
              error={!!errors.message}
              helperText={errors.message?.message}
            />
          )}
        />

        <Box sx={{ mt: 3 }}>
          <Button type="submit" variant="contained" fullWidth>
            Send Message
          </Button>
        </Box>
      </form>
    </Box>
  );
}
```

## Folder Structure
```
src/
├── theme/
│   ├── index.js                   # createTheme configuration
│   ├── palette.js                 # Color definitions
│   └── typography.js              # Font settings
├── components/
│   ├── Button/
│   │   └── Button.jsx
│   ├── Card/
│   │   └── Card.jsx
│   ├── Form/
│   │   ├── TextField.jsx
│   │   └── Form.jsx
│   └── Layout/
│       ├── Header.jsx
│       └── Sidebar.jsx
├── pages/
│   ├── HomePage.jsx
│   └── DashboardPage.jsx
└── App.jsx
```

## Key Principles
- **Component-based**: Use MUI components for consistency and accessibility.
- **Theming**: Customize globally via createTheme; avoid inline styles when possible.
- **sx prop for responsive**: Use sx prop for theme-aware, responsive styles.
- **Variant and size props**: Control appearance via variant (contained, outlined, text) and size (small, medium, large).
- **Accessibility**: MUI components include proper ARIA and semantic HTML.
- **Dark mode**: Use mode: 'dark' in theme; respect prefers-color-scheme.
- **Composition**: Build complex UIs by composing simple components.
- **Type safety**: MUI is fully typed; use TypeScript for better DX.

## Commands & Setup
```bash
# Install MUI and dependencies
npm install @mui/material @emotion/react @emotion/styled

# For icons
npm install @mui/icons-material

# For form validation
npm install react-hook-form @hookform/resolvers zod

# Start development
npm run dev

# Build for production
npm run build
```
