# Grafite

Graph-like database interface using Javascript, MySQL, and Mongo.

- <a href="#goals">Basic Goals</a>
- <a href="#objects">Library Objects</a>
    - <a href="#objects-grafte">grafite</a>
    - <a href="#objects-grafiteQuery">grafiteQuery</a>
    - <a href="#objects-nodeObject">nodeObject</a>
    - <a href="#objects-queryObject">queryObject</a>
    - <a href="#objects-orderObject">orderObject</a>
- <a href="#man">Manipulate Nodes</a>
- <a href="#rel">Node Relationships</a>
- <a href="#query">Querying and Traversing Nodes</a>
- <a href="#node">nodeObject Methods</a>



<h2 id="goals">Basic Goals</h2>

1. Create and manipulate objects
    - Create
    - Update
    - Delete
2. Relate objects to each other
    - Have multiple relationships
    - Remove object relationships
3. Query for objects
    - By object property
    - By relationship
4. Traverse from object to object
    - By object property
    - By relationship



<h2 id="objects">Library Objects</h2>


<h3 id="objects-grafite">grafite</h3>

The main interface for the library. Read the rest of this documentation for more information.


<h3 id="objects-grafiteQuery">grafiteQuery</h3>

Returned by functions that query and traverse nodes:

- <a href="#man-find">grafite.find</a>
- <a href="#query-traverse">grafiteQuery.traverse</a>
- <a href="#query-sort">grafiteQuery.sort</a>
- <a href="#node-traverse">nodeObject.traverse</a>

Has two available methods:

- <a href="#node-traverse">traverse</a>
- <a href="#nde-sort">sort</a>


<h3 id="objects-nodeObject">nodeObject</h3>

Results from functions that create, manipulate, and find nodes:

- <a href="#man-create">grafite.create</a>
- <a href="#man-get">grafite.get</a>
- <a href="#man-update">grafite.update</a>
- <a href="#man-find">grafite.find</a>
- <a href="#query-traverse">grafiteQuery.traverse</a>
- <a href="#query.sort">grafiteQuery.sort</a>
- <a href="#node-traverse">nodeObject.traverse</a>

Available methods:

- <a href="#node-traverse">traverse</a>
- <a href="#node-update">update</a>
- <a href="#node-delete">delete</a>
- <a href="#node-relateTo">relateTo</a>
- <a href="#node-relatedTo">relatedTo</a>
- <a href="#node-unrelateFrom">unrelateFrom</a>
- <a href="#node-severFrom">severFrom</a>


<h3 id="objects-queryObject">queryObject</h3>

An array with 3 elements used to query and traverse for nodes.

```
[
    upstream,
    query object,
    downstream
]
```

- `upstream` a string or an array of strings that define the upstream relationships to look for
- `query object` and object in the style of [MongoDB find object](http://docs.mongodb.org/manual/tutorial/query-documents/)
- `downstream` a string or an array of strings that define the downstream relationships to look for

Use `null` call be used for any 3 of these elements, indicating not to query on that field.


<h3 id="objects-orderObject">orderObject</h3>

A plain object with a key-value pairs. Where the key is the field to sort on, and the value is either `1` for ascending order (defualt), or `0` or `-1` for descending order.



<h2 id="man">Manipulate Nodes</h2>


<h3 id="man-create">grafite.create(data, callback);</h3>

Create a node.

- `data` an object
- `callback(error, nodeObject)`


<h3 id="man-get">grafite.get(node, callback);</h3>

Get a node.

- `node` nodeID or nodeObject
- `callback(error, nodeObject)`


<h3 id="man-update">grafite.update(node, data, callback);</h3>

Update a node's data.

- `node` nodeID or nodeObject
- `data` an object, parameters with undefined values are removed
- `callback(error, nodeObject)`


<h3 id="man-delete">grafite.delete(node, callback);</h3>

Delete a node and node's relationships.

- `node` nodeID or nodeObject
- `callback(error)`



<h2 id="rel">Node Relationships</h2>


<h3 id="rel-relate">grafite.relate(upstreamNode, relationship, downstreamNode, callback);</h3>

Relate upstream node to downstream node with one or more relationships.

- `upstreamNode` nodeID or nodeObject
- `relationship` single relationship or an array an relationships
- `downstreamNode` nodeID or nodeObject
- `callback(error)`


<h3 id="rel-unrelate">grafite.unrelate(upstreamNode, relationship, downstreamNode, callback);</h3>

Remove one or more relationships from upstream node to downstream node.

- `upstreamNode` nodeID or nodeObject
- `relationship` single relationship or an array of relationships
- `downstreamNode` nodeID or nodeObject
- `callback(error)`


<h3 id="rel-unrelate">grafite.unrelate(upstreamNode, downstreamNode, callback);</h3>

Remove all relationships from upstream node to downstream node.

- `upstreamNode` nodeID or nodeObject
- `downstreamNode` nodeID or nodeObject
- `callback(error)`


<h3 id="rel-sever">grafite.sever(node, node, callback);</h3>

Remove all relationships between both nodes.

- `node` nodeID or nodeObject
- `node` nodeID or nodeObject
- `callback(error)`


<h3 id="rel-related">grafite.related(upstreamNode, downstreamNode, [threshold], callback);</h3>

Check if upstream node is related to downstream node, stopping at `threshold` number of jumps.

- `upstreamNode` nodeID or nodeObject
- `downstreamNode` nodeID or nodeObject
- `threshold` optional, defaults to 5, maximum number of steps to check
- `callback(error, related)`


<h3 id="rel-related">grafite.related(upstreamNode, relationship, downstreamNode, callback);</h3>

Check if upstream node is immediately related to downstream node by one or more relationships.

- `upstreamNode` nodeID or nodeObject
- `relationship` single relationship or an array of relationships
- `downstreamNode` nodeID or nodeObject
- `callback(error, related)`



<h2 id="query">Querying and Traversing Nodes</h2>


<h3 id="query-find">grafite.find(query, [...], [callback]);</h3>

Find a node with given queries. Additional queries are OR conditions. Returns a [grafiteQuery](#objects-queryObject) object.

- `query` [queryObject](#objects-queryObject), one or more can be used
- `callback(error, nodes)` optional, if not provided query will not be executed


<h3 id="query-traverse">grafiteQuery.traverse(query, [...], [callback]);</h3>

Traverse to related nodes from a query. Must be used as a chained function from [grafite.find](#query-find) or [grafiteQuery.traverse](#query-traverse). Additional queries are OR conditions.

- `query` [queryObject](#objects-queryObject), one or more can be used
- `callback(error, nodes)` optional, if not provided query will not be executed


<h3 id="query-sort">grafiteQuery.sort(order, callback);</h3>

Sort the results of a query. Callback is required

- `order` [orderObject](#objects-orderObject), one or more can be used
- `callback(error, nodes)` required, must be provided



<h2 id="node">nodeObject Methods</h2>

Most of these are self explanatory. Look at their corresponding grafite methods to understand what they do.

<h4 id="node-traverse">nodeObject.traverse(query, [...], [callback]);</h4>

<h4 id="node-update">nodeObject.update(data, callback)</h4>


<h4 id="node-delete">nodeObject.delete()</h4>


<h4 id="node-relateTo">nodeObject.relateTo(relationship,
downstreamNode, callback)</h4>


<h4 id="node-relatedTo">nodeObject.relatedTo(downstreamNode, callback)</h4>


<h4 id="node-relatedTo">nodeObject.relatedTo(relationship, downstreamNode, callback)</h4>


<h4 id="node-unrelateFrom">nodeObject.unrelateFrom(relationship, downstreamNode, callback)</h4>


<h4 id="node-unrelateFrom">nodeObject.unrelateFrom(downstreamNode, callback)</h4>


<h4 id="node-severFrom">nodeObject.severFrom(node, callback)</h4>
