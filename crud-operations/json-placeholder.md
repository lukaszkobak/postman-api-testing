# CRUD Operations - JSONPlaceholder API

Full request lifecycle testing against the JSONPlaceholder REST API.

## GET - Retrieve Posts

```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

const response = pm.response.json();

pm.test("Response has required fields", () => {
    pm.expect(response).to.have.property("userId");
    pm.expect(response).to.have.property("id");
    pm.expect(response).to.have.property("title");
    pm.expect(response).to.have.property("body");
});

pm.test("Post has correct structure", () => {
    pm.expect(response).to.have.all.keys("userId", "id", "title", "body");
});
```

## POST - Create Post (with dynamic data)

```javascript
// Pre-request script
const randomNum = Math.floor(Math.random() * 10000);
pm.environment.set("randomTitle", "Test Post " + randomNum);
pm.environment.set("timestamp", new Date().toISOString());
```

```javascript
// Test script
pm.test("Status code is 201", function () {
    pm.response.to.have.status(201);
});

pm.test("Status is Created", () => {
    pm.response.to.have.status("Created");
});

const response = pm.response.json();

pm.test("Created ID is 101", () => {
    pm.expect(response.id).to.eql(101);
});
```

## PUT vs PATCH - Update Post

```json
// PUT - full resource replacement
{
   "userId": 1,
   "title": "Updated title",
   "body": "Updated content - PUT test!",
   "id": 1
}
```

```json
// PATCH - partial update (only changed field)
{
    "body": "Updated content - PATCH test!"
}
```

## DELETE - Remove Post

```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

const response = pm.response.json();

pm.test("JSON response empty", () => {
    pm.expect(response).to.be.empty;
});
```

## What This Demonstrates
- Full REST CRUD cycle (Create, Read, Update, Delete)
- Difference between PUT (full replace) and PATCH (partial update)
- Dynamic test data using pre-request scripts (`Math.random`, `Date.toISOString`)
- Response validation: status codes, required fields, data structure
