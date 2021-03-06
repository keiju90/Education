### 6. Backend APIs

#### Persistence (The Data we store)

---

#### State

"A program is described as stateful if it is designed to remember preceding events or user interactions; the remembered information is called the state of the system."

---

#### HTTP is a Stateless Protocol

"No session information is retained by the server. All data is sent to the server so
that the request can be understood in isolation, without any knowledge of previous
requests"

* Client (browser) has the state.
* Client can persist session state (localStorage).
* Sometimes we switch clients.

---

#### Persistence

"In computer science, persistence refers to the characteristic of state that outlives
the process that created it."

---

#### Persisted State

A program (process) that remembers what you did (state), even if you restart it
(presistence).

* Storing data to disk
* Files, Databases etc.
* Reliable Persistence?

---

#### ACID

In computer science, ACID (Atomicity, Consistency, Isolation, Durability) is a set of
properties of database transactions intended to guarantee validity even in the event
of errors, power failures

* Atomicity: all or nothing is stored.
* Consistency: Only change data in allowed ways.
* Isolation: Many may read, but one may write.
* Durability: Data will remain even in case of a crash.

---

#### BASE

The BASE acronym is used to describe the properties of certain databases, usually NoSQL databases. It's often referred to as the opposite of ACID. This applies to distributed or horizontally scaled databases

* Basically Available: 
* Soft state
* Eventual consistency

#### MongoDB

* Our Persistence of Choice
"MongoDB is a document database with the scalability and flexibility that you want
	with the querying and indexing that you need."

---

#### NoSQL vs. SQL
"A NoSQL (originally referring to "non SQL" or "non relational") database provides
a mechanism for storage and retrieval of data that is modeled in means other than
the tabular relations used in relational databases"

* Databases used to be almost exclusively SQL.
* Tabluar data means data ise stored in a table.
* Relational data means data refers to data in other tables.

---

#### Scalability
"Scalability is the property of a system to handle a growing amount of work by
adding resources to the system."

* Vertical means a bigger machine.
* Horizontal means adding more machines.
* SQL doesn't scale well Horizontally.
* Lead to a varied number of "NoSQL" databases.

---

#### Document Database
"MongoDB stores data in flexible, JSON­like documents, meaning fields can
vary from document to document and data structure can be changed over time"

* JSON is not tabular, so NoSQL.
* MongoDB documents can be relational.
* Each document has a unique ID.

---
#### UUID
A universally unique identifier (UUID) is a 128-bit number used to identify information in computer systems

**"145e6b0a-ffb7-48b9-a9ca-13377cd7c4b7"**

- No need to centrally coordinate IDs
- the probability to find a duplicate within 103 trillion version-4 UUIDs is one in a billion.
- Won't collide before the heat death of the universe, probably
- Collisions are historical events

---

#### Querying
"Asking for the information you need".

* How do we define what we're looking for?
* Structured Query Language (SQL) is a standard.
* Many xQLs available.

---

#### Indexing

"Indexes are used to quickly locate data without having to search every row in a
database table every time a database table is accessed."

* Indices should be set up for frequently acessed data.
* Some querying is too complex without it, like free text search.

---

#### Databases and Schemas

Mongo stores multiple detatbase idientified by the path argument of the connection
string (URI)

```Shell
mongodb://localhost:27017/myDatabase
```

Each database has multiple **schemas** that define the format for the stored data

---
#### Practical Mongodb
Enough theory and nomenclature for now.

---
#### Mongoose
"Mongoose provides a straight-forward, schema-based solution to model your
application data. It includes built-in type casting, validation, query building,
business logic hooks and more, out of the box."
- Makes it simpler to communicate with mongo

---
#### Mongoose Schema

Defines the structure of the data

```JavaScript
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
	// _id: { type: Schema.ObjectId, auto: true },
	name: String,
	username: { type: String, unique: true }
});

const User = mongoose.model('User', userSchema);
```

* Schema defined in JSON
* Type can be simple, or expanded using a JSON object
* Every collection has an auto generated ObjectId
* Relational data by referring to _ids of other schemas

---
#### Executing queries

Mongoose calls are asynchronous and takes a callback on the format

```JavaScript
callback(error, result) // It's not a promise
```
but does support ```then()``` and ```async await```.

```JavaScript
User.find().then((error, users) => {
	return res.send(users);
}).catch((error) => {
	next(error);
})
```

---
#### Find

Find takes an object containing the search criteria.

```JavaScript
User.find({name: req.query.name}).then((error, users) => {
	return res.send(users);
}).catch((error) => {
	next(error);
})
```

```JavaScript
doc = user.find({name: "pelle kaning"}).then...
```

---
#### Find By Id or One

Find by id takes an ObjectId as argument. Something that can be Cast to a UUID.

```JavaScript
User.findOne({_id: req.params.userId}).then...
User.findById(req.params.userId).then((error, users) => {
	return res.send(users);
}).catch((error) => {
	next(error);
})
```

```JavaScript
doc = User.findOne({_id: "145e6b0a-ffb7-48b9-a9ca-13377cd7c4b7"}).then...
doc = User.findById("145e6b0a-ffb7-48b9-a9ca-13377cd7c4b7").then...
```

<a href="https://mongoosejs.com/docs/queries.html" target="_blank">https://mongoosejs.com/docs/queries.html</a>

---
#### Delete

Delete an object containing the search criteria.

```JavaScript
User.deleteOne({_id: req.params.userId}).then...
User.findByIdAndDelete(req.params.userId).then((error, users) => {
	return res.send(users);
}).catch((error) => {
	next(error);
})
```

```JavaScript
doc = User.deleteOne({_id: "145e6b0a-ffb7-48b9-a9ca-13377cd7c4b7"}).then...
doc = User.findByIdAndDelete("145e6b0a-ffb7-48b9-a9ca-13377cd7c4b7").then...
```

---
#### Updating (put)

Replace an object

```JavaScript
Model.findOneAndUpdate({id: req.params.userId}
	{ req.body },
	{
		upsert: true,
		new: true,
		returnNewDocument: true
	}.then((status) => {
	if (status.upserted)
		res.status(201)
	else if (status.nModified)
		res.status(200)
	else
		res.status(204)
	}
```

---
#### Updating patch

Updating the keys of an object

```JavaScript
Model.findByIdAndUpdate(req.params.id,
  { 
    $set: dotify(req.body)
  },
  {
    returnNewDocument: true,
    new: true,
  }).then((user) => {
    console.log(user)
    res.send(user)
  }).catch((error) => next(error))
}
```

`$set` replaces the keys with the specified objects...

---
#### Bracket vs Dot Notation

we don't want to replace address with an object containing just street with teh value

```json
  {
   "name": "name other than my name",
   "address" : {
      "street": "coolz street"
   }
  }
```

We want to replace the key street in the object address with the value:

```json
  {
   "name": "name other than my name",
   "address.street": "coolz street"
   }
	}
	```

dotify converts between bracket and dot notation!
