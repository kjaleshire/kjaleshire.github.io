---
layout: post
title: "Naïve echo server in Rust"
date: 2015-01-28
comments: false
categories: rust
---

Also with a simple thread pool, 'cause I could. <3

{% gist kjaleshire/cf6daddc80e056721519 naive_echo.rs %}

The Rust API is still going through quite a bit of churn, especially the `std::io` module which has been renamed `std::old_io` in anticipation of the `io` and `os` modules [re-design](https://github.com/rust-lang/rfcs/blob/master/text/0517-io-os-reform.md). Thus this gist will probably not compile once the next nightly is released.
