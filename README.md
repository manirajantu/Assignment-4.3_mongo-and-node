# MongoDB & Node.js: Assignment

## Brief

In this assignment, we will use the database `sample_supplies` and collection `sales`. Based on the stated business requirements, you are to write queries and aggregations (as answers) that produce results to meet the needs. This assignment will not tell you which data to query and what to return. You have to make a sensible decision on your own with optimization in mind. The general rule is not to produce data that are more than required. 


No codebase is provided in this assignment. You are to set up Node project and install `mongodb` package. Write mongo operations with Node.js and ensure those are working code. Copy the relevant code (the query/aggregation pipeline) to answer the questions here. 


### Business Requirement 1 - List the total cost for each purchase. (sum up the `price` properties in array `items`)

```
// Your answer

const { MongoClient } = require("mongodb"); 
const uri = "mongodb://localhost:27017"; 
const client = new MongoClient(uri);

(async () => {
  await client.connect(); 

  const db = client.db("sample_supplies");
  const collection = db.collection("sales");

  // Business Requirement 1 - List top five purchaser's email and total price (sum up the `price` properties in array `items`)
  let result = await collection
    .aggregate([
      {
        $project: {
          buyer: "$customer.email",
          totalPrice: {
            $sum: "$items.price",
          },
        },
      },
      {
        $limit: 5,
      },
      {
        $sort: {
          totalPrice: -1,
        },
      },
    ])
    .toArray();

  console.log(result);

  client.close();

})();


OUTPUT:
[
  {
    _id: new ObjectId("5bd761dcae323e45a93ccfec"),
    buyer: 'dotzu@ib.sh',
    totalPrice: new Decimal128("1527.13")
  },
  {
    _id: new ObjectId("5bd761dcae323e45a93ccfe9"),
    buyer: 'keecade@hem.uy',
    totalPrice: new Decimal128("1137.31")
  },
  {
    _id: new ObjectId("5bd761dcae323e45a93ccfea"),
    buyer: 'worbiduh@vowbu.cg',
    totalPrice: new Decimal128("942.28")
  },
  {
    _id: new ObjectId("5bd761dcae323e45a93ccfeb"),
    buyer: 'vatires@ta.pe',
    totalPrice: new Decimal128("297.59")
  },
  {
    _id: new ObjectId("5bd761dcae323e45a93ccfe8"),
    buyer: 'cauho@witwuta.sv',
    totalPrice: new Decimal128("269.79")
  }
]


```

### Business Requirement 2 - What are the different types of purchase method and which are more popular?

```
// Your answer


const { MongoClient } = require("mongodb"); 
const uri = "mongodb://localhost:27017"; 
const client = new MongoClient(uri);

(async () => {
  await client.connect(); 

  const db = client.db("sample_supplies");
  const collection = db.collection("sales");

  result = await collection
    .aggregate([
      {
        $group: {
          _id: "$purchaseMethod",
          count: {
            $count: {},
          },
        },
      },
    ])
    .toArray();

  console.log(result);

  client.close();

})();


OUTPUT:
[
  { _id: 'Online', count: 1585 },
  { _id: 'In store', count: 2819 },
  { _id: 'Phone', count: 596 }
]


```

### Business Requirement 3 - What is the average spending for the distinct purchase methods?

```
// Your answer


const { MongoClient } = require("mongodb"); 
const uri = "mongodb://localhost:27017"; 
const client = new MongoClient(uri);

(async () => {
  await client.connect(); 

  const db = client.db("sample_supplies");
  const collection = db.collection("sales");

   result = await collection
    .aggregate([
      {
        $project: {
          purchaseMethod: true,
          totalPrice: {
            $sum: "$items.price",
          },
        },
      },
      {
        $group: {
          _id: {
            purchaseMethod: "$purchaseMethod",
          },
          averagePrice: {
            $avg: "$totalPrice",
          },
        },
      },
    ])
    .toArray();

  console.log(result);

  client.close();

})();


OUTPUT:

[
  {
    _id: { purchaseMethod: 'Online' },
    averagePrice: new Decimal128("618.6438485804416403785488958990536")
  },
  {
    _id: { purchaseMethod: 'In store' },
    averagePrice: new Decimal128("627.0899574317133735367151472153246")
  },
  {
    _id: { purchaseMethod: 'Phone' },
    averagePrice: new Decimal128("594.3616610738255033557046979865772")
  }
]


```

### Business Requirement 4 - What are the top 10 most popular purchased items and their price?

```
// Your answer

const { MongoClient } = require("mongodb"); 
const uri = "mongodb://localhost:27017"; 
const client = new MongoClient(uri);

(async () => {
  await client.connect(); 

  const db = client.db("sample_supplies");
  const collection = db.collection("sales");

   result = await collection
    .aggregate([
      {
        $unwind: "$items",
      },
      {
        $group: {
          _id: "$items.name",
          count: {
            $count: {},
          },
          price:{
              $first:"$items.price"
          }
        },
      },
    ])
    .toArray();

  console.log(result);

  client.close();

})();

OUTPUT:
[
  { _id: 'envelopes', count: 4505, price: new Decimal128("19.95") },
  { _id: 'pens', count: 4559, price: new Decimal128("56.12") },
  { _id: 'backpack', count: 2309, price: new Decimal128("77.71") },
  { _id: 'binder', count: 4676, price: new Decimal128("14.16") },
  { _id: 'laptop', count: 2271, price: new Decimal128("866.5") },
  { _id: 'notepad', count: 6872, price: new Decimal128("35.29") },
  { _id: 'printer paper', count: 2246, price: new Decimal128("40.01") }
]


```

## Submission Guidelines

- Cite any relevant sources consulted during your research
- Solve the problems using your own code
- Do not copy and paste solutions from the source material
- Submit your assignment to black board.