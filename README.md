# remark-refractor

Highlight code blocks in Markdown files with [Refractor](https://github.com/wooorm/refractor)

Also add support for custom language called `js-with-links-details` via [MDX](https://mdxjs.com/) that allows you to write rich annotations inside JS snippets:

// docs.mdx

```js-with-links-details
module.exports = {
  <mode "/concepts/mode">
    <default>
      mode: "production", // "production" | "development" | "none"
    </default>
    mode: "production", // enable many optimizations for production builds
    mode: "development", // enabled useful tools for development
    mode: "none", // no defaults
  </mode>
}
```

You would need to add MDX loader to handle `.mdx` files to your favorite JS bundler.

Once imported you would need to pass a `Pre` React component to the MDX `components` [prop](https://mdxjs.com/getting-started/#mdxprovider):

```
export default {
  components: {
    pre: Pre
  }
};
```

`Pre` component would receive your code as `children` prop, this plugin also adds `componentname` and `url` to every child, that you can use to build any imaginable UI:

```js
const components = {
  mode: (children, props) => {
    return <RichComponent children={children} {...props} />;
  }
};

const Pre = props => {
  const newChildren = React.Children.map(props.children.props.children, child => {
    if (React.isValidElement(child)) {
      if (child.props.props.className.includes("keyword")) {
        return components[child.props.props.componentname];
      }
    }

    return child;
  });

  const newProps = {
    children: newChildren
  };

  return (
    <pre>
      <code {...newProps} />
    </pre>
  );
};
```
