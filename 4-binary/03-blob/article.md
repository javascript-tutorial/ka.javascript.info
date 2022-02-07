# ბლობი

`ArrayBuffer` არის ECMA სტანდარტისა და შესაბამისად, ჯავასკრიპტის ნაწილი.

ბრაუზერში კიდევ არის სხვა მაღალი-ლეველის ობიექტები, რომლებიც აღწერილნი არიან [ფაილის API-ში](https://www.w3.org/TR/FileAPI/), მაგალითად `Blob`.

`Blob` შედგება ნებაყოფლობითი სტრინგ `type`-სგან (MIME-ტიპი ზოგადად), დამატებული ამას `blobParts` -- მიმდევრობა სხვა `Blob` ობიექტების, სტრინგებისა და `BufferSource`-ის.

![](blob.svg)

კონსტრუქტორის სინტაქსია ასეთია:

```js
new Blob(blobParts, options);
```

- **`blobParts`** არის `Blob`/`BufferSource`/`String` მნიშვნელობების მასივი.
- **`options`** ნებაყოფლობითი ობიექტი:
  - **`type`** -- `Blob` ტიპი, ზოგადად MIME-ტიპი, მაგალითად `image/png`,
  - **`endings`** -- გარდაქმნას თუ არა ხაზის ბოლო, რომ `Blob` შეუსაბამდეს მომუშავე ოპერაციული სისტემის ხაზის "დამასრულებლებს" (`\r\n` ან `\n`, newlines). დეფაულთად არის `"transparent"` (არაფერი გააკეთოს), მაგრამ ასევე შეიძლება იყოს `"native"` (გარდაქმნას).

მაგალითად:

```js
// სტრინგისგან შექმენი Blob
let blob = new Blob(["<html>…</html>"], {type: 'text/html'});
// დაიმახსოვრეთ: პირველი არგუმენტი არის მასივი [...]
```

```js
// მასივისა (რომელსაც მითითებული აქვს ტიპი) და სტრინგებისგან შექმენი Blob
let hello = new Uint8Array([72, 101, 108, 108, 111]); // "Hello" ბინარულ ფორმაში

let blob = new Blob([hello, ' ', 'world'], {type: 'text/plain'});
```

ჩვენ შეგვიძლია დავითრიოთ `Blob` სლაისები (slices) შემდეგნაირად:

```js
blob.slice([byteStart], [byteEnd], [contentType]);
```

- **`byteStart`** -- საწყისი ბაიტი, დეფაულთად 0.
- **`byteEnd`** -- ბოლო ბაიტი (არ ჩათვლით, დეფაულთად ბოლომდე).
- **`contentType`** -- ახალი ბლობის `type`, დეფაულთად წყაროს მნიშვნელობა.

არგუმენტები `array.slice`-ის მსგავსია, უარყოფითი რიცხვებიც მოსულა.

```smart header="`Blob` ობიექტს ვერ შეცვლი (immutable)"
`Blob`-ში მონაცემს პირდაპირ ვერ შევცვლით, მაგრამ შეგვიძლია `Blob`-ის სლაისი დავითრიოთ, ახალი `Blob` ობიექტი შევქმნათ მისგან, შევურიოთ ერთმანეთს და ა.შ.

ეს თვისება ჯავასკრიპტის სტრინგების მსგავსია: სტრინგში ქარაქტერს პირდაპირ ვერ შევცვლით, მაგრამ შეგვიძლია ახალი, "შესწორებული" სტრინგი შევქმნათ.
```

## ბლობი, როგორც URL

ბლობი, შიგთავსის საჩვენებლად, შეგვიძლია გამოვიყენოთ როგორც URL `<a>`, `<img>` ან სხვა თაგებისთვის.

`type`-ის დახმარებით, ასევე შეგვიძლია ავტვირთოთ/ჩამოვტვირთოთ `Blob` ობიექტები, და `type` თავისით გარდაიქმნება `Content-Type`-ად ქსელის რექუესთებში.

მოდი მარტივი მაგალითით დავიწყოთ. ლინკზე დაჭერისას დინამიურად დაგენერირებული `Blob` ფაილი, შიგთავსით `hello world`, ჩამოვტვირთოთ.

```html run
<!-- download ატრიბუტი ბრაუზერს, ნავიგაციის მაგივრად, აიძულებს ჩამოტვირთვას -->
<a download="hello.txt" href='#' id="link">ჩამოტვირთე</a>

<script>
let blob = new Blob(["Hello, world!"], {type: 'text/plain'});

link.href = URL.createObjectURL(blob);
</script>
```

ჩვენ ასევე შეგვიძლია, შევქმნათ ლინკი დინამიურად ჯავასკრიპტში და "დავაკლიკოთ" `link.click()`-ით, და შემდეგ ჩამოტვირთვა დაიწყება ავტომატურად.

განვიხილოთ დაახლოებით იგივე კოდი, რომელიც ჩამოტვირთავს დინამიურად შექმნილ `Blob`-ს, HTML-ის გარეშე:

```js run
let link = document.createElement('a');
link.download = 'hello.txt';

let blob = new Blob(['Hello, world!'], {type: 'text/plain'});

link.href = URL.createObjectURL(blob);

link.click();

URL.revokeObjectURL(link.href);
```

`URL.createObjectURL` არგუმენტად იღებს `Blob`-ს და მისგან ქმნის უნიკალურ URL-ს, ფორმით `blob:<origin>/<uuid>`.

`link.href`-ის მნიშვნელობა ასე გამოიყურება:

```
blob:https://javascript.info/1e67e00e-860d-40a5-89ae-6ab0cbee6273
```

`URL.createObjectURL`-ით დაგენერირებული თითოეული URL-თვის, ბრაუზერი ინახავს URL -> `Blolb` მაპინგს (mapping). ასე რომ, ასეთი URL არის მოკლე, მაგრამ `Blob`-თან დაკავშირების საშუალებას გვაძლევს.

დაგენერირებული URL (და შესაბამისად, ლინკიც) ვალიდურია მხოლოდ ამჟამიდელ დოკუმენტისთვის, სანამ ღიაა და არსებობს. და `<img>`-ს, `<a>`-ს (და ნებისმიერ ობიექტს, რომელიც URL მოელის) თაგებს საშუალებას აძლევს მიაწოდოს `Blob`.

მაგრამ რაღაც მინუსი მაინცაა. მართალია, `Blob`-ზე "მიმთითებელი" გვაქვს, მაგრამ თვითონ `Blob` მეხსიერებაში რჩევა და ბრაუზერი თავისით ვერ ათავისუფლებს მაგ მეხსიერებას.

მიმთითებელი მაშინ გათავისუფლდება როდესაც დოკუმენტს "დავტოვებთ" (unload), და შესაბამისად, `Blob` ობიექტებიც მაშინ გათავისუფლდებიან. მაგრამ თუ აპლიკაცია დიდი ხანი ცოცხლობს, მაშინ ცუდ პონტში ვართ.

**ანუ, თუ ჩვენ `Blob`-გან შევქმნით URL-ს, ეგ `Blob` მეხსიერებაში დარჩება, მიუხედავად იმისა რომ აღარ ვიყენებთ.**

`URL.revokeObjectURL(url)` გვაძლევს იმის საშუალებას, გავაკეთოთ ზუსტად ის, რასაც ბრაუზერი აკეთებს, როდესაც დოკუმენტს დავტოვებთ.

ბოლო მაგალითში, `Blob`-ს მხოლოდ ერთხელ ვიყენებთ, ჩამოსატვირთად, ასე რომ შეგვიძლია `URL.revokeObjectURL(link.href)` გამოვიძახოთ.

ბოლოს წინა, დაკლიკებადი HTML-ლინკის, მაგალითში, არ ვიძახებთ `URL.revokeObjectURL(link.ref)`-ს, რადგან მაშინ `Blob` არავალიდური გახდება.

## ბლობის გარდაქმნა base64-ად

`URL.createObjectURL`-ის მაგივრად, `Blob` შეგვიძლია გარდავქმნათ base64-encoded სტრინგად.

ეგ კოდირება, ბინარულ მონაცემს წარმოადგენს როგორც სტრინგს, ულტრა-უსაფრთხო, "წაკითხვად" ASCII-კოდის ქარაქტერებად, 0-დან 64-მდე. ყველაზე მნიშვნელოვანი ისაა, რომ ეს კოდირება შეგვიძლია გამოვიყენოთ "მონაცემთა URL-ებში".

[მონაცემთა URL](mdn:/http/Data_URIs)-ს აქვს `data:[<mediatype>][;base64],<data>` ფორმა. ასეთი URL-ები ნებისმიერ ადგილას შეგვიძლია გამოვიყენოთ, "ჩვეულებრივ" URL-თან ერთად.

მაგალითად, სმაილი:

```html
<img src="data:image/png;base64,R0lGODlhDAAMAKIFAF5LAP/zxAAAANyuAP/gaP///wAAAAAAACH5BAEAAAUALAAAAAAMAAwAAAMlWLPcGjDKFYi9lxKBOaGcF35DhWHamZUW0K4mAbiwWtuf0uxFAgA7">
```

ბრაუზერის დაადეკოდირებს სტრინგს და მოგვცვემს იმიჯს: <img src="data:image/png;base64,R0lGODlhDAAMAKIFAF5LAP/zxAAAANyuAP/gaP///wAAAAAAACH5BAEAAAUALAAAAAAMAAwAAAMlWLPcGjDKFYi9lxKBOaGcF35DhWHamZUW0K4mAbiwWtuf0uxFAgA7">

`Blob` base64-ად რომ გარდავქმნათ, გამოვიყენებთ ჩაშენებულ `FileReader` ობიექტს. მას ბლობიდან მონაცემის წაკითხვა რამდენიმე ფორმატში შეუძლია. [შემდეგ თავში](info:file) უფრო დეტალურად ვისაუბრებთ.

განვიხილოთ ბლობის ჩამოტვირთვის მაგალითი, ამჯერად base-64-ით:

```js run
let link = document.createElement('a');
link.download = 'hello.txt';

let blob = new Blob(['Hello, world!'], {type: 'text/plain'});

*!*
let reader = new FileReader();
reader.readAsDataURL(blob); // გარდაქმნის ბლობს base64-ად და იძახებს onload-ს
*/!*

reader.onload = function() {
  link.href = reader.result; // მონაცემის URL
  link.click();
};
```

`Blob`-დან URL-ის შექმნის ორივე საშუალება მისაღებია. მაგრამ ზოგადად `URL.createObjectURL(blob)` უფრო მარტივი და სწრაფია. 

```compare title-plus="URL.createObjectURL(blob)" title-minus="Blob-ის გარდაქმნა მონაცემის URL-ად"
+ ჩვენით უნდა გავათავისუფლოთ მეხსიერება.
+ ბლობზე პირდაპირი წვდომა, არავითარი "კოდირება/დეკოდირება"
- არაფრის გათავისუფლდება არაა საჭირო.
- სისწრაფესა და მეხსიერება დაიკარგება თუ `Blob` ობიექტი ძალიან დიდია.
```

## იმიჯის გარდაქმნა ბლობად

ჩვენ შეგვიძლია იმიჯის, იმიჯის ნაწილის, ან მთლიანი გვერდის სკრინშოთის `Blob` შევქმნათ. საკაიფოა, თუ სადმე ატვირთვა გვსურს.

იმიჯის ოპერაციები სრულდება `<canvas>` ელემენტით:

1. დახატე იმიჯი (ან მისი ნაწილი) კანვასზე [canvas.drawImage](mdn:/api/CanvasRenderingContext2D/drawImage)-ის გამოყენებით.
2. გამოიძახე კანვასის მეთოდი [.toBlob(callback, format, quality)](mdn:/api/HTMLCanvasElement/toBlob), რომელიც ქმნის `Blob`-ს და დასრულებისას იძახებს `callback`-ს.

ქვემო მაგალითში იმიჯს უბრალდო ვაკოპირებთ, მაგრამ რასაც გვინდა იმას ვუზამთ, სანამ კანვასზე გადავიტანთ და `Blob`-ად გარდავქმნით:

```js run
// დავითრიოთ იმიჯი
let img = document.querySelector('img');

// შევქმნათ იმავე ზომის <canvas>
let canvas = document.createElement('canvas');
canvas.width = img.clientWidth;
canvas.height = img.clientHeight;

let context = canvas.getContext('2d');

// ვაჭამოთ იმიჯი (ეს მეთოდი სურათის ჩამოჭრის საშუალებას გვაძლევს)
context.drawImage(img, 0, 0);
// ჩვენ შეგვიძლია, context.rotate() (შემობრუნება), და ბევრი სხვა რამე გავაკეთოთ კანვასზე.

// toBlob არის ასინქრონული ოპერაცია, როდესაც მორჩება, გამოიძახება callback
canvas.toBlob(function(blob) {
  // ბლობი მზადაა, ჩამოტვირთე
  let link = document.createElement('a');
  link.download = 'example.png';

  link.href = URL.createObjectURL(blob);
  link.click();

  // წაშალე ბლობზე მიმთითებელი, რომ ბრაუზერს მივცეთ მეხსიერების გასუფთავების საშუალება
  URL.revokeObjectURL(link.href);
}, 'image/png');
```

თუ `async/await` გვირჩევნია callback-ების მაგივრად:
```js
let blob = await new Promise(resolve => canvasElem.toBlob(resolve, 'image/png'));
```

გვერდის დასასკრინშოთებლად, შეგვიძლია გამოვიყენოთ ბიბლიოთეკა როგორიცაა <https://github.com/niklasvh/html2canvas>. შემდეგ შეგვიძლია `Blob` წინა მაგალითისნაირად დავითრიოთ.

## ბლობიდან ArrayBuffer-მდე

`Blob` კონსტრუქტორი საშუალებას გვაძლევს შევქმნათ ბლობი (თითქმის) ნებისმიერი რამისგან, `BufferSource`-ის ჩათვლით.

<<<<<<< HEAD
მაგრამ თუ გვინდა უფრო დაბალ-დონეზე დამუშავება, შეგვიძლია უფრო ლოუ-ლეველის `FileReader`-ის `ArrayBuffer` გამოვიყენოთ.

```js
// დაითრიე ArrayBuffer ბლობიდან
let fileReader = new FileReader();
=======
But if we need to perform low-level processing, we can get the lowest-level `ArrayBuffer` from `blob.arrayBuffer()`:

```js
// get arrayBuffer from blob
const bufferPromise = await blob.arrayBuffer();
>>>>>>> 71da17e5960f1c76aad0d04d21f10bc65318d3f6

// or
blob.arrayBuffer().then(buffer => /* process the ArrayBuffer */);
```

## From Blob to stream

When we read and write to a blob of more than `2G`, the use of `arrayBuffer` becomes more memory intensive for us. At this point, we can directly convert the blob to a stream.

A stream is a special object that allows to read from it (or write into it) portion by portion. It's outside of our scope here, but here's an example, and you can read more at <https://developer.mozilla.org/en-US/docs/Web/API/Streams_API>. Streams are convenient for data that is suitable for processing piece-by-piece.

The `Blob` interface's `stream()` method returns a `ReadableStream` which upon reading returns the data contained within the `Blob`.

Then we can read from it, like this:

```js
// get readableStream from blob
const readableStream = blob.stream();
const stream = readableStream.getReader();

while (true) {
  // for each iteration: data is the next blob fragment
  let { done, data } = await stream.read();
  if (done) {
    // no more data in the stream
    console.log('all blob processed.');
    break;
  }

   // do something with the data portion we've just read from the blob
  console.log(data);
}
```

## შეჯამება

`ArrayBuffer`, `Uint8Array` და სხვა `BufferSource`-ები არიან "ბინარული მონაცემები", ხოლო [ბლობი](https://www.w3.org/TR/FileAPI/#dfn-Blob) "ბინარული მონაცემი ტიპით".

ამიტომაც, ის გამოსადეგია ატვირთვა/ჩამოტვირთის ოპერაციებისთვის.

მეთოდებს, რომლებიც ვებ-რექუესთებს აგზავნიან, როგორიცაა [XMLHttpRequest](info:xmlhttprequest), [fetch](info:fetch) და ა.შ., შეუძლიათ `Blob`-თან მუშაობა, ისევე როგორც სხვა ბინარულ ტიპებთან.

ჩვენ მარტივად შეგვიძლია გარდავქმნათ `Blob` და დაბალი-დონის ბინარული მონაცემთა ტიპები ერთმანეთში:

<<<<<<< HEAD
- `new Blob(...)` კონსტრუქტორის გამოყენებით "typed" მასივისგან შეგვიძლია შევქმნათ `Blob`.
- შეგვიძლია უკან დავიბრუნოთ `ArrayBuffer` `Blob`-გან, `FileReader`-ის გამოყენებით.
=======
- We can make a `Blob` from a typed array using `new Blob(...)` constructor.
- We can get back `ArrayBuffer` from a Blob using `blob.arrayBuffer()`, and then create a view over it for low-level binary processing.

Conversion streams are very useful when we need to handle large blob. You can easily create a `ReadableStream` from a blob. The `Blob` interface's `stream()` method returns a `ReadableStream` which upon reading returns the data contained within the blob.
>>>>>>> 71da17e5960f1c76aad0d04d21f10bc65318d3f6
