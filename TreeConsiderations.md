Tree Considerations
==========

There are several data structures in Specify which have tree
structure. E.g. Taxa, geography, storage locations. These structures
are not easy to represent naturally in a relational database in a way
that is both easy to update and easy to query.

The most natural representation is to link rows with self referential
foreign keys to their parent rows. This has the advantage of being
faithful to the intrinsic structure of the tree and makes adding and
moving nodes and sub-trees easy. On the other hand, designing queries
that match sub-trees, a common use case, is nontrivial with this
representation.

The original solution was to embed global information about the
structure of a tree locally in each node in the form of *node
numbers*, which are constructed in such a way that all nodes in a
sub-tree are related by having consecutive numbers. Thus, a sub-tree
query becomes a range query.

The disadvantage of this approach is that it moves the complication
from query time to update time. Whenever the tree structure is
changed, non local updates must be performed to keep the information in
the node numbers current. Where ease of updates is a priority over
ease of queries, this approach is inappropriate. This has been the
case for Harvard, and they have submitted a patch which removes the
node numbers.

There is a way to square this circle. Simply put, the idea is to again
locally capture information about the global structure of the tree
proximate to each node in order to simplify queries, but to do so in a
decoupled and asynchronous fashion so that the simplicity of updates
is retained.

Instead of storing node numbers on each node, an ancillary table can
be constructed with a one-to-one relationship to the tree. This table
could be used to store node numbers, but a more transparent solution
is to simply store a path from the tree's root to each related
node. For this reason it makes sense to term such an accessory table
a *path cache*.

How is this decoupling helpful? When a tree structure is to be
mutated, the parent links can be simply updated to new values, so
updates are as simple as in the sans node number regime. Following
separation of concerns, the responsibility for maintaining the path
cache is delegated to an encapsulated module which notices mutations
of the tree and updates the cache accordingly. This update module can
run asynchronously in a separate thread or process.

With the cache in place sub-tree queries become even simpler than with
node numbers. To find all descendants of a given target node, it
suffices to find those nodes whose paths have the target node's path
as a prefix, that is, with an SQL `LIKE` clause. The ancestors of a
node are simply those whose ids are in the path of the target node.

To illustrate the feasibility of this approach I developed some
proof-of-concept SQL that implements the functions of the update
module. This code can be found
[here](https://gist.github.com/96a5fd7f7105671df074). In fact, the
update module would need do little more than simply execute the given
SQL (minus the `create table` operations) at a reasonable
frequency. The queries are constructed to be tolerant to faults and
concurrent redundant invocation. In the event of cache corruption, the
decoupled nature of the solution admits the possibility of simply
disposing of a cache and waiting for it to be repopulated.


