# TypeScript with React JSX

TypeScript with React JSX is a modified version of the [TypeScript](https://github.com/Microsoft/TypeScript) compiler and language services. TypeScript is a language for application-scale JavaScript. The React JSX patch adds JSX-style syntax to make writing typed [React](https://github.com/facebook/react) applications fun and easy.

> This repo is based on Microsoft's fast, new, completely rewritten, TypeScript compiler. The new compiler and language services aren't supported everywhere the old compiler is yet. For example, you can't use this version for IntilliSense in Visual Studio, and there isn't any easy way of incorporating TypeScript files in your gulp/browserify setup yet. You'll likely be able to do all these things shortly after you can with the unpatched compiler.

<br />
> This repo is not yet ready for prime time. Please feel free to pick apart my code and send me PRs.

## Usage

Basic usage [isn't changed](https://github.com/Microsoft/TypeScript). Use `node ./bin/tsc.js` to transpile your TypeScript files.

This patch is designed with Asana's [Typed React](https://github.com/Asana/typed-react) in mind, but you may be able to use this with the other React **0.12** binding layers that may appear. The only requirement is that `React.createElement` is defined where you use JSX tags.

Here's an example component, `helloWorld.ts`:
```
/// <reference path="react.d.ts" />
import React = require("react");
import TypedReact = require("typed-react");

class HelloWorld extends TypedReact.Component<HelloWorld.IProps, {}> {
    props: HelloWorld.IProps;
    state: {};
    render() {
        return <!div>Hello, {this.props.name}</div>;
    }
}

module HelloWorld {
    export interface IProps {
        name?: string;
    }
    export var Component = TypedReact.createClass(React.createClass, HelloWorld);
}

export = HelloWorld;
```

And then you can use it in `main.ts`,
```
/// <reference path="react.d.ts" />
import HelloWorld = require("./helloWorld");

React.render(<!HelloWorld.Component name="Josh" />, document.body);
```

### Keep in Mind
1. The syntax to use a component is `<!Component />`. The extra exclamation mark is used to avoid any
   ambiguity between type conversions and JSX-style syntax. There's not necessarily any ambiguity from
   the compiler's perspective, but the extra exclamation mark helps make code quicker to read, and allows
   for smarter language services (when the new compiler supports them).

2. User-defined components **must** start with an uppercase letter. Built-ins (like `div`) **must** start with
   a lower-case letter. This is a restriction put in place in JSX starting in React 0.12, and this repo
   maintains that restriction.

3. Third-party React libraries will work just like you may expect. You can't, however, just use TypeScript
   classes directly (this is planned for React 0.13). You instead need to use components created with
   `TypedReact.createClass`. The example above uses a pattern which will be easy to improve once React
   0.13 is released.

4. There's no reason to use ReactElement **factories** if you're using JSX.

5. If a spread attribute is used, no other attributes can be used.

## Contributing, building, etc.

See [the parent repo](https://github.com/Microsoft/TypeScript). Please do submit pull requests!

## Formal Spec
The extra syntax added to TypeScript is almost exactly what [JSX adds to ECMAScript](https://facebook.github.io/jsx/).

PrimaryExpression: (see ECMAScript 6 spec)
* TSXElement

TSXElement
* TSXChildlessElement
* TSXOpeningElement TSXChildren? TSXClosingElement

TSXChildlessElement
* `<!` TSXElementName TSXAttributes? `/>`

TSXOpeningElement
* `<!` TSXElementName TSXAttributes? `>`

TSXClosingElement
* `</` TSXElementName `>`

TSXElementName
* [JSXIdentifier](https://facebook.github.io/jsx/) (without dashes)
* MemberExpression

TSXAttributes?
* (can be empty)
* TSXSpreadAttribute
* TSXAttribute? TSXAttributes?

TSXSpreadAttribute
* `{` `...` AssignmentExpression `}`

TSXAttribute?
* (can be empty)
* [JSXIdentifier](https://facebook.github.io/jsx/) (not Namespaced) `=` TSXAttributeValue

TSXAttributeValue
* `"` [JSXDoubleStringCharacter?](https://facebook.github.io/jsx/) `"`
* `{` AssignmentExpression `}`

TSXChildren?
* (can be empty)
* TSXChild TSXChildren?

TSXChild
* [JSXText](https://facebook.github.io/jsx/)
* TSXElement
* `{` AssignmentExpression `}`
