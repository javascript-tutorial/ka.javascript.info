
# ასინქრონული იტერაცია და გენერატორები

ასინქრონული იტერაცია საშუალებას გვაძლევს გადავუყვეთ ასინქრონულად მიღებულ მონაცემს. მაგალითად, როდესაც ქსელიდან ნაწილ-ნაწილ ვიწერთ რაიმეს. ასინქრონული გენერატორები ამ ყველაფერს იმპლემენტაციას უფრო კომფორტულს ხდის.

მოდი ჯერ მარტივი მაგალითი ვნახოთ, გავიგოთ სინტაქსი როგორია და შემდეგ რეალური მაგალითი განვიხილოთ.

## გავიხსენოთ იტერატორები

მოდი გავიხსენოთ იტერატორების თავი.

ანუ გვაქვს ობიექტი, როგირიცაა `range`:
```js
let range = {
  from: 1,
  to: 5
};
```

...და გვინდა გამოვიყენოთ `for..of` ციკლი, როგორიცაა `for(value of range)`, რომ დავითრიოთ მნიშვნელობები `1`-დან `5`-მდე.

სხვა სიტყვებით, ჩვენ გვინდა *იტერაციის უნარი* დავუმატოთ ობიექტს.

ამის მისაღწევად საჭიროა სპეციალური მეთოდის გამოყენება `Symbol.iterator`:

- ამ მეთოდს გამოიძახებს `for..of` ციკლის დასაწყისში გამოიძახებს, და მან უნდა დააბრუნოს ობიექტი `next` მეთოდით.
- ყოველ იტერაციაზე, `next()` მეთოდი შემდეგი მნიშვნელობის მისაღებად გამოიძახება.
- `next()`-მდა უნდა დააბრუნოს მნიშვნელობა ფორმით `{done: true/false, value:<ციკლის მნიშვნელობა>}`, სადაც `done:true` ციკლის დასასრულს ნიშნავს.

ქვემოთ მოცემულია იტერირებადი `range`-ის იმპლემენტაცია:

```js run
let range = {
  from: 1,
  to: 5,

*!*
  [Symbol.iterator]() { // გამოიძახება მხოლოდ ერთხელ, for..of-ის დასაწყისში
*/!*
    return {
      current: this.from,
      last: this.to,

*!*
      next() { // გამოიძახება ყოველ იტერაციაზე შემდეგი მნიშვნელობის დასათრევად
*/!*
        if (this.current <= this.last) {
          return { done: false, value: this.current++ };
        } else {
          return { done: true };
        }
      }
    };
  }
};

for(let value of range) {
  alert(value); // 1 then 2, then 3, then 4, then 5
}
```

თუ რამე გაუგებარია, ეწვიეთ თავს [იტერატორები](info:iterable), ჩვეულებრივ იტერატორებზე ინფორმაციის მისაღებად.

## ასინქრონული იტერატორები

ასინქრონული იტერაცია საჭიროა როდესაც მნიშვნელობებს ასინქრონულად ვიღებთ: `setTimeout`-ის ან მაგდაგვარი შემფერხებლის შემდეგ.

ყველაზე ხშირი შემთხვევაა როდესაც ობიექტი ქსელს რექუესთს გაუგზავნის რომ შემდეგი მნიშვნელობა მიიღოს, ცოტა ხანში რეალურ მაგალითსაც განვიხილავთ.

იმისათვის რომ ობიექტი ასინქრონულად იტერირებადი ვაქციოთ, უნდა:

1. გამოვიყენოთ `Symbol.asyncIterator` `Symbol.iterator`-ის მაგივრად.
2. `next()` მეთოდმა უნდა დააბრუნოს "დაპირება" (promise, და მეც "ფრომისს" გამოვიყენებ), რათა შემდეგი მნიშვნელობით შეივსოს.
    - `async` ქივორდი მიხედავს ამ სიტუაციას, შეგვიძლია პირდაპირ `async next()` გამოვიყენოთ.
3. ასეთ ობიექტს რომ გადავუყვეთ, უნდა გამოვიყენოთ `for await (let item of iterable)` ციკლი.
    - ყურადღება მიაქციეთ `await` სიტყვას.

მოდი დასაწყისისთვის შევქმნათ წინა `range`-ის მაგვარი ობიექტი, მაგრამ ამჯერაც ის დააბრუნებტს ასინქრონულ მნიშვნელობებს ყოველ ერთ წამში.

მხოლო რამდენიმე ცვლილების შეტანა მოგვიწევს.

```js run
let range = {
  from: 1,
  to: 5,

*!*
  [Symbol.asyncIterator]() { // (1)
*/!*
    return {
      current: this.from,
      last: this.to,

*!*
      async next() { // (2)
*/!*

*!*
        // დააკვირდით, ჩვენ შეგვიძლია "await" გამოვიყენოთ async next-ში:
        await new Promise(resolve => setTimeout(resolve, 1000)); // (3)
*/!*

        if (this.current <= this.last) {
          return { done: false, value: this.current++ };
        } else {
          return { done: true };
        }
      }
    };
  }
};

(async () => {

*!*
  for await (let value of range) { // (4)
    alert(value); // 1,2,3,4,5
  }
*/!*

})()
```

როგორცე ხედავთ, იგივე სტრუქტურა აქვს როგორც ჩვეულებრივ იტერატორს:

1. იმისათვის რომ ობიექტი გავხადოთ ასინქრონულად იტერირებადი, მას უნდა ჰქონდეს მეთOდი `Symbol.asyncIterator` `(1)`.
2. ამ მეთოდმა უნდა დააბრუნოს ობიექტი `async next()` მეთოდით `(2)`.
3. `next()` მეთოდი არაა სავალდებულო იყოს `async`, ის შეიძლება იყოს ჩვეულებრივი მეთოდი რომელიც აბრუნებს ფრომისს, მაგრამ `async` საშუალებას გვაძლევს `await`-ის გამოყენებას და ამიტომ უფრო კომფორტულია. `(3)` აქ ჩვენ უბრალოდ 1 წამი ვაფერხებთ.
4. იტერაციის ჩვენ ვიყენებთ `for await (let value of range)` `(4)`, არ დაგავიწყდეთ "await"-ის დამატება "for"-ის შემდეგ. ის იძახებს `range[Symbol.asyncIterator]()`-ს ერთხელ, და შემდეგ `next()`-ს მომდევნო მნიშვნელობებისთვის.

ქვემოთ ცხრილში მითითებულია განსხვავებები:

|       | იტერატორები | ასინქრონული იტერატორები |
|-------|-----------|-----------------|
| ობიექტის მეთოდი იტერაციის გადასაცემად | `Symbol.iterator` | `Symbol.asyncIterator` |
| `next()` რასაც აბრუნებს              | any value         | `Promise`  |
| იტერაციისთვის გამოყენებული ციკლი                          | `for..of`         | `for await..of` |

````warn header="გაშლის ოპერატორს `...` ასინქრონულ სიტუაციაში ვერ გამოვიყენებთ"
Features that require regular, synchronous iterators, don't work with asynchronous ones.
სინქრონული იტერატორების თვისებებს ასინქრონულებთან ვერ გამოვიყენებთ.

მაგალითად, გაშლი ოპერატორი არ იმუშავებს:
```js
alert( [...range] ); // Error, no Symbol.iterator
```

ანუ ბუნებრივია, რადგან ის ცდილობს მოძებნოს `Symbol.iterator` და არა `Symbol.asyncIterator`. 

იგივე სიტუაციაა `for..of`-თვის: `await`-ის გარეშე მხოლოდ `Symbol.iterator`-თან შეგვიძლია გამოვიყენოთ.
````

## Recall generators

Now let's recall generators, as they allow to make iteration code much shorter. Most of the time, when we'd like to make an iterable, we'll use generators.

For sheer simplicity, omitting some important stuff, they are "functions that generate (yield) values". They are explained in detail in the chapter [](info:generators).

Generators are labelled with `function*` (note the star) and use `yield` to generate a value, then we can use `for..of` to loop over them.

This example generates a sequence of values from `start` to `end`:

```js run
function* generateSequence(start, end) {
  for (let i = start; i <= end; i++) {
    yield i;
  }
}

for(let value of generateSequence(1, 5)) {
  alert(value); // 1, then 2, then 3, then 4, then 5
}
```

As we already know, to make an object iterable, we should add `Symbol.iterator` to it.

```js
let range = {
  from: 1,
  to: 5,
*!*
  [Symbol.iterator]() {
    return <object with next to make range iterable>
  }
*/!*
}
```

A common practice for `Symbol.iterator` is to return a generator, it makes the code shorter, as you can see:

```js run
let range = {
  from: 1,
  to: 5,

  *[Symbol.iterator]() { // a shorthand for [Symbol.iterator]: function*()
    for(let value = this.from; value <= this.to; value++) {
      yield value;
    }
  }
};

for(let value of range) {
  alert(value); // 1, then 2, then 3, then 4, then 5
}
```

Please see the chapter [](info:generators) if you'd like more details.

In regular generators we can't use `await`. All values must come synchronously, as required by the `for..of` construct.

What if we'd like to generate values asynchronously? From network requests, for instance. 

Let's switch to asynchronous generators to make it possible.

## Async generators (finally)

For most practical applications, when we'd like to make an object that asynchronously generates a sequence of values, we can use an asynchronous generator.

The syntax is simple: prepend `function*` with `async`. That makes the generator asynchronous.

And then use `for await (...)` to iterate over it, like this:

```js run
*!*async*/!* function* generateSequence(start, end) {

  for (let i = start; i <= end; i++) {

*!*
    // Wow, can use await!
    await new Promise(resolve => setTimeout(resolve, 1000));
*/!*

    yield i;
  }

}

(async () => {

  let generator = generateSequence(1, 5);
  for *!*await*/!* (let value of generator) {
    alert(value); // 1, then 2, then 3, then 4, then 5 (with delay between)
  }

})();
```

As the generator is asynchronous, we can use `await` inside it, rely on promises, perform network requests and so on.

````smart header="Under-the-hood difference"
Technically, if you're an advanced reader who remembers the details about generators, there's an internal difference.

For async generators, the `generator.next()` method is asynchronous, it returns promises.

In a regular generator we'd use `result = generator.next()` to get values. In an async generator, we should add `await`, like this:

```js
result = await generator.next(); // result = {value: ..., done: true/false}
```
That's why async generators work with `for await...of`.
````

### Async iterable range

Regular generators can be used as `Symbol.iterator` to make the iteration code shorter.

Similar to that, async generators can be used as `Symbol.asyncIterator` to implement the asynchronous iteration.

For instance, we can make the `range` object generate values asynchronously, once per second, by replacing synchronous `Symbol.iterator` with asynchronous `Symbol.asyncIterator`:

```js run
let range = {
  from: 1,
  to: 5,

  // this line is same as [Symbol.asyncIterator]: async function*() {
*!*
  async *[Symbol.asyncIterator]() {
*/!*
    for(let value = this.from; value <= this.to; value++) {

      // make a pause between values, wait for something  
      await new Promise(resolve => setTimeout(resolve, 1000));

      yield value;
    }
  }
};

(async () => {

  for *!*await*/!* (let value of range) {
    alert(value); // 1, then 2, then 3, then 4, then 5
  }

})();
```

Now values come with a delay of 1 second between them.

```smart
Technically, we can add both `Symbol.iterator` and `Symbol.asyncIterator` to the object, so it's both synchronously (`for..of`) and asynchronously (`for await..of`) iterable.

In practice though, that would be a weird thing to do.
```

## Real-life example: paginated data

So far we've seen basic examples, to gain understanding. Now let's review a real-life use case.

There are many online services that deliver paginated data. For instance, when we need a list of users, a request returns a pre-defined count (e.g. 100 users) - "one page", and provides a URL to the next page.

This pattern is very common. It's not about users, but just about anything. 

For instance, GitHub allows us to retrieve commits in the same, paginated fashion:

- We should make a request to `fetch` in the form `https://api.github.com/repos/<repo>/commits`.
- It responds with a JSON of 30 commits, and also provides a link to the next page in the `Link` header.
- Then we can use that link for the next request, to get more commits, and so on.

For our code, we'd like to have a simpler way to get commits.

Let's make a function `fetchCommits(repo)` that gets commits for us, making requests whenever needed. And let it care about all pagination stuff. For us it'll be a simple async iteration `for await..of`.

So the usage will be like this:

```js
for await (let commit of fetchCommits("username/repository")) {
  // process commit
}
```

Here's such function, implemented as async generator:

```js
async function* fetchCommits(repo) {
  let url = `https://api.github.com/repos/${repo}/commits`;

  while (url) {
    const response = await fetch(url, { // (1)
      headers: {'User-Agent': 'Our script'}, // github needs any user-agent header
    });

    const body = await response.json(); // (2) response is JSON (array of commits)

    // (3) the URL of the next page is in the headers, extract it
    let nextPage = response.headers.get('Link').match(/<(.*?)>; rel="next"/);
    nextPage = nextPage?.[1];

    url = nextPage;

    for(let commit of body) { // (4) yield commits one by one, until the page ends
      yield commit;
    }
  }
}
```

More explanations about how it works:

1. We use the browser [fetch](info:fetch) method to download the commits.

    - The initial URL is `https://api.github.com/repos/<repo>/commits`, and the next page will be in the `Link` header of the response.
    - The `fetch` method allows us to supply authorization and other headers if needed -- here GitHub requires `User-Agent`.
2. The commits are returned in JSON format.
3. We should get the next page URL from the `Link` header of the response. It has a special format, so we use a regular expression for that (we will learn this feature in [Regular expressions](info:regular-expressions)).
    - The next page URL may look like `https://api.github.com/repositories/93253246/commits?page=2`. It's generated by GitHub itself.
4. Then we yield the received commits one by one, and when they finish, the next `while(url)` iteration will trigger, making one more request.

An example of use (shows commit authors in console):

```js run
(async () => {

  let count = 0;

  for await (const commit of fetchCommits('javascript-tutorial/en.javascript.info')) {

    console.log(commit.author.login);

    if (++count == 100) { // let's stop at 100 commits
      break;
    }
  }

})();

// Note: If you are running this in an external sandbox, you'll need to paste here the function fetchCommits described above 
```

That's just what we wanted. 

The internal mechanics of paginated requests is invisible from the outside. For us it's just an async generator that returns commits.

## Summary

Regular iterators and generators work fine with the data that doesn't take time to generate.

When we expect the data to come asynchronously, with delays, their async counterparts can be used, and `for await..of` instead of `for..of`.

Syntax differences between async and regular iterators:

|       | Iterable | Async Iterable |
|-------|-----------|-----------------|
| Method to provide iterator | `Symbol.iterator` | `Symbol.asyncIterator` |
| `next()` return value is          | `{value:…, done: true/false}`         | `Promise` that resolves to `{value:…, done: true/false}`  |

Syntax differences between async and regular generators:

|       | Generators | Async generators |
|-------|-----------|-----------------|
| Declaration | `function*` | `async function*` |
| `next()` return value is          | `{value:…, done: true/false}`         | `Promise` that resolves to `{value:…, done: true/false}`  |

In web-development we often meet streams of data, when it flows chunk-by-chunk. For instance, downloading or uploading a big file.

We can use async generators to process such data. It's also noteworthy that in some environments, like in browsers, there's also another API called Streams, that provides special interfaces to work with such streams, to transform the data and to pass it from one stream to another (e.g. download from one place and immediately send elsewhere).
