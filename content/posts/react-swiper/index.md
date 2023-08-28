---
title: Swiper WebComponent
description: Configuring Swiper WebComponent with Next.js
date: 2023-08-28
draft: false
slug: /blog/swiper-web-component
tags:
  - Swiper
  - React
---

# Introduction

As Swiper's documentation indicates, Swiper React components are expected to be removed in future versions. To prepare for this transition, it's recommended to migrate to using Swiper Element (WebComponent). However, it's important to note that React's full support for web components is still in progress as of version 18. This transition presents certain challenges, one of which is encountered when trying to use Swiper components within JSX. TypeScript raises an error in such scenarios, but worry not—there's a solution. In this guide, we'll explore how to resolve this TypeScript issue and proceed with configuring and using the Swiper WebComponent in a Next.js application. To fix it we need to declare the components in `global.d.ts` file in our project root directory:

```ts:title=global.d.ts
import React from 'react';
import type { SwiperProps, SwiperSlideProps } from 'swiper/react';

declare global {
  namespace JSX {
    interface IntrinsicElements {
      'swiper-container': React.DetailedHTMLProps<
      React.HTMLAttributes<HTMLElement> & SwiperProps,
      HTMLElement >;
      'swiper-slide': React.DetailedHTMLProps<
      React.HTMLAttributes<HTMLElement> & SwiperSlideProps,
      HTMLElement >;
    }
  }
}
```

# Swiper Component

The next step is to create a component that will render the Swiper container and slides. I've created a separate file `swiper.service.tsx` to separate slides render and utility functions from the component itself. The component is pretty straightforward:

```tsx:title=src/components/SwiperComponent.tsx
'use client';
import 'swiper/css';
import 'swiper/css/pagination';

import { useEffect, useRef, useState } from 'react';
import {
ISwiperProps,
renderSwiperSlide,
updateParams,
} from './swiper.service';

import { register } from 'swiper/element/bundle';

register();

export default function SwiperComponent({
  slidesData,
}: ISwiperProps) {
const swiperRef = useRef(null);

useEffect(() => {
  // we need to initialize swiper after the component is mounted
const swiperContainer = swiperRef.current;
    // since support for web components is not fully implemented in React we need to initialize swiper manually and update it's params
    if (swiperContainer) {
      const params = updateParams(breakpoint);
      Object.assign(swiperContainer, params);
    }
    // swiper is still missing proper typing so we need to use @ts-expect-error (or @ts-ignore)
    // @ts-expect-error - swiperContainer is not null
    swiperContainer?.initialize();
}, []);

return (
  //we need to use ref to access the swiper container and set init to false for our custom params to work
<swiper-container ref={swiperRef} init={false}>
  {slidesData.map((slide) =>
    renderSwiperSlide(slide, handleClick),
  )}
</swiper-container>
);
}
```

And here's the service file with config and utility functions:

```tsx:title=src/components/swiper.service.tsx
import { IFilterEventsReturn, IFilterOffersReturn } from 'types';

import Card from './Card';
import CardDetails from './CardDetails';

export const updateParams = (windowWidth?: number) => {
  //creating params is similiar like in Swiper React component
const params = {
  breakpoints: {
    320: {
      slidesPerView: 1,
    },
    768:{
      slidesPerView: 2,
    },
    1024: {
    slidesPerView: 3,
    },
  },
  pagination: {
    clickable: true,
    dynamicBullets: true,
    },
  spaceBetween: 20,
  initialSlide: 1,
  //we can also inject styles, I've used it to push pagination below the slides, also noteworthy is that we need to use !important to override some default styles
  //for what classess are available check the documentation https://swiperjs.com/element#parts
  injectStyles: [
    `
    .swiper-wrapper{
    padding-bottom: 2rem;
    }
    swiper-slide,
    .swiper-slide{
    height: auto !important;
    max-width:375px !important;
    }
    `,
  ],
};
  return params;
};

export interface ISwiperProps {
  slidesData: IFilterOffersReturn[] | IFilterEventsReturn[];
  isDetailed?: boolean;
}
//I have two types of slides so I need to check which one is passed
//this is called type guard
export const isOffer = (
  slide: IFilterOffersReturn | IFilterEventsReturn,
): slide is IFilterOffersReturn => {
  return (slide as IFilterOffersReturn).title !== undefined;
};

export const renderSwiperSlide = (
  slide: IFilterOffersReturn | IFilterEventsReturn,
  onClickFunction: (id: string) => void,
  isDetailed?: boolean,
): React.JSX.Element => {
const handleClick = () => {
onClickFunction(slide.objectID);
};
return (
  <swiper-slide
      //I also need to pass different key if the slide is detailed because I'm using the same slide for both views and have them on the same page
      key={isDetailed ? `${slide.objectID}-detailed` : slide.objectID}
      onClick={handleClick} >
    <Card url={slide.img}>
      {isDetailed && isOffer(slide) && (
        <CardDetails
          title={slide.title}
          date={slide.date}
          time={slide.time}
          location={slide.city}
          price={slide.price}
        />
      )}
    </Card>
  </swiper-slide>
);
};
```

# Conclusion

By adopting the Swiper WebComponent in your Next.js project, you're ensuring the continued creation of engaging and interactive user interfaces. This integration empowers you to provide seamless slide-based content, enhancing the overall user experience. As you explore the capabilities of the Swiper WebComponent, you'll discover its potential to elevate the visual appeal and functionality of your web applications.
