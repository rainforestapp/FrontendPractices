# Documentation

## Every Abstraction needs a readme
Whether you are adding a higher order component, a utility, a React component or a shared reducer. Every new and old abstraction across the Frontend should have be documented, in the form of a `readme.md` file.

The readme should contain the following
1. An explanation of why we need this abstraction.
2. A brief explanation of the interface of the abstraction - What arguments does the abstraction consume, what does it return.
3. At least one or more examples of usage.

## Commenting non obvious logic
Logic that is hard to reason about should generally be avoided. Sometimes however we find ourselves writing logic that is quirky. This can have various reasons, like a complicated data model for example. This needs to be commented at all times. Be diligent when reviewing code that contains non-obvious logic.
