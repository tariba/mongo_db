## Switch to the Database

```
use sparta
```
This switches to (or creates) a database named `sparta`.

---

##  Create a Collection

```
db.createCollection('academy')
```

This creates a collection called `academy` 

---

## Insert Documents into the Collection

### Insert a Single Document

```
db.academy.insertOne({ name: "New document" })
```

### Insert Multiple Documents

```
db.academy.insertMany([
  { "course": "Data Engineering", "length": 10 },
  { "course": "Data Analysis", "length": 8 }
])
```

---

## Show Collection

```
db.getCollectionInfos({name: 'students'})
show collections
db.getCollectionNames()
```

---

##  Find Documents

```
db.students.find({})

```

This lists all documents in the collections.

---

## Create a `favourite_fims` Collection

```
db.createCollection('favourite_fims')
```

Tried to insert a document **without required fields**, which failed due to validation:

```
db.favourite_fims.insertOne({
  title: 'Inception',
  director: 'Christopher Nolan',
})
// MongoServerError: Document failed validation
```

After correcting:

```
db.favourite_fims.insertOne({
  title: 'Inception',
  director: 'Christopher Nolan',
  year: 2010,
  genres: ['Sci-Fi', 'Action'],
  rating: 8.0
})
```

### Insert Many

```
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

```
db.favourite_fims.updateOne(
  { title: 'The Matrix' },
  { $set: { rating: 10.0 } }
)
```

---

##  Delete a Document

Removed “The Grand Budapest Hotel” from the collection:

```
db.favourite_fims.deleteOne(
  { title: 'The Grand Budapest Hotel' }
)
```

---



## Clear Screen (in terminal)
```bash
cls
```
Clears the terminal screen. Useful for cleaning up the console when working in environments like Windows Command Prompt.

---


## Find a specific character by name
```
db.characters.find({name: 'Luke Skywalker'})
```
 Retrieves all information for the character with the name "Luke Skywalker".

---

## Find a specific character by name and selecting fields for a character
```
db.characters.find({name: 'Chewbacca'}, {name: 1, eye_color: 1})
```
Returns only the `name` and `eye_color` fields of Chewbacca. The `_id` field is included by default.

---

## Same as above but excluding `_id`
```js
db.characters.find(
  { name: "Chewbacca" },
  {
    _id: 0,
    name: 1,
    eye_color: 1
  }
)
```

Same as above, but excludes the `_id` field.

---

## Accessing embedded fields
```js
db.characters.find(
  {
    name: "Ackbar"
  }, 
  {
    _id: 0,
    name: 1,
    "species.name": 1
  }
)
```
Returns the `name` and nested field `species.name` for Ackbar, excluding `_id`.

---

## Filter by nested field and project results
```js
db.characters.find(
  {
    'species.name': 'Human'
  },
  {
    name: 1,
    'homeworld.name': 1,
    _id: 0
  }
)
```
 Finds all characters whose species is Human and returns their name and homeworld, excluding `_id`.

We use `species.name` because species is likely an embedded document (a nested object). In MongoDB, to query or project a field inside a nested object, you use dot notation.
For example, if the structure looks like this:
```
{
  "name": "Luke Skywalker",
  "species": {
    "name": "Human"
  }
}
```
Then 'species.name' accesses the name field inside the species object.

---

## Using `$in` to filter by multiple values
```
db.characters.find(
  {
    eye_color: {
      $in: ['yellow', 'orange']
    }
  }, 
  {
    name: 1
  }
)
```
Returns characters who have either yellow or orange eyes.

---

## Filter by multiple conditions (implicit AND)
```
db.characters.find(
  {
    gender: 'female',
    eye_color: 'blue'
  }, 
  {
    name: 1
  }
)
```
Returns female characters with blue eyes. This is an implicit `$and`.

---

## Explicit `$and` with two conditions
```
db.characters.find(
  {
    $and: [
      { gender: 'female' },
      { eye_color: 'blue' }
    ]
  },
  {
    _id: 0,
    name: 1
  }
)
```
Same as above but uses the `$and` operator explicitly and hides `_id`.

---


## `$and` query with more fields projected
```
db.characters.find(
  {
    $and: [
      { gender: 'male' },
      { eye_color: 'yellow' }
    ]
  },
  {
    name: 1,
    eye_color: 1,
    gender: 1
  }
)
```
 Same query but includes the `eye_color` and `gender` fields in the result.

---

## Show all character heights
```
db.characters.find({}, {height: 1})
```
Lists the `height` field for all characters.

---

## Filter documents where height is unknown
```
db.characters.find({height: 'unknown'}, {height: 1})
```
 Returns characters whose height is stored as the string `"unknown"`.

---

## Remove the `height` field where value is "unknown"
```js
db.characters.updateMany(
  { height: "unknown" },
  { $unset: { height: "" } }
)
```
 Removes the `height` field entirely from documents where its value is `"unknown"`.

The `$unset` operator is used to delete a field from a document.

- You specify the field name as the key (height in this case). 
- The value (an empty string "") is ignored but required syntactically. 
- The result is that the height field is completely removed from matching documents, not just set to null.

---

## Convert height from string to integer
```js
db.characters.updateMany(
  {},
  [ { $set: { height: { $toInt: "$height" } } } ]
)
```
 This command updates all documents in the characters collection by converting the height field from a string (e.g. "172") to an integer (e.g. 172).

Although we are using updateMany(), the second argument is written as an array. This tells MongoDB to treat the update as an aggregation pipeline. This allows us to use aggregation expressions like `$toInt`.

`$set`

The `$set` operator is used to add a new field or update the value of an existing field. In this case, it replaces the current height value with a converted integer value.

`$toInt`

The `$toInt` operator converts a value to an integer data type.
Here, it takes the string `$height` and turns it into an integer. If the string cannot be converted, the operation will fail unless handled safely.

---
## Understanding `1` and `0` in MongoDB Projections

In MongoDB, when using the `find()` method, you can control which fields are **included** or **excluded** in the result by using `1` or `0` in the projection object.

- `1` means **include** the field.
- `0` means **exclude** the field.

### Examples:

```js
//Include only 'name' and 'height'
db.characters.find({}, { name: 1, height: 1 })

// Exclude only '_id'
db.characters.find({}, { name: 1, _id: 0 })
```

## Clean and Convert Mass to Double

```
db.characters.update(
  { mass: "1,358" },
  { $set: { mass: "1358" } }
)
```
Fixes improperly formatted numeric value by removing a comma.

```
db.characters.update(
  { mass: "unknown" },
  { $unset: { mass: "" } },
  { multi: true }
)
```

 Removes the `mass` field from documents where the value is `"unknown"`.
`multi:true` apply update to all documents that match the filler, not just the first one

``` js
db.characters.update(
  { mass: { $exists: true } },
  [
    { $set: { mass: { $toDouble: "$mass" } } }
  ],
  { multi: true }
)

``` 
Converts the mass field to a double where mass exists.

`$exists:true` condition check whether a field is present, regardless of it's value. Only select documents that have a `mass` field.

`$toDouble`
`$toDouble` is an aggregation operator that converts a value to a double e.g 64 bit floating point number

---


### Total Height of Human Characters

```
db.characters.aggregate([
  { $match: { 'species.name': 'Human' } },
  { $group: { _id: null, total: { $sum: '$height' } } }
])
```
Filters for Human species and sums their height.

`$match` filters the documents to only include characters whose species name is `human`. Similar to `WHERE` clause in SQL

`$group` Groups the filtered documents together. In this case all in one group since `_id:null`

`$sum` calculates the total sum of all `height` field across al `Human` characters

### Total Height by Gender

```js
db.characters.aggregate([
  { $match: { 'species.name': 'Human' } },
  { $group: { _id: '$gender', total: { $sum: '$height' } } }
])
```
Groups Human characters by gender and sums their height.

`_id:$gender` means we are grouping it by the gender field. Each unique gender value e.g 'male', 'female' becomes a separate group. This will return one result per gender with the total height for each

### Max Height by Homeworld

```js
db.characters.aggregate([
  { $group: { _id: "$homeworld.name", max: { $max: "$height" } } }
])
```
Gets max height per homeworld.

`$max` returns maximum value of a specified field within each group. Here, it looks at all characters from the same homeworld then it finds the tallest character in that group.

---

## Distinct and Count Operations

```
db.characters.distinct('species.name')
```
Returns unique species names.

```
db.characters.estimatedDocumentCount({'species.name': 'Human'})
```
Estimates document count for Humans.


```
db.characters.countDocuments({'species.name': 'Human'})
```
Accurate document count for Human species.

---

## Average Mass and Species Count

```
db.characters.aggregate([
  { $group: { _id: "$species.name", avg_mass: { $avg: "$mass" } } },
  { $sort: { avg_mass: 1 } }
])
```

The query groups characters by their species. For each species group, it averages the `mass` field of all characters in that group and sorts the results in ascending order of average mass (`1` = ascending, `-1` = descending).

The `$avg` operator calculates the average (mean) value in each group.


```
db.characters.aggregate([
  {
    $group: {
      _id: "$species.name",
      avg: { $avg: "$mass" },
      count: { $sum: 1 }
    }
  }
])
```
Groups documents by their species name, calculates the average mass for each species group, and counts the number of documents in each species group by adding 1 for every document.

`$sum` calculates the sum of numerical values within a group. When used as `{ $sum: 1 }`, it counts the number of documents in that group by adding 1 for each document.

```
db.characters.aggregate([
  {
    $group: {
      _id: "$species.name",
      avg: { $avg: "$mass" },
      count: { $sum: 1 }
    }
  },
  { $match: { avg: { $ne: null } } }
])
```

This aggregation groups documents by species name, calculating both the average mass and the count of characters per species. After grouping, it uses a `$match` stage to filter out any species groups where the average mass (`avg`) is `null`. The `$ne` operator means "not equal," so `{ avg: { $ne: null } }` ensures only species with a valid average mass are included in the results. This way, the output contains species with meaningful average mass values along with the number of characters counted in each group.


```
db.characters.aggregate([
  {
    $group: {
      _id: "$species.name",
      avg: { $avg: "$mass" },
      count: { $sum: 1 }
    }
  },
  { $match: { avg: { $ne: null } } },
  { $sort: { avg: 1 } }
])
```

This aggregation groups documents by species name, calculating the average mass and the count of characters per species. It then filters out species where the average mass (`avg`) is `null` using the `$match` stage with the `$ne` ("not equal") operator. Finally, it sorts the results by average mass in ascending order (`$sort: { avg: 1 }`), so species with the smallest average mass appear first in the output.

---

## Basic Find

```js
db.characters.find({ name: 'Darth Vader' }, { _id: 1 })
```
 Returns only `_id` of Darth Vader.

---

##  Lookup 

```
db.starships.aggregate([
  {
    $lookup: {
      from: 'characters',
      localField: 'pilot',
      foreignField: '_id',
      as: 'matched_pilot'
    }
  }
])
```
The `$lookup` stage in MongoDB's aggregation pipeline performs a **left outer join** between two collections. In this example, the aggregation starts on the `starships` collection and uses `$lookup` to join related documents from the `characters` collection.

Specifically, it matches the `pilot` field in the `starships` documents with the `_id` field in the `characters` collection. The `pilot` field contains one or more ObjectIDs referring to character documents. The matching character documents are then added to the output under the new field named `matched_pilot`.

This operation enriches each starship document with detailed information about its pilots, combining data from both collections into a single result set.


```js
db.starships.aggregate([
  {
    $lookup: {
      from: 'characters',
      localField: 'pilot',
      foreignField: '_id',
      as: 'matched_pilot'
    }
  },
  {
    $project: { name: 1, model: 1, 'matched_pilot.name': 1 }
  }
])
```
This aggregation pipeline on the `starships` collection begins with a `$lookup` stage that performs a left outer join with the `characters` collection. It matches the `pilot` field in `starships` (which contains one or more character `_id`s) with the `_id` field in `characters`. The matching character documents are added as an array under the new field `matched_pilot`.

Next, the `$project` stage reshapes the output documents to include only selected fields: the starship’s `name` and `model`, as well as the `name` fields of the matched pilots inside the `matched_pilot` array. This filters out all other fields to make the output concise and focused on the relevant information about starships and their pilots.

The `$lookup` stage performs a left outer join between two collections. It lets you combine documents from a “local” collection with matching documents from a “foreign” collection based on specified fields, embedding the matched documents as an array in the output.

The `$project` stage reshapes each document by including, excluding, or modifying fields. It controls which fields appear in the output documents, allowing you to focus on relevant data or rename fields.

---

## Find Multiple Characters by Name

```js
db.characters.find({
  name: { $in: ["Chewbacca", "Han Solo", "Lando Calrissian", "Nien Nunb"] }
}, { _id: 1 })
```
 Returns `_id` for listed characters.

---

## Insert Starship

```js
db.starships.insertOne({
  name: "Millenium Falcon",
  model: "YT-1300 Light Freighter",
  manufacturer: "Corellian Engineering Corporation",
  length: 34.37,
  max_atmosphering_speed: 1050,
  crew: 4,
  passengers: 6,
  pilot: [
    ObjectId("5ea9890f98e05ffdb34de96a"),
    ObjectId("5ea9890f98e05ffdb34de9ba"),
    ObjectId("5ea9891098e05ffdb34de9ec"),
    ObjectId("5ea9891098e05ffdb34dea14")
  ]
})
```

`ObjectId` is a special BSON type used by MongoDB as a unique identifier for documents. Each document in a collection typically has an `_id` field that stores an `ObjectId` to uniquely identify it.

In this example, the `pilot` field is an array of `ObjectId`s referencing `_id`s of documents in the `characters` collection. This creates a relationship between the `starships` and `characters` collections by linking starships to their pilots via their unique IDs.

