
## Switch to the Database

```javascript
use sparta
```
This switches to (or creates) a database named `sparta`.

---

##  Create a Collection

```javascript
db.createCollection('academy')
```

This creates a collection called `academy` 

---

## Insert Documents into the Collection

### Insert a Single Document

```javascript
db.academy.insertOne({ name: "New document" })
```

### Insert Multiple Documents

```javascript
db.academy.insertMany([
  { "course": "Data Engineering", "length": 10 },
  { "course": "Data Analysis", "length": 8 }
])
```

---

## Show Collection

```javascript
db.getCollectionInfos({name: 'students'})
show collections
db.getCollectionNames()
```

---

##  Read (Find) Documents

```javascript
db.students.find({})
db.academy.find({})
```

This lists all documents in the collections.

---

## Create a `favourite_fims` Collection

```javascript
db.createCollection('favourite_fims')
```

Tried to insert a document **without required fields**, which failed due to validation:

```javascript
db.favourite_fims.insertOne({
  title: 'Inception',
  director: 'Christopher Nolan',
})
// ❌ MongoServerError: Document failed validation
```

After correcting:

```javascript
db.favourite_fims.insertOne({
  title: 'Inception',
  director: 'Christopher Nolan',
  year: 2010,
  genres: ['Sci-Fi', 'Action'],
  rating: 8.0
})
```

### Insert Many

```javascript
db.favourite_fims.insertMany([
  {
    title: 'The Matrix',
    director: 'The Wachowskis',
    year: 1999,
    genres: ['Sci-Fi', 'Action'],
    rating: 8.7
  },
  {
    title: 'The Grand Budapest Hotel',
    director: 'Wes Anderson',
    year: 2014,
    genres: ['Comedy', 'Drama'],
    rating: 8.1
  }
])
```

---

## Update a Document

Changed the rating of “The Matrix”:

```javascript
db.favourite_fims.updateOne(
  { title: 'The Matrix' },
  { $set: { rating: 10.0 } }
)
```

---

##  Delete a Document

Removed “The Grand Budapest Hotel” from the collection:

```javascript
db.favourite_fims.deleteOne(
  { title: 'The Grand Budapest Hotel' }
)
```

---
