---
layout: post
title: "Parallelizing a Ray-tracer"
date: 2017-09-09
comments: false
categories: c++ concurrency graphics
---

I worked through Peter Shirley's [Ray Tracing in One Weekend][0] when I needed something to do during [Hurricane Harvey][1]. It was a fun project and expanded my understanding of how ray-tracing works. The book is not large, maybe 20 pages, and walks through building a simple ray-tracer in C++. Eventually it renders the image seen on the cover complete with different material behavior like mirroring & glass refraction. Definitely recommend.


A factor in not using ray-tracing to produce real-time graphics, a la video games, is that it takes several seconds to render even a thumbnail-size image. Fortunately, it's possible to parallelize the tracing algorithm since each pixel can be rendered independently.

After finishing the ray-tracer itself, I attempted to speed it up with threads. Each thread would render a portion of the image, and the main thread then would then build the image. Starting with N threads = 2, I ran the rendering program. It did seem a little faster, but not the nearly x2 speedup I was expecting. I changed N to 4, and the tracer ran notabley slower, and with each N increase the tracer ran significantly slower. Once N reached double-digits, the tracer slowed to a crawl, taking over ten minutes for a single thumbnail image. Something was clearly wrong.

There weren't any rendering resources being shared since each pixel was being rendered independenly. My initial guess was the threads were sharing some kernel object that was lock-protected. But looking through the code I couldn't spot anything backed by a kernel facility. There hadn't been any locks introduced since I hadn't needed them. I considered if the allocator was the culprit. But modern memory allocators like jemalloc are generally receptive to multi-threaded access.

I was nearly ready to give up and admin there was some quirk about threading on Windows I hadn't discovered. A desperate Bing search, however, pulled up a [Stack Overflow answer][2] that happened to mention random number generators. Sure enough, I was using a C++ standard library RNG, and it had introduced a global variable.

The question now was ensuring each thread had a copy of the RNG. Each thread could create one at instantiation. But then the RNG variable would have to be passed down to each function that needed it, and this seemed quite messy. I thought about creating a global array of RNGs and having each thread populate it. That would work, but I wanted to try another solution first, [thread-local storage][3].

I had heard of TLS before in passing, and that C++ had good support for it as of C++11. It was as simple as putting a keyword before the RNG variable: `thread_local`. The tracer compiled with 8 threads, and when it ran, all cores were pegged beautifully.

Lesson learned: double-check & triple-check that there are no shared resources between threads. Even the most innocuous standard library type could be harboring a global variable, or worse, an atomic lock.

You can see the finished code with a sample image [here][4].

[0]: https://www.amazon.com/Ray-Tracing-Weekend-Minibooks-Book-ebook/dp/B01B5AODD8
[1]: http://www.npr.org/2017/08/28/546668862/texas-capital-austin-is-spared-the-brunt-of-harvey-but-it-still-feels-effects
[2]: https://stackoverflow.com/questions/612860/what-can-make-a-program-run-slower-when-using-more-threads
[3]: https://en.wikipedia.org/wiki/Thread-local_storage#C_and_C.2B.2B
[4]: https://github.com/kjaleshire/ray-tracer
