---
title: Storybook Form
description: Creating custom form component with Storybook
date: 2023-09-01
draft: false
slug: /blog/storybook-form
tags:
  - Storybook
  - React
---

# Introduction

In our voyage through the realm of modern web development, we've already uncovered the art of creating bespoke [input](https://klaudiuszb.me/blog/storybook-input) components, each thoughtfully designed to align perfectly with your application's uniqueness. But what's a well-crafted input without an equally impressive form to house it?

In this installment, we embark on a journey to create a custom form that will serve as the canvas for our meticulously designed input fields. And here's the kicker: the star of this form is none other than the custom Input component we honed in our previous article.

Picture this: input fields tailored to your application's brand and user expectations, integrated seamlessly into a form that not only enhances visual appeal but also delivers a top-notch user experience. We're diving headfirst into the world of crafting custom forms with inputs that speak to the heart of your application.

Prepare to witness how the fusion of tailored design and functional reusability can elevate your user interface to new heights. This is where form meets function, where custom meets convenience. It's time to shape the future of your application's user interactions, one input at a time. Let's begin our journey into the art of crafting custom forms, powered by the exceptional Input component we've already crafted.

```tsx:title=Form.tsx
import * as yup from 'yup';

import { FormProvider, useForm } from 'react-hook-form';

import { yupResolver } from '@hookform/resolvers/yup';
import Input from '@stories/Input/Input';

interface IInput {
  name: string;
  type: string;
  placeholder: string;
  userNotFound?: boolean;
  emailAlreadyInUse?: boolean;
  tooManyLoginAttempts?: boolean;
  wrongPassword?: boolean;
  isPassword?: boolean;
  icon: string;
}

interface IInputs {
  inputs: IInput[];
}

export interface IFormValues {
  email?: string;
  password?: string;
}

interface IFormProps extends IInputs {
  onSubmit: (data: IFormValues) => void;
  schema: yup.AnyObjectSchema;
  className?: string;
  children?: React.ReactNode;
}

const Form = ({
  onSubmit,
  schema,
  inputs,
  className,
  children,
}: IFormProps) => {
  const methods = useForm({
    resolver: yupResolver(schema),
  });
  return (
    <FormProvider {...methods}>
      <form onSubmit={methods.handleSubmit(onSubmit)} className={className}>
        {inputs.map((input) => (
          <Input {...input} key={input.name} />
        ))}
        {children}
      </form>
    </FormProvider>
  );
};

export default Form;
```

Form Inputs

The inputs prop is an array of objects, each defining an input field. This array enables us to dynamically render input components within our form. Below is an example of how inputs might look:

```ts:title=form.service.ts

const inputs = [
  {
    name: 'email',
    type: 'text',
    placeholder: 'E-mail',
    icon: 'envelope.svg',
    emailAlreadyInUse: false,
    userNotFound: false,
    tooManyLoginAttempts: false,
  },
  {
    name: 'password',
    type: 'password',
    icon: 'lock.svg',
    placeholder: 'Hasło',
    wrongPassword: false,
    isPassword: true,
    passwordReminder: true,
  },
];
```

# Storybook

Now that we've dissected the components, let's see how to create a story for our Form component.

```tsx:title=Form.stories.tsx
import { Button } from '@stories/Button';
import type { Meta, StoryObj } from '@storybook/react';
import { schema } from 'utils/schemas/signInSchema';
import Form from './Form';
import { inputs, onSubmit } from './form.service';

// More on how to set up stories at: https://storybook.js.org/docs/react/writing-stories/introduction#default-export
const meta = {
  title: 'Example/Form',
  component: Form,
  parameters: {
    // Optional parameter to center the component in the Canvas. More info: https://storybook.js.org/docs/react/configure/story-layout
    layout: 'fullscreen',
  },
  // This component will have an automatically generated Autodocs entry: https://storybook.js.org/docs/react/writing-docs/autodocs
  tags: ['autodocs'],
  // More on argTypes: https://storybook.js.org/docs/react/api/argtypes
  argTypes: {},
} satisfies Meta<typeof Form>;

export default meta;
type Story = StoryObj<typeof meta>;

// More on writing stories with args: https://storybook.js.org/docs/react/writing-stories/args
export const Default: Story = {
  args: {
    onSubmit: onSubmit,
    inputs: inputs,
    schema: schema,
  },
  render: (args) => (
    <Form {...args} className="p-2 gap-2">
      <Button
        label="Zaloguj się"
        type="submit"
        classes="mt-2"
        isLoading={false}
        rounded="medium"
      />
    </Form>
  ),
  parameters: {
    layout: 'centered',
  },
};

```

In this example, we've created a sign-in form that incorporates our custom Input components. We've used inputs, onSubmit for form submission, and a validation schema (schema) to ensure data integrity.

This form serves as a prime illustration of how our custom Input components seamlessly fit into real-world scenarios, offering both aesthetics and functionality. You can similarly create forms for other purposes, such as user registration or data submission.

In the next phase of our journey, we'll expand on this foundation and explore the creation of additional custom forms to enhance the user experience within our application. Stay tuned for more!

# Conclusion

As we conclude our journey, we've not only learned the art of crafting custom input components but have also witnessed how these components can come together to form a cohesive and user-friendly interface. By creating a custom form and showcasing it in Storybook, we've unlocked the true potential of our tailored inputs.

Now, armed with the knowledge of creating both custom inputs and forms, you're well-prepared to shape the future of your web applications. The possibilities are endless, and the user experience is yours to craft. Happy coding!
