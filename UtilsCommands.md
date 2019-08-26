# Commands:

##### username: m001-student

##### password: m001-mongodb-basics

## Connection:

- shell MY CLUSTER - `mongo "mongodb+srv://cluster0-lwdjb.mongodb.net/test" --username admin --password admin`
- shell Course cluster -`mongo "mongodb://cluster0-shard-00-00-jxeqq.mongodb.net:27017,cluster0-shard-00-01-jxeqq.mongodb.net:27017,cluster0-shard-00-02-jxeqq.mongodb.net:27017/test?replicaSet=Cluster0-shard-0" --authenticationDatabase admin --ssl --username m001-student --password m001-mongodb-basics`
- application - `mongodb+srv://admin:<password>@cluster0-lwdjb.mongodb.net/test?retryWrites=true&w=majority`
- compass - `mongodb+srv://admin:<password>@cluster0-lwdjb.mongodb.net/test`

## Import Data:

- `load("loadMovieDetailsDataset.js")`

## Change Database:

- `use video`
- `db`

## Insert Documents:

- Insert One Document

```javascript
db.moviesScratch.insertOne({
	title: "Star Trek II: The Wrath of Khan",
	year: 1982,
	imdb: "tt0084726"
})
```

- Insert One Document, setting \_id manually

```javascript
db.moviesScratch.insertOne({
	_id: "tt0084726",
	title: "Star Trek II: The Wrath of Khan",
	year: 1982,
	imdb: "tt0084726"
})
```

- Insert Many Documents, setting \_id manually

```javascript
db.moviesScratch.insertMany([
	{
		_id: "tt0084726",
		title: "Star Trek II: The Wrath of Khan",
		year: 1982,
		type: "movie"
	},
	{
		_id: "tt0796336",
		title: "Star Trek",
		year: 2009,
		type: "movie"
	},
	{
		_id: "tt0084726",
		title: "Star Trek II: The Wrath of Khan",
		year: 1982,
		type: "movie"
	},
	{
		_id: "tt00408101",
		title: "Star Trek Into Darkness",
		year: 2013,
		type: "movie"
	},
	{
		_id: "tt0117731",
		title: "Star Trek: First Contact",
		year: 1996,
		type: "movie"
	}
])
```

- Insert Many Documents, setting \_id manually AND ordering false to add all even that has an equal object

```javascript
db.moviesScratch.insertMany(
	[
		{
			_id: "tt0084726",
			title: "Star Trek II: The Wrath of Khan",
			year: 1982,
			type: "movie"
		},
		{
			_id: "tt0796336",
			title: "Star Trek",
			year: 2009,
			type: "movie"
		},
		{
			_id: "tt0084726",
			title: "Star Trek II: The Wrath of Khan",
			year: 1982,
			type: "movie"
		},
		{
			_id: "tt00408101",
			title: "Star Trek Into Darkness",
			year: 2013,
			type: "movie"
		},
		{
			_id: "tt0117731",
			title: "Star Trek: First Contact",
			year: 1996,
			type: "movie"
		}
	],
	{
		ordered: false
	}
)
```

## Search Documents:

- Simple Search using filters Documents

```javascript
db.movies
	.find({
		year: 1896,
		genre: "Documentary, Short"
	})
	.pretty()
```

---

```javascript
db.movieDetails
	.find({
		"awards.wins": 2,
		"awards.nominations": 2
	})
	.count()
```

---

```javascript
db.movieDetails
	.find({
		rated: "PG",
		"awards.nominations": 10
	})
	.count()
```

- Search Documents walking through objects

```javascript
db.data
	.find({
		"wind.direction.angle": 290
	})
	.pretty()
```

- Search Documents walking through an Array and selecting exactly position

```javascript
db.movies
	.find({
		"cast.0": "Jeff Bridges"
	})
	.pretty()
```

---

```javascript
db.movieDetails
	.find({
		"writers.0": "Ethan Coen",
		"writers.1": "Joel Coen"
	})
	.count()
```

---

```javascript
db.movieDetails
	.find({
		genres: "Family"
	})
	.count()
```

---

- Filter which field we wan't to bring with my query
  - 1 to show, 0 to hide.

```javascript
db.movies.find(
	{
		genre: "Action, Adventure"
	},
	{
		title: 1,
		_id: 0
	}
)
```

---

# Update Documents:

--- https://docs.mongodb.com/manual/reference/operator/update/

- Update Documents using filters.

```javascript
db.movieDetails.updateOne(
	{
		title: "The Martian"
	},
	{
		$set: {
			poster: "http://www.google.com.br"
		}
	}
)
```

```javascript
db.movieDetails.updateOne(
	{
		title: "The Martian"
	},
	{
		$set: {
			awards: {
				wins: 8,
				nominations: 14,
				text: "Nominated for 3 Golder Globes."
			}
		}
	}
)
```

```javascript
db.movieDetails.updateOne(
	{
		title: "The Martian"
	},
	{
		$inc: {
			"tomato.reviews": 3,
			"tomato.userReviews": 25
		}
	}
)
```

- Creating an array on push

```javascript
db.movieDetails.updateOne(
	{
		title: "The Martian"
	},
	{
		$push: {
			reviews: {
				rating: 4.5,
				date: ISODate("2016-01-12T09:00:00Z"),
				reviewer: "Spencer H.",
				text: "reviewText1"
			}
		}
	}
)
```

- More than onde update in the same array.

```javascript
db.movieDetails.updateOne(
	{
		title: "The Martian"
	},
	{
		$push: {
			reviews: {
				$each: [
					{
						rating: 0.5,
						date: ISODate("2016-01-12T07:00:00Z"),
						reviewer: "Yabo A",
						text: "reviewText2"
					},
					{},
					{
						rating: 5,
						date: ISODate("2016-01-12T09:00:00Z"),
						reviewer: "Kristina Z",
						text: "reviewText3"
					},
					{},
					{
						rating: 2.5,
						date: ISODate("2015-10-26T04:00:00Z"),
						reviewer: "Matthew Samuel",
						text: "reviewText4"
					},
					{},
					{
						rating: 4.5,
						date: ISODate("2015-12-13T03:00:00Z"),
						reviewer: "Eugene B",
						text: "reviewText5"
					}
				]
			}
		}
	}
)
```

- Update Many Documents

```javascript
db.movieDetails.updateMany(
	{
		rated: null
	},
	{
		$unset: {
			rated: ""
		}
	}
)
```

- Update documents using variables

```javascript
let filter = {
	title: "House, M.D., Season Four: New Beginnings"
}
let doc = db.movieDetails.findOne(filter)
doc.poster
doc.poster = "http://www.imdb.com/title/tt1329164/mediaviewer/rm2619416576"
doc.genres
doc.genres.push("TV Series")
db.movieDetails.replaceOne(filter, doc)
```

# Delete Documents:

- Delete documents by filter

```javascript
db.movieDetails.deleteOne({
	_id: ObjectId("5d17af63d970b2ffd974d69f")
})
```

```javascript
db.movieDetails.deleteMany({
	metacritic: 38
})
```

# Create User:
```javascript
db.createUser(
  {
    user: "rec02",
    pwd: "pwd",
    roles: [
       { role: "readWrite", db: "db1" }
    ]
  }
)

db.createUser(
  {
    user: "rec01",
    pwd: "pwd",
    roles: [
       { role: "readWrite", db: "db2" }
    ]
  }
)
```

# Query with operators

```javascript
db.movieDetails.find({
	writers: { $in: ["Ethan Coen", "Joel Coen"] }
})
```
