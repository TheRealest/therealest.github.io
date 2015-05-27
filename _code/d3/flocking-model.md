---
title: Flocking Model
---

```js
var coinsA = coins.reverse();
var makeChange = function(total) {
  var combos = [1];

  coinsA.forEach(function(coin) {
    for (var i = coin; i <= total; i++) {
      combos[i] = combos[i] || 0;

      combos[i] += combos[i-coin];
    }
  });

  return combos[total];
};
```