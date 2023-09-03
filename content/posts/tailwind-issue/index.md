---
title: Tailwind Issue
description: Resolving an issue with Tailwind CSS
date: 2023-09-03
draft: false
slug: /blog/tailwind-issue
tags:
  - Tailwind
---

# Introduction

While working on adding functionality to a custom button component, I encountered an issue where dynamically assigned classes were being overwritten by Tailwind CSS's base styles.
[Github issue](https://github.com/tailwindlabs/tailwindcss/discussions/5969) where people are discussing the same problem. The solution they provide is to disable preflight in tailwind config, but I didn't want to do that.

```tsx:title=src/stories/Button.tsx
<button
  type={type}
  disabled={isLoading}
  className={cn(
    baseStyles,
    sizes.get(size),
    roundedSizes.get(rounded),
    classes,
    {
      'w-full': fullWidth,
      'mx-auto': centered,
      //here is the issue
      'bg-gray-300 text-black': disabled,
    },
      )}
  {...props}
>
```

Instead of applying the expected disabled styles, the button ended up with the base styles. I attempted to resolve this by using the `!important` flag, but it proved ineffective.

# Solution

The solution I eventually found involved creating a custom class in the Tailwind CSS config file and using it in place of inline styles.

```css:title=globals.css
@layer components{
  .disabled {
    @apply bg-gray-300 text-black;
  }
}
```

```tsx:title=src/stories/Button.tsx
<button
  type={type}
  disabled={isLoading}
  className={cn(
    baseStyles,
    sizes.get(size),
    roundedSizes.get(rounded),
    classes,
    {
      'w-full': fullWidth,
      'mx-auto': centered,
      //solution
      disabled,
    },
      )}
  {...props}
>
```
