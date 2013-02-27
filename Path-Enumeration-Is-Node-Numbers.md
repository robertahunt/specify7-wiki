The Deep Connection Between Node Numbers and Path Enumeration
-------------------------------------------------------------

To see how path enumeration is really just a generalization of node
numbering, start by looking at the properties of the node numbering
system.

1. Each node in a tree has a `nodeNumber` attribute and a
`highestChildNodeNumber` attribute.

2. `n.nodeNumber <= n.highestChildNodeNumber` for any node, `n`.

3. If node `a` is an ancestor of node `n`, then
`a.nodeNumber < n.nodeNumber` and,
`a.highestChildNodeNumber >= n.highestChildNodeNumber`.

4. If node `m` and `n` have the same parent, then (without loss of
generality) `m.highestChildNodeNumber < n.nodeNumber`. That is, the
node number ranges of sibling nodes must be disjoint.

These properties are the only ones necessary to the functioning of the
system. In particular, it is only the ordering on the numbers that
matters. There is no requirement that there not be 'gaps'. For
example, if `a.nodeNumber = 10` there need not be a child `c` of `a`
with `c.nodeNumber = 11`. Further, there is no need for the node
numbers to be natural numbers or even integers.

The main problem with node numbers is that changes to the tree
structure may result in re-numberings of nodes far from the subtree
where the change occurs. This because there has to be 'room' made for
any new nodes in a subtree. If integer node numbers are replaced with
rationals, this problem is obviated since there is always 'room' for
more rational numbers between any two other numbers. So let the first
generalization of node numbering be allowing non-integer node numbers.

Next, it is annoying that there is an asymmetry between the names
`nodeNumber` and `highestChildNodeNumber` for what are basically two
endpoints of an interval. The `nodeNumber` is the just lower bound, so
call it simply `lb`. Similarly replace `highestChildNodeNumber` with
`ub`.

So how does this work? Constructing an example is helpful, and
taxonomy will serve. Start with a root node, `life`. It needs `lb` and
`ub` values. Since there is no worry about having enough 'room',
`life.lb = 0` and `life.ub = 1`.

Now for some child nodes:
* `animal.lb = 0.1; animal.ub = 0.19;`
* `plant.lb = 0.2; plant.ub = 0.29;`
* `fungus.lb = 0.3; fungus.ub = 0.39;`
*  etc.

The values don't matter as long as they make disjoint intervals and
fall between 0 and 1.

Some animals:
* `invert.lb = 0.11; invert.ub = 0.149;`
* `vert.lb = 0.15; vert.ub = 0.189;`
* etc.

And imagining farther down the tree:
* `snails.lb = 0.13421; snails.ub = 0.134219;`
* `worms.lb = 0.121; worms.ub = 0.1219;`
* `dogs.lb = 0.15652; dogs.ub = 0.15661;`
* `humans.lb = 0.18542; humans.ub = 0.185532;`
* etc.

Dividing up the intervals will eventually result in intervals narrower
than any given floating point precision. To avoid that problem just
suppose the values are stored as strings representing the decimal
expansion. Since there is no harm in introducing gaps, values can
always be chosen that have non-repeating decimal expansions.

Notice that the intervals for the kingdom nodes are particularly
nice. All animals will have decimal expansions that start out like
`0.1*`. Plants will start like `0.2*`. If every node was sure to have
nine or fewer children, the pattern could be replicated all the way
down the tree. Subtree relationships would then correspond to prefix
matches. Also only the lower bound would be necessary since the upper
bound could be obtained by append a 9.

Of course the limitation of nine children is entirely due to choosing
a decimal expansion for the for fractions. A hexidecimal expansion
would relax the limit to 15 children and still permit the nice prefix
matching. On the other hand, there is no reason not to stick with
decimal expansion but allocate *two* digits at each level of the tree
allowing up to 99 children per node.

Of course, the maximum number of children is utimately limited by the
number of id values that are available for the primary key of node
table. If the same number of digits comprising the maximum possible
primary key is allocated in the node number at each level of the tree,
then prefix matching can be used regardless of how many children are
supported!

The next realization is that the *zero-padded primary key can simply
be used as the value* of the decimal expansion at the portion
allocated to the node's level of the tree. So node numbers end up
looking like:

    0.{pk1}{pk2}{pk3}{pk4}...{pkN}

where `pkN` is the zero-padded primary key of the node at N-th level
of the tree.

The whole point of using fixed width portions of the decimal expansion
at each level of the tree was to allow string prefix matching to be
used in place of numeric comparison since the expansions are expected
to exceed the floating point precision of the system. For string
matching, the `0.` prefix common to all the values is
superfluous. Also, the only reason to zero-pad the primary key values
is to keep the digits from each level of tree from becoming mixed
together. That can be done just as easily by seperating them by some
character which is not a digit, such as ','. When that is done, node
numbers have become path enumeration.
