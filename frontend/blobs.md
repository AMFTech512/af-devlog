---
title: Blobs and Blob URLs
date: 2022-08-15T18:36:34Z
---

Blobs and Blob URLs
---

Learned from this stackoverflow post: https://stackoverflow.com/questions/30864573/what-is-a-blob-url-and-why-it-is-used

I was dev-tooling Youtube and found that they use a standard `<video>` tag to display the video, but the url was set to something weird like `blob:https://youtube.com/some-uuid`. I tried to follow the url but it didn't exist. It turns out that blob urls only exist on the client (it's like a virtual url). Basically, you can create a blob (which is basically an object with binary data) and create a url for it, and then any element on your page can use that url as its source. Pretty neat.

Here's some sample code that I wrote that generates an html page on the fly and renders it in an iframe:
```html
<!DOCTYPE html>
<html>
  <body>
    <h1>The iframe element</h1>

    <iframe
      id="frame"
      src="https://www.w3schools.com"
      title="W3Schools Free Online Web Tutorials"
    >
    </iframe>

    <script>
      // some html
      const html = `
        <html>
          <body>
            <h1>Hello world</h1>
          </body>
        </html>
      `;

      // encode the text into a UInt8Array (so it's like binary data)
      const enc = new TextEncoder();
      const buff = enc.encode(html);

      // create a blob from our UInt8Array
      const blob = new Blob([buff], { type: "text/html" });

      // grab the iframe
      const iframe = document.getElementById("frame");

      // create a url for the blob and set the iframe's src to that url
      const url = URL.createObjectURL(blob);
      iframe.src = url;
    </script>
  </body>
</html>
```

So Youtube probably implements the streaming logic for videos using some proprietary method in javascript, and then creates a raw video in memory via a blob and sets the url for the `<video>` tag to that blob.