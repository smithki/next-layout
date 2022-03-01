# 🗺 `next-super-layout`

Next.js conveniently solves many of the headaches involved with modern React development. However, one of the fundamental pieces still _missing_ from Next.js is the ability to create clean, composable, data-infused layouts. [There is some advice to be found in Next.js docs](https://nextjs.org/docs/basic-features/layouts), but no sufficient out-of-the-box abstraction. This problem only worsens when component-level data becomes necessary anywhere in your application; at which point your only option is to "drill props" and deal with increasing amounts of `/pages` boilerplate. This project tries to solve the layouts problem with a simple, opinionated abstraction that plays nicely with existing Next.js conventions.

## 📦 Installation

Using NPM:

```zsh
npm install next-super-layout
```

Using Yarn:

```zsh
yarn add next-super-layout
```

## 🛠 Usage

Bootstrapping new layouts is a cinch using the `createLayout` function. Simply give your layout a `name`, describe some UI with `getLayout`, and fetch some initial props data using `getData`. Take a look:

```tsx
// layouts/my-layout.tsx
import { createLayout } from 'next-super-layout';

const myLayout = createLayout({
  name: 'myLayout', // choose something unique from amongst all your layouts

  getLayout: (page, data) => {
    // `page` is the React element being wrapped.
    // `data` is the data returned from the `getData` function.
    return (<>
      <MyHeader />
      {page}
      <MyFooter />
    </>);
  },

  getData: async (ctx) => {
    // `ctx` is the `GetStaticPropsContext` object passed to `getStaticProps`.
    return { ... };
  },
});
```

Once we've created a layout, we'll connect it to a Next.js page:

```tsx
// pages/some/path.tsx
import { myLayout } from './layouts/my-layout';

export default myLayout.wrapPage((props) => {
  return <>{...}</>;
});

export const getStaticProps = myLayout.wrapGetStaticProps(...);
// or...
export const getServerSideProps = myLayout.wrapGetServerSideProps(...);
```

Should you need to fetch additional data for your page, you can define a custom [`getStaticProps`](https://nextjs.org/docs/basic-features/data-fetching/get-static-props) or [`getServerSideProps`](https://nextjs.org/docs/basic-features/data-fetching/get-server-side-props) function, then pass it to `Layout.wrapGetStaticProps` or `Layout.wrapGetServerSideProps`, respectively.

### Connecting `next-super-layout` to your Next.js application

To make use of our layout-wrapped pages, we'll need to define a custom Next.js `_app`. Don't worry, it's pretty easy:

```tsx
// pages/_app
import { LayoutProvider } from 'next-super-layout';

export default function App(props) {
  return <LayoutProvider {...props} />;
}
```

_Voila!_

### Composing layouts

With `next-super-layout`, it's effortless to compose multiple layouts together using the `combineLayouts` function:

```tsx
// pages/some/path.tsx
import { combineLayouts } from 'next-super-layout';
import { myLayout } from './layouts/my-layout';
import { myOtherLayout } from './layouts/my-other-layout';

const combinedLayout = combineLayouts(myLayout, myOtherLayout);

export default combinedLayout.wrapPage((props) => {
  return <>{...}</>;
});

export const getStaticProps = combinedLayout.wrapGetStaticProps(...);
// or...
export const getServerSideProps = combinedLayout.wrapGetServerSideProps(...);
```

### Using layout data

Layouts contain a `useData` hook that easily connects any component within the React tree of a page to the data retrieved by that page's `getData` fetcher.

```tsx
// components/my-component.tsx
import { myLayout } from './layouts/my-layout';

function MyComponent() {
  const myLayoutData = myLayout.useData();

  // ...

  return <>{...}</>
}
```

## ⚖️ License

[MIT](./LICENSE)
