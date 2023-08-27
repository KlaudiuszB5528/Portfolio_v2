---
title: Storybook with Tailwind
description: How to integrate Storybook with Tailwind CSS
date: 2023-08-20
draft: false
slug: /blog/storybook-tailwind
tags:
  - Storybook
  - CSS
---

# Install @storybook/addon-styling

Add the _@storybook/addon-styling_ package to your DevDependencies

`npm install -D @storybook/addon-styling`<br>
#or<br>
`yarn add -D @storybook/addon-styling`

## Auto-config

📣 Before running this codemod, ensure that you have no other changes in your git branch.

As of version 1.3, @storybook/addon-styling offers a codemod to automatically configure your storybook with Tailwind.

To give it a try, run the following script:

`yarn addon-styling-setup`

## Util function

Overwritting Tailwind classess doesn't always end as expected therefore you can use two libraries to help you with that:

- [tailwind-merge](https://www.npmjs.com/package/tailwind-merge)
- [clsx](https://www.npmjs.com/package/clsx)

Then you can create a util function to use both libraries:

```javascript:title=src/utils/cn.ts
import { clsx, ClassValue } from 'clsx';
import { twMerge } from 'tailwind-merge';

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

Tailwind-merge will ensure that classess are merge correctly and clsx will help you to use conditional classess.<br>
With clsx you can use an object to define the classess you want to use:

```javascript:title=src/stories/Button.tsx
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
    },
      )}
  {...props}
>
```

In the example above, the classess defined in the object will only be added if the condition is true.<br>
`w-full` will only be added if `fullWidth` is true.<br>
`mx-auto` will only be added if `centered` is true.

## Resources

- [Storybook Docs](https://storybook.js.org/recipes/tailwindcss#install-storybookaddon-styling)
- [Shadcn/ui](https://shadcn.com/)
