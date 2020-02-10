Functional Trees
================

A system that allows walking and rewriting of parts of trees in a
functional manner, along with translation of references to internal
nodes that can be carried from one tree to its successors.

Implemented in a manner that is compatible with and depends upon FSet.

## Design and Usage

### Sub-classing the `node` class
In most cases it is likely that one would subclass the `node` class
provided by this package.  Any subclass of `node` can specify which of
its slots might hold subtrees by defining a `child-slots` slot which
should be initialized to hold the names of these fields and should be
allocated on the class itself.  See the following example.

```lisp
(defclass if-then-else-node (node)
  ((child-slots :initform '(then else) :allocation :class)
   (then :reader then :type '(list node))
   (else :reader else :type '(list node)))
  (:documentation "An if-then-else subtree of a program AST."))
```

Each child slot should hold a list of nodes.

### Default data access
In some cases it may be useful to identify a slot which by default
holds the data for a node.  This can be specified by defining the
`data-slot` slot on your node subclass, which similarly should be
stored on the class instance.

```lisp
(defclass node-with-data (node)
  ((children :reader children
             :type list
             :initarg :children
             :initform nil
             :documentation "The list of children of the node,
which may be more nodes, or other values.")
   (child-slots :initform '(children) :allocation :class)
   (data-slot :initform 'data :allocation :class)
   (data :reader data :initarg :data :initform nil
         :documentation "Arbitrary data")))
```

By defining `data-slot` the generic `data` function will operate on
the defined subclass.  As `data` is the default key for common
common-lisp functions like `position` when run on nodes, this makes
their usage much more concise.

```lisp
(let ((it (convert 'node-with-data '(1 2 3 4 (5 6 7 8) (9 (10 (11)))))))
  (list (position 8 it)
        (@ (position 8 it) it)))
```

## Tasks
- [ ] Eliminate hard-coded children.
- [ ] Integrate with FSet.
- [ ] Integrate with GMap.
- [ ] Tests provide coverage and are passing.