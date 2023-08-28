---
title: Storybook Component Config
description: Useful tip to configure Storybook components
date: 2023-08-22
draft: false
slug: /blog/storybook-component-config
tags:
  - Storybook
  - JS
---

# Introduction

To create customizable components using [Storybook](https://storybook.js.org/) with [Tailwind](https://tailwindcss.com/) you can prepare predefined variants and apply appropriate classes based on the passed props.<br> While the Storybook documentation suggests using a function with a switch statement to return the necessary classes, there's a more elegant approach.<br>
I prefer utilizing Maps to store classes and subsequently applying them within a function to ensure the correct classes are returned.<br> Here's a simple example illustrating this approach with a Button component::

```tsx:title=src/stories/Button.tsx
import { sizes } from './button.service';

interface ButtonProps {
  type?: 'button' | 'submit' | 'reset';
  size?: 'small' | 'medium' | 'large';
  label: string;
  onClick?: () => void;
}

export const Button = ({
  size = 'medium',
  label,
  ...props
}: ButtonProps) => {
  return (
    <button
      className="flex text-white bg-dark-gray"
      {...props}
    >
      {label}
    </button>
  );
};
```

And here's the service file with Maps:

```ts:title=src/stories/button.service.ts
const sizesConfig = {
  small: 'text-xs py-2 px-4 min-w-40',
  medium: 'text-sm py-3 px-6 w-52',
  large: 'text-lg py-4 px-8 w-64',
};
export const sizes = new Map(Object.entries(sizesConfig));
```

There are two main advantages of this approach:

- you can easily add new variants
- you can use it in other components

Of course there other ways to create a Map, for example:

```javascript
const sizes = new Map();
sizes.set('small', 'text-xs py-2 px-4 min-w-40');
sizes.set('medium', 'text-sm py-3 px-6 w-52');
sizes.set('large', 'text-lg py-4 px-8 w-64');
```

Or even:

```javascript
const sizesConfig = [
  ['small', 'text-xs py-2 px-4 min-w-40'],
  ['medium', 'text-sm py-3 px-6 w-52'],
  ['large', 'text-lg py-4 px-8 w-64'],
];

const sizes = new Map(sizesConfig);
```

but I prefer to use the one with the object because it's more readable.

# Usage

Now that you've created the Map, you can use it in your component:

```tsx:title=src/stories/Button.tsx
export const Button = ({
  size = 'medium',
  label,
  ...props
}: ButtonProps) => {
  return (
    <button
      className="flex text-white bg-dark-gray"
      size={sizes.get(size)}
      {...props}
    >
      {label}
    </button>
  );
};
```

# Conclusion

In this article, we explored an elegant way to create customizable components using Storybook with Tailwind CSS. By utilizing Maps to store class configurations, we achieve a cleaner and more maintainable codebase. The benefits of this approach are two-fold: we can effortlessly incorporate new variants and easily reuse the solution in other components.

By adopting this technique, you're empowered to build components that are flexible, maintainable, and adaptable to changing design requirements. Experiment with this approach in your projects and see how it simplifies your styling workflow while enhancing code quality.
