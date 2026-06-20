# Dynamic Data & Request Chaining - Simple Books API

A full order lifecycle test (Create → Read → Update → Delete) using pre-request scripts, environment variable chaining, and cleanup - tested against the Simple Books API.

## Pre-request Script - Dynamic Customer Data

```javascript
const names = ["Lukasz Kobak", "Anna Nowak", "Piotr Wisniewski", "Kasia Kowalska"];
const randomName = names[Math.floor(Math.random() * names.length)];

pm.environment.set("customerName", randomName);
pm.environment.set("orderTime", new Date().toISOString());

console.log("Order for: " + randomName);
console.log("Order date and time: " + pm.environment.get("orderTime"));
```

## Create Order - Saving Data for Next Requests

```javascript
pm.test("Status code is 201", function () {
    pm.response.to.have.status(201);
});

const response = pm.response.json();

pm.test("Order created", () => {
    pm.expect(response.created).to.be.true;
});

pm.test("Order ID received", () => {
    pm.expect(response.orderId).to.exist;
    pm.expect(response.orderId).to.be.a("string");
});

// Save order ID for the next request in the chain
if (response.orderId) {
    pm.environment.set("orderId", response.orderId);
    console.log("Order created! ID:", response.orderId);
}
```

## Read Order - Verifying Chained Data

```javascript
const response = pm.response.json();

pm.test("Order ID matches saved", () => {
    pm.expect(response.id).to.eql(pm.environment.get("orderId"));
});

pm.test("Book ID matches saved", () => {
    const savedBookId = parseInt(pm.environment.get("bookId"));
    pm.expect(response.bookId).to.eql(savedBookId);
});
```

## Delete Order - Graceful Handling + Cleanup

```javascript
pm.test("Delete attempted", () => {
    const status = pm.response.code;
    // Pass if 204 (deleted) OR 404 (already gone)
    pm.expect([204, 404]).to.include(status);
});

console.log("Cleaning up environment...");

// Remove test data after the lifecycle completes
pm.environment.unset("orderId");
pm.environment.unset("bookId");

console.log("Cleanup complete!");
```

## What This Demonstrates
- Pre-request scripts generating dynamic test data (random names, timestamps)
- Chaining requests by saving IDs to environment variables (`pm.environment.set`) and reusing them in later requests
- Verifying chained data matches across the request sequence
- Graceful status code handling (accepting multiple valid outcomes)
- Test data cleanup after a full lifecycle - good practice for repeatable test runs
