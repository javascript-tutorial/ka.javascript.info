```js run demo
function* pseudoRandom(seed) {
  let value = seed;

  while(true) {
    value = value * 16807 % 2147483647
    yield value;
  }

};

let generator = pseudoRandom(1);

alert(generator.next().value); // 16807
alert(generator.next().value); // 282475249
alert(generator.next().value); // 1622650073
```

იგივე შეგვიძლია გავაკეთოთ ჩვეულებრივი ფუნქციითაც:

```js run
function pseudoRandom(seed) {
  let value = seed;

  return function() {
    value = value * 16807 % 2147483647;
    return value;
  }
}

let generator = pseudoRandom(1);

alert(generator()); // 16807
alert(generator()); // 282475249
alert(generator()); // 1622650073
```

მართალია ზემოთ მოცემული კოდი მუშაობს, მაგრამ ჩვენ ვკარგავთ `for..of`-ის გამოყენების შესაძლებლობასა და გენერატორების კომპოზიციას, რაც შეიძლება სხვაგან გამოგვადგეს.
