# Grafite

An experiment in creating a graph-like database using MySQL and Redis.

## Node Object

```
{
    id: '',
    created: 0,
    updated: 0,
    data: {
        ...
    },
    related: [
        ...
    ]
}
```

- `id` - This node's unique ID, a string.
- `created` - Millisecond timestamp of when the node was created (UTC).
- `updated` - Millisecond timestamp of when the node was last updated (UTC).
- `data` - Object containing this node's properties
- `related` - Array of elements describing related nodes. Each element is an array: `[ upstream, id, downstream ]`. Upstream and downstream are relationships that can either be `null` if there is no relationship in that direction, a `string` for a single relationship, or an `array` of strings for multiple relationships. `id` is the ID of the related node.

---

## grafite.create(properties, callback)

Create a node with given properties. See `node` section for documentation of node methods.

### Arguments
- `properties` - Object of key-value pairs.
- `callback(error, node)` - Returns the created node.

---

## grafite.get(id, callback)

Gets a node with given node ID.

### Arguments
- `id` - The ID of the node to get.
- `callback(error, node)` - Returns a node, `null` if not found.

---

## grafite.related(a, b, [threshold = 5], callback)

Checks if this node A is related to node B. This method will attempt to establish the shortest possible path from node A to node B using **downstream relationships only**. If path length exceeds threshold before a complete relationship is found, then it will fail. Higher threshold will increase query time and decrease performance as more nodes are being searched.

### Arguments
- `a` - A node object, or node ID to be related to.
- `b` - A node object, or node ID to be related to.
- `callback(error, related, path)`
    - `related` - True if related, false if not related or threshold is reached before relationship is found.
    - `path` - Empty array if no relationship, If there is an relationship, an array of alternating nodes and relationships that outline the path between the two nodes. It will always start with node A and end with the node B. Example ` [ nodeA, 'downstream', someNode, 'downstream', nodeB ]`.

---

## grafite.find(query, ..., [callback])

Scan the database for nodes based on given queries. Queries are joined by OR.

### Arguments
- `query` - One or more search queries.
- `callback(error, nodes)` - Returns an array of nodes. If omitted, defers query execution and return until last method in chain.

### Search Query Examples
- `[ null, { key: 'value' }, null ]` - Find node with property
- `[ 'up', null, null ]` - Find node with upstream relationship
- `[ null, null, 'down' ]` - Find node with downstream relationship
- `[ ['up' ,'up'], null, null ]` - Find node with any upstream relationship
- `[ null, null, ['down', 'down'] ]` - Find node with any downstream relationship

---

## grafite.traverse(query, ..., [callback])

Must be chained to `grafite.find`. Find nodes related to dataset based on queries. Queries within a traversal are joined by OR.

### Arguments
- `query` - One or more traversal queries to narrow down related nodes.
- `callback(error, nodes)` - Returns an array of nodes. If omitted, defers query execution and return until last method in chain.

### Traversal Query Examples
- `[ null, { key: 'value' }, null ]` - Find node with property up or downstream
- `[ 'up', null, null ]` - Find upstream node related by 'up'
- `[ null, null, 'down' ]` - Find downstream node related by 'down'

---

## grafite.sort(order, ..., callback)

Must be chained to `grafite.find` or `grafite.traverse`. Sorts given dataset by order queries. Queries are ran in the order then are given.

### Arguments
- `order` - One or more order query.
- `callback(error, nodes)` - Required, returns an array of nodes.

### Order Query Examples
- `[ null, 'key', null ]` - Sort by values of given key
- `[ null, [ 'key1', 'key2'] , null ]` - Sort by values of given keys
- `[ 1, null, null ]` - Sort by upstream relationships
- `[ null, null, 1 ]` - Sort by downstream relationships
- `[ null, null, null, -1 ]` - Sort in reverse
- `[ 1, 'key', 1, -1 ]` - Combined, sort by upstream relationship, values of given key, downstream relationship, and reserved

---

## node.update(properties, callback)

Updates this node. Setting value to `undefined` removes that property.

### Arguments
- `properties` - An object of key-value pairs. If value is `undefined` that key will be removed.
- `callback(error)`

---

## node.remove(callback)

Removes this node. Also removes all attached relationships.

### Arguments
- `callback(error)`

---

## node.exists(callback)

Checks if this node still exists.

### Arguments
- `callback(error, exists)` - Returns true or false.

---

## node.relate(downstream, node, callback)

Relates this node to given node with given downstream relationship. More than one relationship can be specified.

### Arguments
- `downstream` - The downstream relationship to given node.
- `node` - A node object, or node ID to be related to.
- `callback(error)`

---

## node.unrelate([downstream], node, callback)

Removes all downstream relationships from this node to given node. If downstream relationship is given, only that relationship is removed.

### Arguments
- `downstream` - The downstream relationship to given node.
- `node` - A node object, or node ID to be related to.
- `callback(error)`

---

## node.traverse(query, ..., callback)

Traverse from this node. See `grafite.traverse` for more information.

### Arguments
- `query` - See `grafite.traverse` for information on traversal queries.
- `callback(error, nodes)` - Returns an array of nodes. If omitted, defers query execution and return until last method in chain.

---

## node.sort(order, ..., callback)

Must be chained to `node.traverse`. See `grafite.sort` for more information.

### Arguments
- `order` - See `grafite.sort` for information on order queries.
- `callback(error, nodes)` - Required, returns an array of nodes.
