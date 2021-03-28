---
title: Stop using css variables in media queries
description:
  Using clamp(), min() or max() instead of CSS Variables in Media Queries still makes a great responsive UX with a lot less code
date: 2021-01-07
author: Julio Soto
---


In the past any time I started a project one of the first things I tent to do
was to define my `cssVariables` file in my packages `lib` folder.

In this post I wanna emphasize how beneficial this is for your application. As
an example, we will defined `gap units` in Media Queries.

My file would normally have contained some information like this 👇🏻

```ts
// packages/lib/styles/global.ts

export const cssVariables = `
  :root {
    // GAP UNITS
    --gap-unit-xs: 0.5rem;
    --gap-unit-sm: 1rem;
    --gap-unit-md: 2rem;
    --gap-unit-lg: 4rem;
    --gap-unit-xl: 6rem;
    --grid-gap-unit: 10px;

    // GAP VARIABLES
    --gap: var(--gap-unit-xs);
    --gap-bottom-element: var(--gap-unit-sm);
    --gap-bottom-container: var(--gap-unit-lg);
    ...

    ${getMQ('small-up')} {
      --gap: var(--gap-unit-sm);
      ...
    }

    ${getMQ('tablet')} {
      --gap: var(--gap-unit-md);
      --gap-bottom-element: var(--gap-unit-md);
      ...
    }

    ${getMQ('desktop')} {
      --grid-gap-unit: 20px;
      --gap: var(--gap-unit-lg);

      ...
    }

    ${getMQ('panorama')} {
      --grid-gap-unit: 40px;
      --gap-bottom-container: var(--gap-unit-xl);
      ...
    }
  }
`;
```

After that setup, it becomes very easy to apply a standard responsive spacing
unit on every device.

For example if you have a `SectionHeader` component, this is how I go about
having the text well spaced and structured.

```jsx
import * as React from 'react';
import { css } from '@emotion/react';
import { getMQ } from '@lib/styles';

const styles = {
  root: css`
    margin-bottom: var(--gap-bottom-container);

    .label {
      font-weight: 700;
      text-transform: uppercase;
      margin-bottom: var(--gap-bottom-element);
    }

    .subtitle {
      color: var(--c-neutral);
      margin-bottom: var(--gap-bottom-element);
    }
  `,
};

interface SectionHeaderProps {
  label: JSX.Element;
  subtitle: JSX.Element;
const SectionHeader: React.FC<SectionHeaderProps> = ({ label, subtitle }) => (
  <div css={styles.root}>
    <h2 className="label">{label}</h2>
    <p className="subtitle small">{subtitle}</p>
  </div>
);

export default SectionHeader;
```

### 👋🏻 clamp(), min() and max() functions to the rescue

It is time to welcome the new utility functions.

Instead of the media queries mess, we can now achieve the same responsivenes
with the min() function.

The min() function takes two parameters or more: a minimum value and a maximum
allowed value, but you can put more arguments into these functions. Except for
the clamp() function which always takes three arguments.

```ts
// packages/lib/styles/global.ts

export const cssVariables = `
  :root {
    // GAP UNITS
    --gap-unit-xs: 0.5rem;
    --gap-unit-sm: 1rem;
    --gap-unit-md: 2rem;
    --gap-unit-lg: 4rem;
    --gap-unit-xl: 6rem;
    --grid-gap-unit: min(10px, 20px);

    // GAP VARIABLES
    --gap: min(var(--gap-unit-xs), 6vw, var(--gap-unit-xl));
    --gap-bottom-element: min(var(--gap-unit-sm), 8vw, var(--gap-unit-md));
    --gap-bottom-container: min(var(--gap-unit-lg), 12vw, var(--gap-unit-xl));
    ...
`;
```

And that is it 🎉 Hopefully now you will find the code cleaner and easier to
read as I do.

<br />
