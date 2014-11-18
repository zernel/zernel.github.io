---
layout: post
title: "Make a redirect page in javascript"
category: Technology
tags: Javascript
---

How to redirect the user from one page to another? Try these:

    // similar behavior as an HTTP redirect
    window.location.replace("http://stackoverflow.com");

    // similar behavior as clicking on a link
    window.location.href = "http://stackoverflow.com";

It is better than using window.location.href =, because replace()
does not put the originating page in the session history, meaning the
user won't get stuck in a never-ending back-button fiasco. If you want
to simulate someone clicking on a link, use location.href. If you want
to simulate an HTTP redirect, use location.replace.

Also, you could redirect back to prev page by using `window.history.back(-1);`.
