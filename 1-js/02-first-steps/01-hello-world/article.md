# გამარჯობა, სამყაროვ!

This part of the tutorial is about core JavaScript, the language itself.

სახელმძღვანელოს ეს ნაწილი ეხება JavaScript-ს, თვითონ ენას.

But we need a working environment to run our scripts and, since this book is online, the browser is a good choice. We'll keep the amount of browser-specific commands (like `alert`) to a minimum so that you don't spend time on them if you plan to concentrate on another environment (like Node.js). We'll focus on JavaScript in the browser in the [next part](/ui) of the tutorial.

მიუხედავად ამისა, გვჭირდება სამუშაო გარემო, რათა გავუშვათ ჩვენი სკრიპტები და, რადგან ეს გახლავთ ონლაინ-წიგნი, ბრაუზერი ამისთვის მშვენიერი არჩევანია. ჩვენ მინიმუმამდე დავიყვანთ ბრაუზერისთვის სპეციფიკური ბრძანებების (როგორიცაა `alert`) რაოდენობას, რათა მათზე დრო არ დაკარგოთ [იმ შემთხვევაში], თუ გეგმავთ, კონცენტრირდეთ სხვა გარემოზე (როგორიცაა Node.js). ბრაუზერში JavaScript-ის გამოყენებაზე ყურადღებას გავამახვილებთ სახელმძღვანელოს [შემდეგ ნაწილში](/ui).

So first, let's see how we attach a script to a webpage. For server-side environments (like Node.js), you can execute the script with a command like `"node my.js"`.

ამრიგად, თავდაპირველად ვნახოთ, როგორ ხდება ვებგვერდისათვის სკრიპტის მიბმა. სერვერის მხარის გარემოში (როგორიცაა Node.js) სკრიპტის გაშვება შეგიძლიათ შემდეგნაირი ბრძანებით: `„node my.js“`.


## The "script" tag
## ტეგი „script“

JavaScript programs can be inserted almost anywhere into an HTML document using the `<script>` tag.

JavaScript-პროგრამების ჩასმა შესაძლებელია HTML-დოკუმენტის მასშტაბით, `<script>` ტეგის გამოყენებით.

For instance:

მაგალითად:

```html run height=100
<!DOCTYPE HTML>
<html>

<body>

  <p>სკრიპტის წინ...</p>

*!*
  <script>
    alert( 'გამარჯობა, სამყაროვ!' );
  </script>
*/!*

  <p>...სკრიპტის შემდეგ.</p>

</body>

</html>
```

```online
You can run the example by clicking the "Play" button in the right-top corner of the box above.

ზემოთ მოცემული განყოფილების მარჯვენა ზედა კუთხეში არსებულ „Play“ ღილაკზე დაწკაპუნებით შეგიძლიათ მაგალითში მოცემული კოდი გაუშვათ.
```

The `<script>` tag contains JavaScript code which is automatically executed when the browser processes the tag.

`<script>` ტეგი შეიცავს JavaScript-კოდს, რომელიც, როგორც კი დამუშავდება ბრაუზერის მიერ, ავტომატურად ეშვება.


## Modern markup
## თანამედროვე მარკირება

The `<script>` tag has a few attributes that are rarely used nowadays but can still be found in old code:

`<script>` ტეგს გააჩნია რამდენიმე ატრიბუტი, რომლებიც დღესდღეობით იშვიათად გამოიყენება, მაგრამ მაინც გვხვდება ძველ კოდში:

The `type` attribute: <code>&lt;script <u>type</u>=...&gt;</code>

ატრიბუტი `type`: <code>&lt;script <u>type</u>=...&gt;</code>
: The old HTML standard, HTML4, required a script to have a `type`. Usually it was `type="text/javascript"`. It's not required anymore. Also, the modern HTML standard totally changed the meaning of this attribute. Now, it can be used for JavaScript modules. But that's an advanced topic, we'll talk about modules in another part of the tutorial.

: ძველი HTML-სტანდარტი, HTML4, სკრიპტისაგან მოითხოვდა `type`-ის ქონას. ჩვეულებრივ, მას ჰქონდა შემდეგი მნიშვნელობა: `type="text/javascript"`. ამჟამად ეს აღარ არის აუცილებელი. ამასთან, თანამედროვე HTML-სტანდარტმა მთლიანად შეცვალა ამ ატრიბუტის დატვირთვა. ახლა იგი შეიძლება გამოყენებულ იქნეს JavaScript-მოდულებისათვის. მაგრამ ამაზე საუბარი ჯერჯერობით ნაადრევია, მოდულების შესახებ სახელმძღვანელოს სხვა ნაწილში ვისაუბრებთ.

The `language` attribute: <code>&lt;script <u>language</u>=...&gt;</code>

ატრიბუტი `language`: <code>&lt;script <u>language</u>=...&gt;</code>
: This attribute was meant to show the language of the script. This attribute no longer makes sense because JavaScript is the default language. There is no need to use it.

: ამ ატრიბუტის დანიშნულება სკრიპტის ენის აღნიშვნა იყო. თუმცა, რადგან [ამჟამად] JavaScript-ი ნაგულისხმევი ენაა, ამ ატრიბუტის გამოყენებას აზრი აღარ აქვს.

Comments before and after scripts.

კომენტარები სკრიპტის წინ და სკრიპტის შემდეგ.
: In really ancient books and guides, you may find comments inside `<script>` tags, like this:

: ძალიან მოძველებულ წიგნებსა და სახელმძღვანელოებში შეიძლება წააწყდეთ კომენტარებს `<script>` ტეგის შიგნით, მაგალითად, შემდეგი სახით:

    ```html no-beautify
    <script type="text/javascript"><!--
        ...
    //--></script>
    ```

    This trick isn't used in modern JavaScript. These comments hide JavaScript code from old browsers that didn't know how to process the `<script>` tag. Since browsers released in the last 15 years don't have this issue, this kind of comment can help you identify really old code.

    ეს ხრიკი თანამედროვე JavaScript-ში აღარ გამოიყენება. აღნიშნული კომენტარი მალავდა JavaScript-კოდს ძველი ბრაუზერებისაგან, რომლებმაც არ იცოდნენ, როგორ დაემუშავებინათ `<script>` ტეგი. მას შემდეგ, რაც ბოლო 15 წლის განმავლობაში გამოშვებულ არც ერთ ბრაუზერს ეს პრობლემა აღარ აქვს, ერთადერთი, რაშიც მსგავსი კომენტარი შეიძლება დაგეხმაროთ ჭეშმარიტად უძველესი კოდის იდენტიფიცირებაა.


## External scripts
## გარე სკრიპტები

If we have a lot of JavaScript code, we can put it into a separate file.

თუ დიდი მოცულობით JavaScript-კოდი გვაქვს, შეგვიძლია იგი ცალკეულ ფაილში განვათავსოთ.

Script files are attached to HTML with the `src` attribute:

სკრიპტის ფაილების HTML-ისთვის მიბმა ხორციელდება `src` ატრიბუტით:

```html
<script src="/path/to/script.js"></script>
```

Here, `/path/to/script.js` is an absolute path to the script from the site root. One can also provide a relative path from the current page. For instance, `src="script.js"` would mean a file `"script.js"` in the current folder.

აქ `/path/to/script.js` არის აბსოლუტური მისამართი საიტის ფესვიდან — სკრიპტის ფაილამდე. ასევე შესაძლებელია მივუთითოთ ფარდობითი მისამართი მიმდინარე გვერდიდან. მაგალითად, `src="script.js"` [ან `src="./script.js"`] გულისხმობს, რომ `"script.js"` ფაილი განთავსებულია მიმდინარე საქაღალდეში.

We can give a full URL as well. For instance:

ასევე შეგვიძლია მივუთითოთ სრული URL-მისამართი. მაგალითად:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.11/lodash.js"></script>
```

To attach several scripts, use multiple tags:

რამდენიმე სკრიპტის მისაბმელად ვიყენებთ [ერთდროულად] მრავალ ტეგს:

```html
<script src="/js/script1.js"></script>
<script src="/js/script2.js"></script>
…
```

```smart
As a rule, only the simplest scripts are put into HTML. More complex ones reside in separate files.

როგორც წესი, HTML-ში თავსდება მხოლოდ უმარტივესი სკრიპტები, უფრო რთულები კი — ცალკეულ ფაილებში.

The benefit of a separate file is that the browser will download it and store it in its [cache](https://en.wikipedia.org/wiki/Web_cache).

ცალკეული ფაილის უპირატესობა ის გახლავთ, რომ ბრაუზერი მოახდენს მის ჩამოტვირთვას და [ქეშში](https://en.wikipedia.org/wiki/Web_cache) შენახვას.

Other pages that reference the same script will take it from the cache instead of downloading it, so the file is actually downloaded only once.

სხვა გვერდები, რომლებიც შეიცავენ იმავე სკრიპტს, ნაცვლად ჩამოტვირთვისა, ამოიღებენ მას ქეშიდან. ამრიგად, ფაილის ჩამოტვირთვა ფაქტობრივად მხოლოდ ერთხელ მოხდება.

That reduces traffic and makes pages faster.

ეს ამცირებს ტრაფიკს და გვერდებს უფრო სწრაფს ხდის.
```

````warn header="If `src` is set, the script content is ignored. | თუ `src` ატრიბუტი განსაზღვრულია, `script` ტეგის შიგთავსი უგულებელყოფილი იქნება."
A single `<script>` tag can't have both the `src` attribute and code inside.

ერთსა და იმავე `<script>` ტეგში ერთდროულად `src` ატრიბუტისა და შიგთავსის ქონა არ შეიძლება.   

This won't work:

ეს არ იმუშავებს:

```html
<script *!*src*/!*="file.js">
  alert(1); // the content is ignored, because src is set // შიგთავსი უგულებელყოფილია, რადგან განსაზღვრულია src ატრიბუტი
</script>
```

We must choose either an external `<script src="…">` or a regular `<script>` with code.

უნდა გავაკეთოთ არჩევანი: ან გარე სკრიპტი `<script src="…">`, ან ჩვეულებრივი კოდი `<script>` ტეგის შიგნით.

The example above can be split into two scripts to work:

ზემოთ მოყვანილი მაგალითი შეიძლება დაიყოს ორ სკრიპტად, რათა [სრულფასოვნად] იმუშავოს:

```html
<script src="file.js"></script>
<script>
  alert(1);
</script>
```
````

## Summary
## შეჯამება

- We can use a `<script>` tag to add JavaScript code to a page.
- გვერდისათვის JavaScript-კოდის დასამატებლად `<script>` ტეგი გამოიყენება.
- The `type` and `language` attributes are not required.
- `type` და `language` ატრიბუტები არასავალდებულოა.
- A script in an external file can be inserted with `<script src="path/to/script.js"></script>`.
- გარე ფაილად არსებული სკრიპტის ჩასმა შემდეგნაირად არის შესაძლებელი: `<script src="path/to/script.js"></script>`.


There is much more to learn about browser scripts and their interaction with the webpage. But let's keep in mind that this part of the tutorial is devoted to the JavaScript language, so we shouldn't distract ourselves with browser-specific implementations of it. We'll be using the browser as a way to run JavaScript, which is very convenient for online reading, but only one of many.

ჯერ კიდევ ბევრი რამ გვაქვს განსახილველი ბრაუზერის სკრიპტებისა და ვებგვერდთან მათი ურთიერთქმედების შესახებ, მაგრამ, როგორც უკვე აღვნიშნეთ, სახელმძღვანელოს ეს ნაწილი მიეძღვნა პროგრამირების ენა JavaScript-ს, ასე რომ, ნუ გაამახვილებთ ყურადღებას ბრაუზერთან დაკავშირებულ დეტალებზე. ჩვენ გამოვიყენებთ ბრაუზერს, როგორც გზას JavaScript-ის გასაშვებად, რაც ონლაინ კითხვის კონტექსტში ძალიან მოსახერხებელია, მაგრამ ეს არ გახლავთ ერთადერთი გზა.
