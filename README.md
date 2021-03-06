 ![react-flow](https://user-images.githubusercontent.com/2857535/67417109-516ef000-f5c8-11e9-938a-c2121d3ebbe4.png)

# :ocean: React Flow

React Flow is a library for building node-based graphs. You can easily implement custom node types and it comes with components like a mini-map and graph controls. Feel free to check out the [examples](https://react-flow.netlify.com/) or read the [blog post](https://webkid.io/blog/react-flow-node-based-graph-library/) to get started.

- [Key Features](#key-features)
- [Installation](#installation)
- [Usage](#usage)
- [ReactFlow Component Prop Types](#reactflow-component-prop-types)
- [Styling](#styling)
- [Nodes](#nodes)
  - [Options](#options-1)
  - [Node Types & Custom Nodes](#node-types--custom-nodes)
  - [Handle Component](#handle-component)
- [Edges](#nodes)
  - [Options](#options-2)
  - [Edge Types & Custom Edges](#edge-types--custom-edges)
- [Components](#components)
  - [Background](#background)
  - [Minimap](#minimap)
  - [Controls](#controls)
- [Helper Functions](#helper-functions)
- [Access Internal State](#access-internal-state)
- [Examples](#examples)
- [Development](#development)
- [Testing](#testing)

# Key Features

* **Easy to use:** Seamless zooming & panning behaviour and single and multi-selections of elements
* **Customizable:** Different [node](#node-types--custom-nodes) and [edge types](#edge-types--custom-edges) and support for custom nodes with multiple handles and edges
* **Fast rendering:** Only nodes that have changed are re-rendered and only those that are in the viewport are displayed
* **Utils:** Snap-to-grid, background styles and graph [helper functions](#helper-functions)
* **Components:** [Background, Minimap and graph controls](#components)
* **Reliable**: Written in [Typescript](https://www.typescriptlang.org/) and tested with [cypress](https://www.cypress.io/)

In order to make this library as flexible as possible we don’t do any state updates besides the positions. This means that you need to pass the functions to remove an element or connect nodes by yourself. You can implement your own ones or use the helper functions that come with the library.

## Installation

```
npm install react-flow-renderer
```

## Usage

This is a very basic example of how to use react-flow. There are more advanced examples in the [example](/example/src) folder.

```javascript
import React from 'react';
import ReactFlow from 'react-flow-renderer';

const elements = [
  { id: '1', data: { label: 'Node 1' }, position: { x: 250, y: 5 } },
  { id: '2', data: { label: 'Node 2' }, position: { x: 100, y: 100 } },
  { id: 'e1-2', source: '1', target: '2', animated: true },
];

const graphStyles = { width: '100%', height: '100%' };

const BasicFlow = () => (
  <ReactFlow elements={elements} style={graphStyles} />
);
```

## ReactFlow Component Prop Types

- `elements`: array of [nodes](#nodes) and [edges](#edges) *(required)*
- `onElementClick`: element click handler
- `onElementsRemove`: element remove handler
- `onNodeDragStart`: node drag start handler
- `onNodeDragStop`: node drag stop handler
- `onConnect`: connect handler
- `onLoad`: editor load handler
- `onMove`: move handler
- `onSelectionChange`: fired when element selection changes
- `nodeTypes`: object with [node types](#node-types--custom-nodes)
- `edgeTypes`: object with [edge types](#edge-types--custom-edges)
- `style`: css properties
- `className`: additional class name
- `connectionLineType`: connection line type = `straight` or `bezier`
- `connectionLineStyle`: connection style as svg attributes
- `deleteKeyCode`: default: `8` (delete)
- `selectionKeyCode`: default: `16` (shift)
- `snapToGrid`: default: `false`
- `snapGrid`: [x, y] array - default: `[16, 16]`
- `onlyRenderVisibleNodes`: default: `true`
- `isInteractive`: default: `true`. If the graph is not interactive you can't drag any nodes
- `selectNodesOnDrag`: default: `true`

## Styling

There are two ways how you can style the graph and the elements.
You can create your own CSS rules or pass style properties to the components.

#### Using Class Names

Since we are using DOM nodes for rendering the graph you can simply overwrite the styles with your own CSS rules.
The React Flow wrapper has the className `react-flow`. If you want to change the graph background for example you can do:

```css
.react-flow {
  background: red;
}
```

The same applies to the nodes (className: `react-flow__node`), edges (className: `react-flow__edge`), controls (`react-flow__controls`) or the mini map plugin (`react-flow__minimap`).

#### Using Properties

You could achieve the same effect by passing a style prop to the React Flow component:

```javascript
const FlowWithRedBg = (
  <ReactFlow
    elements={elements}
    style={{ background: 'red', width: '100%' height: '300px' }}
  />
);
```

## Nodes

There are three different [node types](#node-types--custom-nodes) (`default`, `input`, `output`) you can use. The node types differ in the number and types of handles. An input node has only a source handle, a default node has a source and a target and an output node has only a target handle. You create nodes by adding them to the `elements` array of the React Flow component.

Node example: `{ id: '1', type: 'input', data: { label: 'Node 1' }, position: { x: 250, y: 5 } }`

### Options

- `id`: string *(required)*
- `position`: { x: number, y: number } *(required)*
- `data`: {} *(required if you are using a standard type, otherwise depends on your implementation)*
- `type`: 'input' | 'output' | 'default' or a custom one you implemented
- `style`: css properties
- `className`: additional class name
- `targetPosition`: 'left' | 'right' | 'top' | 'bottom' handle position - default: 'top'
- `sourcePosition`: 'left' | 'right' | 'top' | 'bottom' handle position - default: 'bottom'

### Node Types & Custom Nodes

The standard node types are `input`, `default` and `output`. The default node types object looks like this:

```javascript
{
  input: InputNode,
  default: DefaultNode,
  output: OutputNode
}
```

The keys represent the type names and the values are the components that get rendered.
If you want to introduce a new type you can pass a `nodeTypes` object to the React Flow component:

```javascript
nodeTypes={{
  special: MyCustomNode
}}
```

You could now use the type `special` for a node.
The `default`, `input` and `output` types would be still available except you overwrote one of them.
There is an example of a custom node implementation in the [custom node example](/example/src/CustomNode).

### Handle Component

We export a `Handle` component as a helper for your custom nodes:

```javascript
import { Handle } from 'react-flow-renderer';

const targetHandleWithValidation = (
  <Handle
    type="target"
    position="left"
    isValidConnection={(connection) => connection.source === 'some-id'}
    onConnect={params => console.log('handle onConnect', params)}
    style={{ background: '#fff' }}
  />
);
```

#### Prop Types

- `type`: 'source' | 'target'
- `id`: string - you only need this when you have multiple source or target handles otherwise the node id is used
- `position`: 'left' | 'right' | 'top' | 'bottom' handle position - default: 'top' for type target, 'bottom' for type source
- `onConnect`: function that gets triggered on connect
- `isValidConnection`: function receives a connection `{ target: 'some-id', source: 'another-id' }` as param, returns a boolean - true by default
- `style`: css properties
- `className`: additional class name

#### Multiple Handles

If you need multiple source or target handles you can achieve this by creating a custom node. Normally you just use the id of a node for the `source` or `target` of an edge. If you have multiple source or target handles you need to pass an id to these handles. These ids get then added to the node id, so that you can connect a specific handle. If you have a node with an id = `1` and a handle with an id = `a` you can connect this handle by using the id = `1__a`.
You can find an example of how to implement a custom node with multiple handles in the [custom node example](/example/src/CustomNode/ColorSelectorNode.js#L18-L29).

## Edges

React Flow comes with three [edge types](#edge-types--custom-edges) (`straight`, `default`, `step`). As the names indicate, the edges differ in the representation. The default type is `default` which is a bezier edge. You create edges by adding them to your `elements` array of the React Flow component.

Edge example: `{ id: 'e1-2', type: 'straight', source: '1', target: '2', animated: true, label: 'edge label' }`

If you wanted to display this edge, you would need a node with id = 1 (source node) and one with id = 2 (target node).

### Options

- `id`: string *(required)*
- `source`: string (an id of a node) *(required)*
- `target`: string (an id of a node) *(required)*
- `type`: 'input' | 'output' | 'default' or a custom one you implemented
- `animated`: boolean
- `style`: css properties for the edge line path
- `label`: string
- `labelStyle`: css properties for the text
- `labelShowBg`: boolean - default: `true`
- `labelBgStyle`: css properties for the text background

You can find an example with lots of different edges in the [edges example](https://react-flow.netlify.app/edges).

### Edge Types & Custom Edges

The basic edge types are `straight`, `default` and `step`. The default `edgeTypes` object looks like this:

```javascript
{
  default: BezierEdge,
  straight: StraightEdge,
  step: StepEdge
}
```

The keys represents the type names and the values are the edge components.
If you want to introduce a new edge type you can pass an `edgeTypes` object to the React Flow component:

```javascript
edgeTypes={{
  special: MyCustomEdge
}}
```

Now you could use the new type `special` for an edge.
The `straight`, `default` and `step` types would still be available unless you overwrote one of them.
There is an implementation of a custom edge in the [edges example](/example/src/Edges/index.js).

## Components

### Background

React Flow comes with two background variants: **dots** and **lines**. You can use it by passing it as a children to the React Flow component:

```javascript
import ReactFlow, { Background } from 'react-flow-renderer';

const FlowWithBackground = () => (
  <ReactFlow elements={elements}>
    <Background
      variant="dots"
      gap={12}
      size={4}
    />
  </ReactFlow>
);
```

#### Prop Types

- `variant`: string - has to be 'dots' or 'lines' - default: `dots`
- `gap`: number - the gap between the dots or lines - default: `16`
- `size`: number - the radius of the dots or the stroke width of the lines - default: `0.5`
- `color`: string - the color of the dots or lines - default: `#999` for dots, `#eee` for lines
- `style`: css properties
- `className`: class name

### MiniMap

You can use the mini map plugin by passing it as a children to the React Flow component:

```javascript
import ReactFlow, { MiniMap } from 'react-flow-renderer';

const FlowWithMiniMap = () => (
  <ReactFlow elements={elements}>
    <MiniMap
      nodeColor={(node) => {
        switch (node.type) {
          case 'input': return 'red';
          case 'default': return '#00ff00';
          case 'output': return 'rgb(0,0,255)';
          default: return '#eee';
        }
      }}
    />
  </ReactFlow>
);
```

#### Prop Types

- `nodeColor`: string | function - if you pass a color as a string all nodes will get that color. If you pass a function you can return a color depending on the passed node.
- `nodeBorderRadius`: number
- `maskColor`: string
- `style`: css properties
- `className`: class name

### Controls

The control panel contains a zoom-in, zoom-out, fit-view and a lock/unlock button. You can use it by passing it as a children to the React Flow component:

```javascript
import ReactFlow, { Controls } from 'react-flow-renderer';

const FlowWithControls = () => (
  <ReactFlow elements={elements}>
    <Controls />
  </ReactFlow>
);
```

#### Prop Types

- `style`: css properties
- `className`: class name
- `showZoom`: boolean - default: true
- `showFitView`: boolean - default: true
- `showInteractive`: boolean - default: true

## Helper Functions

If you want to remove a node or connect two nodes with each other you need to pass a function to `onElementsRemove` and `onConnect`. In order to simplify this process we export some helper functions so that you don't need to implement them:

```javascript
import ReactFlow, { isNode, isEdge, removeElements, addEdge } from 'react-flow-renderer';
```

#### isEdge

Returns true if element is an edge

`isEdge = (element: Node | Edge): element is Edge`

#### isNode

Returns true if element is a node

`isNode = (element: Node | Edge): element is Node`

#### removeElements

Returns elements without the elements from `elementsToRemove`

`removeElements = (elementsToRemove: Elements, elements: Elements): Elements`

#### addEdge

Returns elements array with added edge

`addEdge = (edgeParams: Edge, elements: Elements): Elements`

#### project

Transforms pixel coordinates to the internal React Flow coordinate system

`project = (position: XYPosition): XYPosition`

You can use these function as seen in [this example](/example/src/Overview/index.js#L40-L41) or use your own ones.

## Access Internal State

We are using [Easy Peasy](https://easy-peasy.now.sh/) for state handling.
If you need to access the internal state you can use the `useStoreState` hook inside a child component of the React Flow component:

```javascript
import ReactFlow, { useStoreState } from 'react-flow-renderer';

const NodesDebugger = () => {
  const nodes = useStoreState(state => state.nodes);

  console.log(nodes);

  return null:
}

const Flow = () => (
  <ReactFlow elements={elements}>
    <NodesDebugger />
  </ReactFlow>
);
```

## Examples

You can find all examples in the [example](example) folder or check out the live versions:

- [overview](https://react-flow.netlify.app/)
- [basic](https://react-flow.netlify.app/basic)
- [custom node](https://react-flow.netlify.app/custom-node)
- [horizontal](https://react-flow.netlify.app/horizontal)
- [stress](https://react-flow.netlify.app/stress)
- [edges](https://react-flow.netlify.app/edges)
- [empty](https://react-flow.netlify.app/empty)
- [inactive](https://react-flow.netlify.app/inactive)

## Development

First of all you need to install the React Flow dependencies `npm install` and the ones of the examples `cd example && npm install`.

If you want to contribute or develop some custom features the easiest way is to start the dev server:

```
npm run dev
```

This serves the content of the `example` folder and watches changes inside the `src` folder. The examples are using the source of the `src` folder.

## Testing

Testing is done with cypress. You can find all test in the [`integration/flow`](/cypress/integration/flow) folder. In order to run the test do:

```
npm run test
```

## Thanks!

Special thanks to [Andy Lindemann](https://github.com/AndyLnd) for a lot of helpful contributions!

---

React Flow was initially developed by [webkid](https://webkid.io), a data visualization company from Berlin. If you need help or want to develop react-based tools or data visualizations, get in touch!
