# Component Hierarchy

## App views

In most cases, we define child parent relationships between components by nested folders as described in `FolderStructure.md`.
```
SomeComponent
  index.js
  ...
  SomeChildComponent
    index.js
    ...
```

Which looks like this in jsx:

```jsx harmony
<SomeComponent>
  <SomeChildComponent />
</SomeComponent>
```


## Basic UI building blocks with strict hierarchy

The above approach works fine in most cases, but for reusable basic building blocks of the UI, which have a strictly predefined parent / child relationship based on css properties, there is another convention available, which makes the intended hierarchy explicitly visible.

Let's say have `UI/index.js` which contains components for defining the application layout. We defined a `Page` and `PageTitle`, now we need to introduce a layout with a `Sidebar`, which requires having a common wrapper for the main content node and the sidebar. We could define the intended structure by just using nested folders but let's consider this alternative:

```jsx harmony
<Page>
  <Page.Title>Title</Page.Title>
  <Page.Content>
    <Page.Content.Main></Page.Content.Main>
    <Page.Content.Sidebar></Page.Content.Sidebar>
  </Page.Content>
</Page>
```
The desired structure is visible in the render function rather than in the folders of the import statements. This helps making the intended hierarchy very explicit and it should make use of such components more intuitive.

It also saves us from creating a folder for each hierarchy level, which seems excessive with components that are usually two lines long. On the other hand we can keep using folders where appropriate, we just have to attach all sub components to the root.

While defining components like this, make sure the children are all named functions, so that we get proper names in React dev tools and jest snapshots.
