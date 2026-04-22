---
name: shadcn/ui
description: >
  shadcn/ui: copy-paste headless components built on Radix UI and Tailwind CSS for customizable, accessible UIs.
  Trigger: Building accessible, customizable React components with shadcn/ui.
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
- Building React UIs with copy-paste, customizable components
- Using headless components (no built-in styling assumptions)
- Combining Radix UI (accessibility) with Tailwind CSS (styling)
- Creating forms with React Hook Form and Zod validation
- Building accessible dialogs, dropdowns, popovers, and tooltips
- Needing full control over component styling

## Critical Patterns

### Pattern 1: Installing and Using Components
Copy-paste components directly into your project.

```bash
# Install shadcn/ui CLI
npm install -D shadcn-ui

# Add individual components
npx shadcn-ui@latest add button
npx shadcn-ui@latest add card
npx shadcn-ui@latest add input
npx shadcn-ui@latest add form
npx shadcn-ui@latest add dialog
npx shadcn-ui@latest add dropdown-menu

# Components are copied to components/ui/
```

```jsx
// components/ui/button.jsx (example - auto-generated)
import * as React from "react"
import { Slot } from "@radix-ui/react-slot"
import { cn } from "@/lib/utils"

const Button = React.forwardRef(({ className, variant = "default", size = "default", asChild = false, ...props }, ref) => {
  const Comp = asChild ? Slot : "button"
  return (
    <Comp
      className={cn(
        "inline-flex items-center justify-center rounded-md text-sm font-medium transition-colors",
        // Variants...
        className
      )}
      ref={ref}
      {...props}
    />
  )
})

Button.displayName = "Button"
export { Button }
```

### Pattern 2: Basic Component Usage
Use shadcn/ui components like any other React component.

```jsx
import { Button } from "@/components/ui/button"
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card"
import { Input } from "@/components/ui/input"

export function Dashboard() {
  return (
    <div className="space-y-6">
      {/* Card component */}
      <Card>
        <CardHeader>
          <CardTitle>Welcome</CardTitle>
          <CardDescription>Manage your account settings</CardDescription>
        </CardHeader>
        <CardContent>
          <div className="space-y-4">
            <Input placeholder="Enter your name" />
            <Button onClick={() => alert('Saved!')}>Save Changes</Button>
          </div>
        </CardContent>
      </Card>
    </div>
  )
}
```

### Pattern 3: Forms with React Hook Form Integration
Combine shadcn/ui inputs with React Hook Form for type-safe forms.

```jsx
import { useForm, Controller } from "react-hook-form"
import { zodResolver } from "@hookform/resolvers/zod"
import { z } from "zod"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { Textarea } from "@/components/ui/textarea"
import {
  Form,
  FormControl,
  FormDescription,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form"

const formSchema = z.object({
  email: z.string().email("Invalid email address"),
  password: z.string().min(8, "Password must be at least 8 characters"),
  message: z.string().min(10, "Message must be at least 10 characters"),
})

type FormValues = z.infer<typeof formSchema>

export function LoginForm() {
  const form = useForm<FormValues>({
    resolver: zodResolver(formSchema),
    defaultValues: {
      email: "",
      password: "",
      message: "",
    },
  })

  async function onSubmit(values: FormValues) {
    try {
      const response = await fetch("/api/login", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(values),
      })
      if (!response.ok) throw new Error("Login failed")
      const data = await response.json()
      console.log("Login successful:", data)
    } catch (error) {
      console.error("Form error:", error)
    }
  }

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-6 max-w-md">
        <FormField
          control={form.control}
          name="email"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Email</FormLabel>
              <FormControl>
                <Input placeholder="john@example.com" {...field} />
              </FormControl>
              <FormDescription>Your login email address</FormDescription>
              <FormMessage />
            </FormItem>
          )}
        />

        <FormField
          control={form.control}
          name="password"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Password</FormLabel>
              <FormControl>
                <Input type="password" {...field} />
              </FormControl>
              <FormMessage />
            </FormItem>
          )}
        />

        <FormField
          control={form.control}
          name="message"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Message</FormLabel>
              <FormControl>
                <Textarea placeholder="Your message..." {...field} />
              </FormControl>
              <FormMessage />
            </FormItem>
          )}
        />

        <Button type="submit" className="w-full">Sign In</Button>
      </form>
    </Form>
  )
}
```

### Pattern 4: Dialog and Dropdown Components
Build accessible dialogs and dropdowns with Radix UI under the hood.

```jsx
import { useState } from "react"
import { Button } from "@/components/ui/button"
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogHeader,
  DialogTitle,
  DialogTrigger,
} from "@/components/ui/dialog"
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu"

export function DialogAndDropdown() {
  const [open, setOpen] = useState(false)

  return (
    <div className="space-y-4">
      {/* Dialog */}
      <Dialog open={open} onOpenChange={setOpen}>
        <DialogTrigger asChild>
          <Button variant="outline">Open Dialog</Button>
        </DialogTrigger>
        <DialogContent>
          <DialogHeader>
            <DialogTitle>Confirm Action</DialogTitle>
            <DialogDescription>
              Are you sure you want to proceed? This action cannot be undone.
            </DialogDescription>
          </DialogHeader>
          <div className="flex gap-2 justify-end">
            <Button variant="outline" onClick={() => setOpen(false)}>Cancel</Button>
            <Button onClick={() => setOpen(false)}>Confirm</Button>
          </div>
        </DialogContent>
      </Dialog>

      {/* Dropdown Menu */}
      <DropdownMenu>
        <DropdownMenuTrigger asChild>
          <Button variant="outline">Options</Button>
        </DropdownMenuTrigger>
        <DropdownMenuContent>
          <DropdownMenuItem onClick={() => console.log("Edit")}>
            Edit
          </DropdownMenuItem>
          <DropdownMenuItem onClick={() => console.log("Delete")}>
            Delete
          </DropdownMenuItem>
          <DropdownMenuItem onClick={() => console.log("Share")}>
            Share
          </DropdownMenuItem>
        </DropdownMenuContent>
      </DropdownMenu>
    </div>
  )
}
```

### Pattern 5: Customizing Components
Modify copied components to match your design system.

```jsx
// components/ui/button.jsx - Customize colors and styles
import * as React from "react"
import { cn } from "@/lib/utils"

const Button = React.forwardRef(({ className, variant = "default", size = "default", ...props }, ref) => {
  const variants = {
    default: "bg-blue-600 text-white hover:bg-blue-700",
    destructive: "bg-red-600 text-white hover:bg-red-700",
    outline: "border border-gray-300 hover:bg-gray-50",
    ghost: "hover:bg-gray-100",
  }

  const sizes = {
    default: "h-10 px-4 py-2",
    sm: "h-9 px-3 text-sm",
    lg: "h-11 px-8 text-lg",
    icon: "h-10 w-10",
  }

  return (
    <button
      className={cn(
        "inline-flex items-center justify-center rounded-md text-sm font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-offset-2 disabled:opacity-50 disabled:cursor-not-allowed",
        variants[variant],
        sizes[size],
        className
      )}
      ref={ref}
      {...props}
    />
  )
})

Button.displayName = "Button"
export { Button }
```

## Folder Structure
```
src/
├── components/
│   ├── ui/                      # shadcn/ui components (auto-generated)
│   │   ├── button.jsx
│   │   ├── card.jsx
│   │   ├── input.jsx
│   │   ├── form.jsx
│   │   ├── dialog.jsx
│   │   └── dropdown-menu.jsx
│   ├── MyForm.jsx               # Your custom components using ui/
│   └── Dashboard.jsx
├── lib/
│   └── utils.ts                 # cn() utility for classNames
└── App.jsx
```

## Key Principles
- **Copy-paste, not npm**: Components are pasted into your project, not imported from npm. You own and can modify them.
- **Headless + Styled**: Radix UI handles accessibility and behavior; Tailwind handles styling. You control both.
- **Fully customizable**: Modify any component after copying; it's your code.
- **Accessibility first**: All components follow WAI-ARIA patterns out of the box.
- **Tailwind required**: shadcn/ui is built on Tailwind; you need it configured.
- **Type-safe with TypeScript**: Components are fully typed.
- **Small bundle**: Only components you use are included; no unused code.
- **React Hook Form integration**: Forms work seamlessly with React Hook Form + Zod.

## Commands & Setup
```bash
# Create a new project (with shadcn/ui preset)
npx create-next-app@latest my-app --typescript --tailwind

# Or add to existing project
npm install -D shadcn-ui

# Initialize shadcn/ui
npx shadcn-ui@latest init

# Add components as needed
npx shadcn-ui@latest add button
npx shadcn-ui@latest add form
npx shadcn-ui@latest add input
npx shadcn-ui@latest add card
npx shadcn-ui@latest add dialog

# View all available components
npx shadcn-ui@latest list
```
