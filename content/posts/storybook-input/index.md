---
title: Storybook Input
description: Creating custom input component with Storybook
date: 2023-08-31
draft: false
slug: /blog/storybook-input
tags:
  - Storybook
  - React
---

# Introduction

In the realm of modern web development, crafting interfaces that resonate with users is an art. Among the building blocks, input components play a pivotal role in user interactions. However, the demand often extends beyond standard offerings. This is where the strength of custom reusable input components shines.

Imagine having input fields tailored to your application's uniqueness—each element carefully designed to align with your brand and user expectations. In this exploration, we'll unlock the advantages of creating bespoke input components. We'll discover how customization not only enhances visual appeal but also boosts usability and adaptability.

Get ready to delve into the world of creating custom input components that elevate your application's user experience, all while reaping the benefits of reusability and tailored design.

## Stack

- [Next.js](https://nextjs.org/)
- [React-hook-form](https://react-hook-form.com/)
- [Storybook](https://storybook.js.org/)
- [TypeScript](https://www.typescriptlang.org/)
- [Tailwind CSS](https://tailwindcss.com/)
- [cn util function](https://klaudiuszb.me/blog/storybook-tailwind)

# Getting started

Let's take a look at the final result, and then we'll dive into the details:

```tsx:title=stories/Input.tsx
'use client';

import Image from 'next/image';
import Link from 'next/link';
import { useState } from 'react';
import { useFormContext } from 'react-hook-form';
import { cn } from 'utils/styles';
import Label from './Label';
import PasswordToggle from './PasswordToggle';

interface InputProps {
  name: string;
  type: string;
  placeholder: string;
  icon: string;
  userNotFound?: boolean;
  emailAlreadyInUse?: boolean;
  tooManyLoginAttempts?: boolean;
  wrongPassword?: boolean;
  isPassword?: boolean;
  passwordReminder?: boolean;
  value?: string;
}

export default function InputComponent({
  name,
  type,
  placeholder,
  userNotFound = false,
  emailAlreadyInUse = false,
  tooManyLoginAttempts = false,
  wrongPassword = false,
  isPassword = false,
  icon,
  passwordReminder,
  ...inputProps
}: InputProps) {
  const {
    register,
    formState: { errors },
  } = useFormContext();
  const [typeState, setTypeState] = useState(type);
  const fieldError = errors[name];
  const errorsMap = new Map([
    ['userNotFound', userNotFound],
    ['emailAlreadyInUse', emailAlreadyInUse],
    ['tooManyLoginAttempts', tooManyLoginAttempts],
    ['wrongPassword', wrongPassword],
  ]);
  const error = Array.from(errorsMap).find(([, value]) => value === true)?.[0];
  return (
    <div className="w-full flex flex-col gap-1">
      <div className="w-full h-full relative text-[#3B434B] text-sm">
        <div className="absolute inset-0 pointer-events-none">
          <Image
            src={`/icons/${icon}`}
            alt="icon"
            width={16}
            height={16}
            className="absolute left-5 top-[53%] -translate-y-1/2"
          />
        </div>
        <input
          className={cn(
            'bg-[#F2F2F4] py-[15px] px-[16px] pl-12 rounded-xl w-full focus:outline-none',
            {
              'bg-[#A33B361A] border border-[#A33B36]': error || fieldError,
            },
          )}
          {...register(`${name}`)}
          type={typeState}
          placeholder={placeholder}
          {...inputProps}
        />
        {isPassword && (
          <PasswordToggle type={typeState} setType={setTypeState} />
        )}
      </div>
      <Label fieldError={fieldError} name={name} error={error} />
      {passwordReminder && (
        <Link href="/forgot-password" className="text-sm text-[#3B434B]">
          Forgot Password?
        </Link>
      )}
    </div>
  );
}
```

Our Input also uses two other components: `Label` and `PasswordToggle`.

```tsx:title=stories/Label.tsx
import { FieldError, FieldErrorsImpl } from 'react-hook-form/dist/types/errors';
import { IFormValues } from '@stories/Form/Form';
import { Merge } from 'react-hook-form/dist/types/utils';

interface ILabelProps {
  fieldError:
    | FieldError
    | Merge<FieldError, FieldErrorsImpl<IFormValues>>
    | undefined;
  name: string;
  error: string | undefined;
}

const errorsConfig = {
  userNotFound: 'User not found.',
  emailAlreadyInUse: 'Email already taken.',
  tooManyLoginAttempts: 'Too many login attempts. Please try again in a few minutes.',
  wrongPassword: 'Wrong password. Please try again or use the "Forgot Password" option.',
};
const errors = new Map(Object.entries(errorsConfig));

export default function Label({ fieldError, name, error }: ILabelProps) {
  return (
    <label htmlFor={name} className="text-[#A33B36] text-xs">
        {fieldError && fieldError?.message}
        {error !== undefined && errors.get(error)}
    </label>
  );
}
```

```tsx:title=stories/PasswordToggle.tsx
import Image from 'next/image';
import { Dispatch, SetStateAction } from 'react';

interface IProps {
  setType: Dispatch<SetStateAction<string>>
  type: string;
}

export default function PasswordInput({ setType, type }: IProps) {
  const handlePasswordVisibility = () => {
    type === 'password' ? setType('text') : setType('password');
  };
  const icon = type === 'password' ? 'eye.svg' : 'eye-off.svg';
  return (
    <Image
      src={`/icons/${icon}`}
      alt="eye icon"
      width={16}
      height={16}
      className='absolute right-5 top-[53%] -translate-y-1/2 cursor-pointer'
      onClick={handlePasswordVisibility}
    />
  );
}
```

Now let's explain the code above. First we created the possible props that our Input component can receive:

```tsx:title=stories/Input.tsx
interface InputProps {
  name: string;
  type: string;
  placeholder: string;
  icon: string;
  userNotFound?: boolean;
  emailAlreadyInUse?: boolean;
  tooManyLoginAttempts?: boolean;
  wrongPassword?: boolean;
  isPassword?: boolean;
  passwordReminder?: boolean;
  value?: string;
}
```

I used this Input component in a forms to sign in and sign up users of course You can adjust props to Your needs or add more to satisfy the needs of your application.
We'll dive into creating a custom form in another article.
Input also uses form context which comes from react-hook-form, then we have a state for our password input and a function to toggle the visibility of the password. We also have a fieldError which comes from react-hook-form and an error which is a string that we pass to our Input component to display a specific error message to handle the errors that come from our backend.

```tsx:title=stories/Input.tsx
const {
    register,
    formState: { errors },
  } = useFormContext();
  const [typeState, setTypeState] = useState(type);
  const fieldError = errors[name];
  const errorsMap = new Map([
    ['userNotFound', userNotFound],
    ['emailAlreadyInUse', emailAlreadyInUse],
    ['tooManyLoginAttempts', tooManyLoginAttempts],
    ['wrongPassword', wrongPassword],
  ]);
  const error = Array.from(errorsMap).find(([, value]) => value === true)?.[0];
```

I like using Maps to create configs and depending on props or values I can display a specific error message. In this case I used a Map to pass which error should be displayed to the Label component.

`<Label fieldError={fieldError} name={name} error={error} />`

In Label component we create another map with proper error messages and display them based on the error prop that we pass to the Input component.

```tsx:title=stories/Label.tsx
const errorsConfig = {
  userNotFound: 'User not found.',
  emailAlreadyInUse: 'Email already taken.',
  tooManyLoginAttempts: 'Too many login attempts. Please try again in a few minutes.',
  wrongPassword: 'Wrong password. Please try again or use the "Forgot Password" option.',
};
const errors = new Map(Object.entries(errorsConfig));

export default function Label({ fieldError, name, error }: ILabelProps) {
  return (
    <label htmlFor={name} className="text-[#A33B36] text-xs">
      <>
        {fieldError && fieldError?.message}
        {error !== undefined && errors.get(error)}
      </>
    </label>
  );
}
```

# Storybook

To create a story for our Input component we need to create a file Input.stories.tsx in our stories folder.

```tsx:title=stories/Input.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import Input from './Input';
import MockFormProvider from './MockFormProvider';

// More on how to set up stories at: https://storybook.js.org/docs/react/writing-stories/introduction#default-export
const meta = {
  title: 'Example/Input',
  component: Input,
  parameters: {
    // Optional parameter to center the component in the Canvas. More info: https://storybook.js.org/docs/react/configure/story-layout
    layout: 'fullscreen',
  },
  // This component will have an automatically generated Autodocs entry: https://storybook.js.org/docs/react/writing-docs/autodocs
  tags: ['autodocs'],
  // More on argTypes: https://storybook.js.org/docs/react/api/argtypes
  argTypes: {},
} satisfies Meta<typeof Input>;

export default meta;
type Story = StoryObj<typeof meta>;

// More on writing stories with args: https://storybook.js.org/docs/react/writing-stories/args
export const Default: Story = {
  args: {
    name: 'Input',
    placeholder: 'Enter your email',
    type: 'text',
    icon: 'envelope.svg',
  },
  render: (args) => (
    <MockFormProvider>
      <Input {...args} />
    </MockFormProvider>
  ),
  parameters: {
    layout: 'centered',
  },
};

export const UserNotFound: Story = {
  args: {
    name: 'Input',
    placeholder: 'Enter your email',
    value: 'kb@test.pl',
    type: 'text',
    icon: 'envelope.svg',
    userNotFound: true,
  },
  render: (args) => (
    <MockFormProvider>
      <Input {...args} />
    </MockFormProvider>
  ),
  parameters: {
    layout: 'centered',
  },
};

export const Password: Story = {
  args: {
    name: 'Input',
    placeholder: 'Enter your password',
    type: 'password',
    isPassword: true,
    icon: 'lock.svg',
  },
  render: (args) => (
    <MockFormProvider>
      <Input {...args} />
    </MockFormProvider>
  ),
  parameters: {
    layout: 'centered',
  },
};

export const WrongPassword: Story = {
  args: {
    name: 'Input',
    placeholder: 'Enter your password',
    value: '123456',
    type: 'password',
    isPassword: true,
    icon: 'lock.svg',
    wrongPassword: true,
  },
  render: (args) => (
    <MockFormProvider>
      <Input {...args} />
    </MockFormProvider>
  ),
  parameters: {
    layout: 'centered',
  },
};
```

Because we are using react-hook-form's useFormContext we need to create a mock form provider to wrap our Input component in it.

```tsx:title=stories/MockFormProvider.tsx
import { FormProvider, useForm } from 'react-hook-form';

import React from 'react';

interface IFormProps {
  children: React.ReactNode;
}

const MockFormProvider = ({ children }: IFormProps) => {
  const methods = useForm();
  return (
    <FormProvider {...methods}>
      <form>{children}</form>
    </FormProvider>
  );
};

export default MockFormProvider;`
```

Now we created a story for our Input component. Of course we can create more stories for different props that we pass to our Input component. We can also create a story for our Label component and PasswordToggle component but I'll leave it to You.

# Conclusion

In this article, we've ventured into the realm of creating a custom Input component that can seamlessly integrate into our forms. By delving into the intricacies of its design and functionality, we've not only enhanced our understanding of building user-friendly interfaces but also learned the art of crafting tailored components that align perfectly with our application's needs.

But our journey doesn't end here. In the upcoming article, we'll harness the power of our custom Input component to create a bespoke form. This form will serve as the cornerstone for user sign-in and sign-up processes, providing a cohesive and engaging experience for our application's users.

Stay tuned as we continue to explore the world of web development, empowering ourselves with the tools and knowledge to create exceptional user interfaces.
