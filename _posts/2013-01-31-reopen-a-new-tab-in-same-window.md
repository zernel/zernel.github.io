---
layout: post
title: "Reopen a new tab in same window"
category: Technology
tags: Javascript
---

You could simply set the target attribute as blank in the hyperlink
redirecting to the PDF and it will open the file in a new tab. Something
similar to this :

    <a href="http://mysite.com/files/test.pdf" target="_blank"></a>

If you want to use JQuery for this, you can try something like this :

    $(document).ready(function() {
      $("a").click(function() {
       link_host = this.href.split("/")[2];
       document_host = document.location.href.split("/")[2];

       if (link_host != document_host) {
         window.open(this.href);
         return false;
        }
      });
    });
