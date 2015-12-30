#Website Performance Optimization Techniques

## Preface
Every day performance is becoming more important. Users expect websites to behave the same way as desktop applications in terms of speed and general performance. That&#8217;s why we, as developers, should make an effort to provide the best experience to our visitors.

There is an increasing interest in what is called WPO (Web Performance Optimization). This is a set of different techniques you can use to improve your website, depending on the area of optimization you want to target. If you are like me and enjoy improving sites performance you might find this list useful.

I intend to keep this list as updated as possible. If you find mistakes or non-listed techniques, just let me know. I&#8217;d really appreciate your feedback.

##### Table of Contents
- [What to optimize](#what-to-optimize)
- [Improve download time](#improve-download-time)
- [Reduce HTTP requests](#reduce-http-requests)
- [Reduce web traffic](#reduce-web-traffic)
- [Browser rendering](#browser-rendering)
- [Others](#others)

## What to optimize

When talking about optimizing web sites we can be focusing on improving the load time for our site, or maybe decrease the amount of bytes needed to render it.

Apart from monitoring the metric we want to improve, it is important that we also monitor metrics like amount of users / sessions, time on site, and user's performed actions. An improvement in load time or page size are meaningless if they don't improve (or even make worse) the other important ones.

This article will divide the different techniques in sections depending on what each of them try to improve.

### A note on page load time

Page load time used to be a key metric to optimize. However, it doesn't reflect how the page is actually rendered.

A page could be blank for 5 seconds due to a blocking script and then load the rest of content in 1 second, or it could render immediately and load some non-blocking third-party scripts that take several seconds. In both cases we could have a page load time of 6 seconds, though the user experience in the second one is way better.

Metrics like [Speed Index](https://sites.google.com/a/webpagetest.org/docs/using-webpagetest/metrics/speed-index) are better in the sense that they measure how fast the visible areas of a page are rendered, which matches more accurately how users perceive the page.

## Improve download time

### Use a server near your user (or improving your RTT)

The fact of hosting your website geographically far from your target users can increase the round-trip delay time of your requests. Have a look at recent [Mike Bailey&#8217;s article](http://mike.bailey.net.au/blog/?p=38) measuring RTT over a server in the USA and one in Australia. If you see yourself in this situation, consider changing your hosting server or using cloud servers such [Amazon&#8217;s S3](http://aws.amazon.com/s3/).

## Reduce HTTP requests

### Combination

By combining your requests you will improve your website load time. So instead of requesting 3 or 4 CSS files, try to combine them into one single file. The same applies to JS files. Personally I find useful to develop using multiple files and in a production stage combine them using a dynamic combiner. For instance an own function that minifies, combines and caches the output in the server.

### CSS image sprites

As simple as merging several image files into a single one and use them from your css as background images, setting its position to the correct values.

### CSS Data URIs

Instead of image sprites you can take a look at [Data URIs](http://www.nczonline.net/blog/2010/07/06/data-uris-make-css-sprites-obsolete/). It can make you get rid off of big, non-maintainable images files. Just take into account that IE6 and IE7 don&#8217;t support this feature, and you should follow the [MHTML workaround](http://www.phpied.com/mhtml-when-you-need-data-uris-in-ie7-and-under/). Note that, [according to researches](http://www.mobify.com/blog/data-uris-are-slow-on-mobile/), decoding Data URIs on mobile devices may be much slower than loading these resources from cache. You need to consider whether saving a request is worth it compared to the introduced overhead.

### Use CSS3 instead of images

[Gradients](http://www.webdesignerwall.com/tutorials/cross-browser-css-gradient/), [patterns](http://leaverou.me/2010/12/checkered-stripes-other-background-patterns-with-css3-gradients/) and [triangles](http://www.dinnermint.org/blog/css/creating-triangles-in-css/) can be made using CSS/CSS3. Not only do they reduce the number of requests, but they also improve maintainability since changes can be made very quickly.

### Multipart XHR

If the browser supports canvas (and javascript is enabled) it is possible to send several resources over the wire as a string formed by chunks of data concatenated, using a known string as a separator. Thus, in a single request reducing web traffic. The browser makes more work, having to split the data, but it is still a nice solution, improving load times in several magnitudes.

It is nicely explained on [this video from Yahoo Developer Network](http://developer.yahoo.com/yui/theater/video.php?v=zakas-hpjs) where Ross Harmes (from minute 44) explains how Yahoo has planned to use it to the Flickr mobile version.

There are some caching (and IE) issues, but it may be useful for mobile sites targeting well known modern browsers.

### Facebook&#8217;s BigPipe

[BigPipe](http://www.facebook.com/notes/facebook-engineering/bigpipe-pipelining-web-pages-for-high-performance/389414033919) consists of early flushing main content, while deferring a set of pagelets that are composed by asynchronous threads and injected into their corresponding containers using Javascript.

This allows users to see the main content as early as possible, while generating in parallel the content of different side areas, that are served by flushing in the very moment they are ready. This way, if an area needs more time to get completed, it will not affect the whole page.

Although the technique was introduced in 2010, some sites are embracing it these days. An example is the [Drupal](http://wimleers.com/blog/drupal-8-bigpipe-module-ready), CMS, which has included it in its 8.0.0 version.

### Caching

Setting a cache strategy will make the browser don&#8217;t request again a file that has already downloaded. This is useful for static content that is not frequently changed or if we have implemented a system to serve newer versions of the file seamlessly (by last-modified or e-tag header, or by appending a version number to the query string). Or maybe it is just not a problem to have a client using a non-updated version of the file (in example, a body background).

### Avoid 301 redirects

By linking consistently to correct URLs across your website you will save the browser from requesting a URL, wait for a redirect message to finally request the right URL.

### Service workers

Service workers allow us to intercept browser requests. This allows offline capabilities and selective caching of resources. In practice, this means that, although we don't strictly reduce the amount of HTTP requests, we can return a response quicker than if it had to hit a server.

I recommend you to check the [Service Worker Recipes](https://github.com/GoogleChrome/samples/tree/gh-pages/service-worker) and [Demos](https://github.com/w3c-webmob/ServiceWorkersDemos#basic-demos) to learn more about them.

## Reduce web traffic

### Minification

Smaller files sizes can be achieved by minimizing content before sending it to the wire. Javascript and CSS files are well known to be easily minified (you can serve a minified copy of your file or let your server minify it on-the-air). HTML can also be minified by, for instance, [removing white spaces](http://madskristensen.net/post/A-whitespace-removal-HTTP-module-for-ASPNET-20.aspx) (caution with the textareas).

### LocalStorage versus cookies

I have already explained [how localStorage can replace cookies](http://blog.josemanuelperez.es/2010/07/progressively-addopting-html5-feature/) for saving users&#8217; preferences that the server doesn&#8217;t need to be aware of, reducing subsequently traffic.

### Provide static resources from cookieless domains

If you just have to use cookies, try to isolate your static resources from cookie pollution. Thus you will reduce the size of your requests.

### Split consistently static resources across different subdomains/domains

If you use different domains or subdomains to provide your static files, make sure that a client request a specific file always to the same domain, to take advantage of caching. This is important if files are simulated to be split into different subdomains rather than really being hosted in different subdomains.

### Images optimization

JPG and PNG files can be optimized using services like [Yahoo&#8217;s Smush.it](http://www.smushit.com/ysmush.it/) or [jpegoptim](http://blog.josemanuelperez.es/2009/06/jpegoptim-optimize-jpg-page-spee/). It may be not a huge save (after all, JPG files are supposed to be very optimized), but it is always good to keep your files as small as possible. Have a look at [the chapter on Optimizing Images](http://www.bookofspeed.com/chapter5.html) from the [Book of Speed](http://www.bookofspeed.com/index.html), showcasing the tools you can use to optimize them.

In addition, new image formats such as [WebP](https://developers.google.com/speed/webp/) could further reduce file size while keeping a similar quality. Take into account that not every [browser supports WebP](http://en.wikipedia.org/wiki/WebP#Support) it at the moment.

### Gzip compression

This is a must. HTML, CSS and JS files should be gzipped. You can greatly reduce their size (at least the size of the file through the wire), and it&#8217;s very easy to be set up. Additional savings can be achieved using [Zopfli](https://code.google.com/p/zopfli/), achieving [savings of around 6%](http://www.stevesouders.com/blog/2013/03/08/zopflinator/) in comparison with default mod_deflate config.

### Brotli

[Brotli](http://calendar.perfplanet.com/2015/new-years-diet-brotli-compression/) is a different decoder that seems to perform better than GZip.

### Compression using PNG, Canvas and getImageData()

Although it is a technique known since some time ago (see [this Nihilogic article of 2008](http://blog.nihilogic.dk/2008/05/compression-using-canvas-and-png.html)), during last days a series of tweets and posts have spread the word about this technique. In part, due to [a post by ajaxian](http://ajaxian.com/archives/want-to-pack-js-and-css-really-well-convert-it-to-a-png-and-unpack-it-via-canvas) and the [10k application competition](http://10k.aneventapart.com/).

If the browser supports canvas (and javascript is enabled) it is possible to compress a file (css, javascript&#8230;) as a PNG image. PNG is a lossless image that seems to compress this files even further than gzip.

### Last-modified and E-Tag

By using one of these headers, the server can respond to the browser that the version of the requested file is updated and does not need to download a new one. This is useful as a method to make sure every client uses the most updated version of your files. Just remember that a roundtrip is always necessary to perform the check, so it might be not the best approach. You could consider adding a version string as a parameter in your URL requests.

### Diffable

Google has just [released a way to patch client files](http://code.google.com/p/diffable/) (mainly large JS files, but virtually any file) by just serving the content that has been changed from client&#8217;s version of the file to the current version.

## Browser rendering

### Putting Javascript includes after CSS

Javascript will block your CSS files due to the need of downloading and executing the file, before applying the CSS rules to the document. By putting javascript after CSS the browser can start to render the document sooner.

### Non-blocking Javascript

There are different techniques to load Javascript without blocking the browser. [Steve Souders](http://www.stevesouders.com/blog/2009/04/27/loading-scripts-without-blocking/) made a great analysis of the different approaches and their support depending on the browser.

### Defer parsing of JavaScript
It consists of deferring the parsing and execution of JS code until where it is needed. An example of this is [Gmail for Mobile HTML5](http://googlecode.blogspot.se/2009/09/gmail-for-mobile-html5-series-reducing.html).

### On-demand load

It consist of downloading javascript files as they are needed according to the user interaction.

### Preload resources

It can be a good idea to download resources prior to when they are really needed if we know how they user usually behaves. For instance, in a paginated photo gallery you can stat requesting images that will be shown in the next page, to show the images to the user as fast as possible when page is changed. This has to be used carefully to not overload the browser&#8217;s memory and without damaging user experience when viewing the current page. A nice example can be seen on Facebook user&#8217;s albums, that provide json data containing the images urls, comments and tags of the photos of the album, saving requests to the server.

### Early flushing

[Steve Souders explains this technique](http://www.stevesouders.com/blog/2009/05/18/flushing-the-document-early/) to serve part of your document earlier, to allow the browser start requesting files before getting the full document.

### Specify images dimensions

By setting the dimensions of our images we can help the browser find out the layout of the different elements of the documents, avoiding repaintings and reflows. This tip is nicely explained at [Google Page Speed](http://code.google.com/intl/en/speed/page-speed/docs/rendering.html#SpecifyImageDimensions).

### CDN

[Using a Content Delivery Network](http://developer.yahoo.com/performance/rules.html#cdn) can greatly improve the download time of your resources (as already stated before). Small websites can take advantage of CDN if they make use of Javascript libraries such as jQuery, requesting the framework Javascript files using a [Google](http://code.google.com/intl/es-ES/apis/ajaxlibs/documentation/)&#8216;s or [Microsoft](http://www.asp.net/ajaxlibrary/cdn.ashx)&#8216;s CDN. You can decrease your site traffic and improve the website load (maybe the user has already cached that file). Just [consider a fallback](http://stackoverflow.com/questions/1014203/best-way-to-use-googles-hosted-jquery-but-fall-back-to-my-hosted-library-on-goo) in case there is any problem getting these files from the CDN.

### Revise your javascript performance

Nicholas Zakas is great at showing javascript performance issues in his [High Performance Javascript](http://www.amazon.com/gp/product/059680279X?ie=UTF8&amp;tag=nczonline-20&amp;linkCode=as2&amp;camp=1789&amp;creative=390957&amp;creativeASIN=059680279X) book. You can take a look to some of the tips, [compiled by Jon Raasch](http://jonraasch.com/blog/10-javascript-performance-boosting-tips-from-nicholas-zakas).

### Web workers

[Web workers](http://www.whatwg.org/specs/web-workers/current-work/) provide a way to execute javascript code in background threads. The goal is to perform processor-intensive calculations without blocking the user interface thread.

### Frame rate

In order to provide a smooth experience, especially regarding scrolling (and parallax), you should take a look at [these issues and how to solve them](http://calendar.perfplanet.com/2013/the-runtime-performance-checklist/).

## Others

### Avoid 404 errors (pages and resources)

Always check that your links point to existing resources and pages. Make use of redirections if resources have been moved to a different URL. For example, Google Chrome requests favicon at path `/favicon.ico` when you have not set any favicon. This requests throws 404 errors. To avoid them, set a favicon, and in case it is not host in that path, provide a redirection to its path.

Thanks for reading! I will try to keep this list updated (at least for personal use) as more ways to make great performance sites appear.

### Use advanced protocols

There are recent protocols like [SPDY](http://www.chromium.org/spdy/spdy-whitepaper) and [QUIC](http://en.wikipedia.org/wiki/QUIC) that claim an improvement over TCP and UDP. Note that protocols like SPDY work better not using [domain sharding](https://developers.google.com/speed/pagespeed/service/ShardResources), what used to be a technique to increase the number of parallel requests, circumventing the limit imposed by browsers.
