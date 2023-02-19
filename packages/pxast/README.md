# `pxast`

**P**i**x**iv novel **A**bstract **S**yntax **T**ree

----------

**pxast** is a specification for representing [pixiv novel][pixiv-novel] in a syntax tree. It implements [unist][unist].

**Warning**: This project still in draft.

## Contents

## Introduction

This document defines a format for representing [pixiv novel][pixiv-novel] as an [abstract syntax tree][syntax-tree].

## Types

## Nodes

### `Parent`

```idl
interface Parent <: UnistParent {
  children: [PxastContent]
}
```

### `Literal`

```idl
interface Literal <: UnistLiteral {
  value: string
}
```

### `Root`

```idl
interface Root <: Parent {
  type: 'root'
}
```

### `Paragraph`

```idl
interface Paragraph <: Parent {
  type: 'paragraph'
  children: [PhrasingContent]
}
```

For example, the following text:

```text
たとえば私はこの文章を書く。
```

Yields:

```js
{
  type: 'paragraph',
  children: [
    {type: 'text', value: 'たとえば私はこの文章を書く。'}
  ]
}
```

### `Heading`

```idl
interface Heading <: Parent {
  type: 'heading'
  children: [StaticPhrasingContent]
}
```

For example, the following text:

```text
[chapter:まえがき]
```

Yields:

```js
{
  type: 'heading'
  children: [{type: 'text', value: 'まえがき'}]
}
```

### `PageHeading`

```idl
interface Page <: Node {
  type: 'pageHeading'
  pageNumber: 1 <= number
}
```

For example, the following text:

```text
[newpage]
```

Yields:

```js
{
  type: 'pageHeading',
  pageNumber: 1
}
```

### `Text`

```idl
interface Text <: Literal {
  type: 'text'
}
```

For example, the following text:

```text
たとえば私はこの文章を書く。
```

Yields:

```js
{type: 'text', value: 'たとえば私はこの文章を書く。'}
```

### `Ruby`

```idl
interface Ruby <: Literal {
  type: 'ruby'
  ruby: string
}
```

For example, the following text:

```text
[[rb:私>わたし]]
```

Yields:

```js
{
  type: 'ruby',
  value: '私',
  ruby: 'わたし'
}
```

### `Break`

```idl
interface Break <: Node {
  type: 'break'
}
```

For example, the following text:

```text
これは一行目。
これが二行目。
```

Yields:

```js
{
  type: 'paragraph',
  children: [
    {type: 'text', value: 'これは一行目。'},
    {type: 'break'},
    {type: 'text', value: 'これが二行目。'}
  ]
}
```

### `Link`

```idl
interface Link <: Parent {
  type: 'link'
  url: string
  children: [StaticPhrasingContent]
}
```

For example, the following text:

```text
[[jumpurl:リンク例>https://example.com]]
```

Yields:

```js
{
  type: 'link',
  url: 'https://example.com',
  children: [{type: 'text', value: 'リンク例'}]
}
```

### `Image`

```idl
interface Image <: Node {
  type: 'image'
  illustId: string
  pageNumber: 1 <= number?
}
```

For example, the following text:

```text
[pixivimage:000001-02]
```

Yields:

```js
{
  type: 'image',
  illustId: '000001',
  pageNumber: 2
}
```

### `PageReference`

```idl
interface PageReference <: Node {
  type: 'pageReference'
  pageNumber: 1 <= number
}
```

For example, the following text:

```text
[jump:01]
```

Yields:

```js
{
  type: 'pageReference',
  pageNumber: 1
}
```

## Content model

```idl
type PxastContent = FlowContent | PhrasingContent
```

### `FlowContent`

```idl
type FlowContent = Heading | PageHeading | Paragraph
```

### `PhrasingContent`

```idl
type PhrasingContent = Link | Image | PageReference | StaticPhrasingContent
```

### `StaticPhrasingContent`

```idl
type StaticPhrasingContent = Break | Ruby | Text
```

## License

[MIT][license]

<!-- Link Definitions -->

[license]: ./LICENSE
[pixiv-novel]: https://www.pixiv.net/novel/
[unist]: https://github.com/syntax-tree/unist
[syntax-tree]: https://github.com/syntax-tree/unist#syntax-tree