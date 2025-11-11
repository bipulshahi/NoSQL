# 1 — Simple analogy (the library)

Think of a database like a **library**:

* A **database** = one entire library building.
* A **collection** = one bookshelf (e.g., the “Science Fiction” shelf).
* A **document** = one book on that shelf.
* A **field** inside a document = a piece of information in the book (title, author, pages).

In a relational (SQL) library, every book on a shelf must follow the *same form* (same set of pages in the same order). In MongoDB (document DB), each book can have different chapters — one book might have photos, another a map, another extra appendices. That flexibility is the main idea. 

---

# 2 — What is MongoDB (short)

* **MongoDB** is a document-oriented NoSQL database.
* It stores **documents** (JSON-like objects) inside **collections** rather than rows inside tables.
* Documents are stored as **BSON** (binary JSON) which supports extra types (dates, binary data, 64-bit ints). 

---

# 3 — Documents & Collections — real tiny examples

### Example 1 — Product catalog (one product = one document)

```json
// in collection: products
{
  "_id": ObjectId("64a9f..."),
  "sku": "TSHIRT-001",
  "name": "Organic Cotton T-Shirt",
  "price": 399,
  "colors": ["black","white"],
  "sizes": ["S","M","L"],
  "specs": {"material":"cotton","weight_g":180},
  "tags": ["men","tops","eco"]
}
```

Another product can have totally different fields:

```json
{
  "sku":"MUG-019",
  "name":"Ceramic Mug",
  "price": 249,
  "capacity_ml":300,
  "fragile": true
}
```

No ALTER TABLE needed — one doc can have `sizes`, another can have `capacity_ml`. 

### Example 2 — Blog posts (collection: posts)

```json
{
  "_id": ObjectId("..."),
  "title":"Why MongoDB?",
  "author":"Rina",
  "content":"...",
  "tags":["nosql","mongodb"],
  "comments":[
    {"user":"Asha","text":"Nice!","time": ISODate("2025-11-10T08:00:00Z")},
    {"user":"Vik","text":"Thanks!","time": ISODate("2025-11-11T09:15:00Z")}
  ],
  "views": 1200
}
```

Here `comments` is nested array—easy to model in one document.

---

# 4 — Core terms (quick)

* **Document** — JSON-like object (BSON under the hood).
* **Collection** — group of documents (like a table).
* **Database** — container of collections.
* **_id** — unique identifier for every document (MongoDB creates one automatically `ObjectId` unless you provide another). 

---

# 5 — Basic CRUD with `mongosh` (commands you can try)

**Switch to DB / show DBs**

```js
show dbs
use shopDB        // switch/create database
```

**Create/Insert**

```js
db.products.insertOne({
  sku:"TSHIRT-001", name:"Organic T-Shirt", price:399
})
db.products.insertMany([{...},{...}])
```

**Read / Find**

```js
db.products.find()                 // all docs (returns cursor)
db.products.find({ price: { $gt: 300 } })   // filter: price > 300
db.products.findOne({ sku: "TSHIRT-001" })
db.products.find({}, { name:1, price:1 })   // projection: only name & price
```

**Update**

```js
db.products.updateOne(
  { sku: "TSHIRT-001" },
  { $set: { price: 379 } }   // change price
)
db.products.updateMany({ tags: "sale" }, { $inc: { price: -50 } })
```

**Delete**

```js
db.products.deleteOne({ sku: "OLD-001" })
db.products.deleteMany({ discontinued: true })
```

These are equivalent to SQL INSERT/SELECT/UPDATE/DELETE but use JSON filters and update operators (`$set`, `$inc`, etc.). 

---

# 6 — Important data types and BSON

BSON supports:

* strings, numbers (32/64-bit), booleans
* `Date` (`ISODate`), `ObjectId`, binary data, embedded documents, arrays
  This gives more precise typing than plain JSON and is efficient to store/transfer. 

---

# 7 — Why developers like MongoDB (short)

* **Flexible schema** → iterate fast (ideal for startups/MVPs).
* **JSON-like** → natural fit with JavaScript/Node.js stacks.
* **Built-in scaling & availability** (replica sets and sharding) for large apps.
* **Good query & aggregation capabilities** for nested data. 

---

# 8 — Quick peek: Indexes & queries (why index matters)

If you frequently search `products` by `sku`, create an index on `sku`. Without an index, MongoDB scans the whole collection (slow). With index, lookup is fast.

```js
db.products.createIndex({ sku: 1 })
```

Think of index as a library card catalog for quick lookup.

---

# 9 — Very-high-level architecture (so you know what terms mean)

* **mongod** — the primary database server process.
* **mongosh** — shell to talk to MongoDB.
* **Replica set** — group of mongod instances (1 primary + N secondaries) for failover and redundancy. If primary fails, replica set elects a new one. Good for high availability.
* **Sharding** — automatic horizontal partitioning of data across multiple shards (machines) for scaling huge datasets. Use sharding when a single machine isn’t enough. 

---

# 10 — Tools you’ll commonly use

* **mongosh** — interactive shell (CLI).
* **MongoDB Compass** — GUI to explore collections, view schema, run queries visually. Great for beginners. 
* **MongoDB Atlas** — cloud hosted MongoDB (if you don’t want to run servers).
* CLI helpers: `mongoimport`, `mongoexport`, `mongodump`, `mongorestore` for import/export and backups. 

---

# 11 — When to pick MongoDB (short checklist)

Pick MongoDB when:

* Your data is document-like and may evolve (product catalogs, user profiles, posts).
* You want fast development and less DB migration pain.
* You need to scale horizontally for large read/write loads. 

Avoid MongoDB if:

* You need lots of complex multi-row transactions with strong consistency (banking) — although MongoDB supports transactions now, RDBMS still shine in heavy normalized relational workloads.
* You need many complex JOINs across many tables (relational dbs are designed for that).

---

# 12 — Mini hands-on exercise (mental or on your laptop)

Create a small `shopDB` and `products` collection, insert 3 products (copy examples above), then:

* Find products with `price > 300`.
* Update one product to add a new field `stock: 50`.
* Create index on `sku`.

If you want, tell me what environment you’ll use (local mongod, Atlas, or Compass) and I’ll give exact commands tailored to that.

---

# 13 — Revisit + what’s next

Recap: MongoDB stores JSON-like documents (BSON) in collections, is schema-flexible, easy to iterate with, and fits modern app patterns. You learned the library analogy, saw small document examples, basic CRUD in `mongosh`, and the role of tools like Compass/Atlas. 
