# Arctic Redpoll - a JavaScript Tree library (obviously)

Arctic Redpoll is a JavaScript Tree library for people who don't want to have to care about Trees, and just want to work with data. Which should be anyone working with trees, really.

Instead of using an API where you are responsible for building the tree, like this:

```
node n = new Node(value);
someOtherNode.add(n);
```

this library used the following API instead:

```
var root = new Tree();
root.add({ all: "the", values: "you", care: "about" });
```

Done. 

Of course, if you want a little more control, read on, but really that's that most important part: this library takes care of the tree building so you don't have to. Unless *you care*, in which case it lets you care to an incredibly degree.

## Cool... so... why "arctic redpoll"?

Because naming things is hard, and to be honest https://npmjs.com is filled with poor, incomplete and/or undocumented tree libraries, and as a consequence all the obvious names are taken.

So why not name it after an arctic redpoll?   

## Fair enough... so what is an "arctic redpoll"?

I'm glad you asked! [Arctic Redpolls](https://en.wikipedia.org/wiki/Arctic_redpoll) are a kind of finch (specifically, the "redpoll" genus in the finch family), and live in tundra regions in the arctic circle, defying all logic by simply existing.

They look like this:

![](arctic-redpoll.jpg)

(photograph [CC2.0-by](https://creativecommons.org/licenses/by/2.0), [Ron Knight](https://www.flickr.com/photos/sussexbirder/13667517895/in/photolist-nYBFrH-mPKAUg-mPKMpx-B6XZ2a-dKMwiR-63rJ41-63rzPd))

They have absolutely nothing to do with trees, other than "living in them", and they are (as far as we know) unaware of the intricacies of data storage and data structure manipulation. But they *are* quite cute, and zoologically quite interesting. So there you go: arctic redpolls. Go look them up, and appreciate the diversity of life on this planet. Because why not?

## Installation

Good old npm:
```
$> npm install arctic-redpoll --save
```

## How to use this library

Start off the way any requirement starts off:

```
var Tree = require('arctic-redpoll');
```

The subsequent API is fairly straight forward.

### Forming roots

```
var root = new Tree();
```

And done, we have a root.

It is also possible to create a fully specified Tree, by passing in a json-serialized Tree description:

```
var oldTree = ...
var serialized = oldTree.toString();
var newTree = new Tree(serialized); // functionally equivalent to oldTree
```


### Forming nodes

Note that for the purposes of a tree library, references to nodes and references to trees are *identical*, and so there is no distinction between a "Tree" data structure and a "Tree node" data structure. They are the same thing.

The modus operandus of this library is that you should not care about nodes unless you *really* care about nodes. As such, you can add as many children to any node as you like, and if you want the new nodes that makes, you can capture those, but you don't have to.

```
root.add({
  some: "object",
  with: 1,
  or: "more",
  values
});

// get a reference to the node/subtree created when adding data:
var n = root.add({
  some: "other",
  thing: "with",
  more: "properties"
});

// don't bother with additional references
n.add({ ... });
n.add({ ... });
n.add({ ... });

```

Additionally, if you want absolute precision, you can pass in an insertion algorithm that can traverse the tree as it exists, and find the right node to do the insertion for you

```
function rightParent(currentLevel, toInsert, comparatorProperty) {
  // is this the right node to add "toInsert" into,
  // based on the value of toInsert.value[comparatorProperty]?
}

function insertAlgorithm(current, node, comparator) {
  if (rightParent(current,node,comparator)) {
    return current.addChild(node);
  }
  for (child of current.children) {
    if (child.add(node, insertAlgorithm)) {
      return node;
    } 
  }
  return false;
}

// insert this value into the tree. We don't care "where", nor
// should we. The insertion algorithm will take care of that for us.
var inserted = root.add(vals, insertAlgorithm);
```
## Extended API

Let's cover all the things that this library does right now:

### remove(node)

Removes a node from the tree. Tree don't care where it is. If it exists, it'll find it, and kill it.

### find(matchObject)

Find a node for which all key/value pairs in `matchObject` match. For example:
```
var root = new Tree(),
    n1 = root.add({a:1,b:2}),
    n2 = root.add({a:2,b:2,c:3});

root.find({a:1}); // => will find n1
root.find({b:2}); // => will find n1
root.find({c:3}); // => will find n2
root.find({a:2, b:2, c:3}); // => will find n2
```

### findAll(matchObject)

find *all* nodes for which all key/value pairs in `matchObject` match. For example:
```
var root = new Tree(),
    n1 = root.add({a:1,b:2}),
    n2 = root.add({a:2,b:2,c:3});

root.findAll({a:1}); // => will find [n1]
root.find({b:2}); // => will find [n1,n2]
```

### valueOf()

This is a JavaScript spec-required function if you care about your data structure doing the right thing, so: it exists, and returns the tree itself, because that's the only correct behaviour.

### toString()

returns a String serialized form of this Tree. Which happens to be the tree's JSON respresentation! Handy.

### serialize()

An alias for `toString()`, itself an alias for `toJSON()`.

### toJSON()

Creates a JSON representation of this tree. Of course it would be incredibly useless if this JSON representation could not be read back in, and as such this library is transitive with respect to serialization:

```
var oldTree = ...;
...
var json = oldTree.toJSON();
var newTree = new Tree(json);
// "oldTree" and "newTree" are equivalent
```

### getAllValuePaths()

Generates *all paths* from the root to each of the leaves, where each path is encoded as an array of each node's `value` property. 

```
var root = new Tree();
var n11 = root.add({a:1});
var n12 = root.add({a:2});
var n111 = n11.add({b:1});
var n121 = n11.add({b:1});
root.getAllValuePaths();
/*
  [
    [{a:1}, {b:1}],
    [{a:2}, {b:1}]
  ]
*/ 
```

### getIterator()

Gives you an iterator for walking the entire tree without having to care about the "how" part.

```
var it = root.getIterator();

while(it.hasNext()) {
  var node = it.next();
  doSomethingWithThisInformation(node);
}
```

There is currently no control over the order in which all nodes are found, and if you *really* need an iterator, ordering should not matter: all nodes will be traversed anyway. If this is not the case, you don't want an iterator but a node collection.

## License

This code is Public Domain, except in jurisdictions that do not acknowledge public domain (for instance, France or Germany), in which case it's MIT licensed (hopefully eventually the world is sensible enough to recognise "I just wrote this but I do not care about what you do with it", but until then we're stuck with this ridiculous dual-license model).

