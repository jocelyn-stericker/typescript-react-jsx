# TypeScript with React JSX

> There are now better options available. **Consider using [jsx-typescript](https://github.com/fdecampredon/jsx-typescript) instead.**

TypeScript with React JSX is a modified version of the [TypeScript](https://github.com/Microsoft/TypeScript) compiler and language services. To avoid ambiguity with the casting operator, instead of `<Component />`, the syntax is `<!Component />`. This makes reading code easier, and will make autocompletion smarter.

Currently based on [TypeScript 1.3](https://github.com/Microsoft/TypeScript). Basic usage, including type checking, works. Can be integrated into Visual Studio 2015 and gulp. *IntelliSense and syntax highlighting don't work.*

## Using Typescript with React JSX
You can use this anywhere you can use TypeScript 1.3. The CLI, Visual Studio 2015, and gulp are supported. [Typed React](https://github.com/Asana/typed-react) is required.

### Command line
Use `node ./bin/tsc.js` to transpile your TypeScript files. See syntax from the original repo.

### Gulp
Use [gulp-typescript-react-jsx](https://github.com/jnetterf/gulp-typescript-react-jsx).

### Visual Studio 2015
> Make sure you make a backup of typescriptServices.js in case you eventually decide you don't want to use this compiler!

Replace `typescriptServices.js` in `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\CommonExtensions\Microsoft\TypeScript` with the one in `bin/` from this repo.

## Example

```
// component.ts

/// <reference path="react.d.ts" />
import React = require("react");
import TypedReact = require("typed-react");

class HelloWorld extends TypedReact.Component<HelloWorld.IProps, {}> {
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

```
// main.ts

/// <reference path="react.d.ts" />
import HelloWorld = require("./helloWorld");

React.render(<!HelloWorld.Component name="Josh" />, document.body);
```

### Troubleshooting
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

4. Currently, if a spread attribute is used, no other attributes can be used.

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
