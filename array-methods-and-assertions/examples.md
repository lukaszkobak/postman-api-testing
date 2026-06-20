# Array Methods & Assertions

Using JavaScript array methods (`filter`, `find`, `forEach`) combined with Chai assertions to validate API responses, tested across multiple public APIs.

## filter() - CoinGecko API

```javascript
const response = pm.response.json();

const Bitcoin = response.filter(l => l.name === "Bitcoin");
const Dogecoin = response.find(l => l.name === "Dogecoin");

pm.test("Check if Bitcoin is in list", () => {
    pm.expect(Bitcoin[0].name).to.eql("Bitcoin");
    pm.expect(Bitcoin[0].name).to.exist;
});

pm.test("Dogecoin is cheaper than Bitcoin", () => {
    pm.expect(Dogecoin.current_price).to.be.below(Bitcoin[0].current_price);
});

pm.test("Prices are numbers", () => {
    pm.expect(Bitcoin[0].current_price).to.be.a("number");
    pm.expect(Dogecoin.current_price).to.be.a("number");
});
```

## filter() with includes() - Dog API

```javascript
const response = pm.response.json();

const englishBreeds = Object.keys(response.message).filter(breed => 
    response.message[breed].includes("english")
);

console.log("English breeds:", englishBreeds.length);
```

## find() on objects - GitHub API

```javascript
const response = pm.response.json();

const specificRepo = response.find(f => f.id === 940929652);

pm.test("Specific ID V2", () => {
    pm.expect(specificRepo.id).to.eql(940929652);
});

pm.test("Sum of Repos is 11", () => {
    pm.expect(response.length).to.eql(11);
});
```

## forEach() - Finding the Most Expensive Coin

```javascript
const coins = pm.response.json();

let mostExpensive = coins[0];

coins.forEach(coin => {
    if (coin.current_price > mostExpensive.current_price) {
        mostExpensive = coin;
    }
});

console.log("Most expensive coin:");
console.log(mostExpensive.name + " = $" + mostExpensive.current_price);
```

## forEach() with Counter - Mid-Price Range Coins

```javascript
const coins = pm.response.json();

let midPriceCoin = 0;

coins.forEach(coin => {
    if (coin.current_price >= 100 && coin.current_price <= 1000) {
        midPriceCoin++;
        console.log(coin.name + " = $" + coin.current_price);
    }
});

console.log("Count: " + midPriceCoin);
```

## What This Demonstrates
- `filter()` for finding all matching items in an array
- `find()` for retrieving a single matching object
- `forEach()` for iterating, counting, and comparing values
- Chai assertion library (`pm.expect`) for type checks, comparisons, and existence checks
- Combining array methods with `Object.keys()` for nested object structures
