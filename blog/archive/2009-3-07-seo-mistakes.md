---
author: joseph
comments: false
date: 2009-3-07 08:02:47
layout: post
slug: five-common-seo-mistakes
title: Five Common SEO Mistakes
wordpress_id: 12
categories: [linkedIn, SEO, overview]
---

I've come across this many times when building sites for clients.  ASP.NET is really great for building dynamic content pages, but not so great when you're trying to expose those dynamic pages to a crawler or bot used by search engines.  Usually I've found myself having to index the crawler or bot to go find specific pages if I wanted something to show up explicitly.  For instance, you might have a product that you really want to showcase and have searchable.  The product's URL, however might be parameter based, something like http://www.yourstore.com/productdetails.aspx?productid=5.

<!-- more -->

Here are 5 common mistakes you should avoid when building sites that need to be Search Engine Optimized (SEO).


## **1. Overuse of Button Controls**


The Button and LinkButton controls are handy for running server-side logic when a link or push button is clicked, but keep in mind that **search engines can't follow these links**. These controls cause a postback via Javascript code that search engines are unable to execute.  I've seen more than one developer who's standard method of linking from one page to another was to drag a LinkButton control onto the page and then place a Response.Redirect in the event handler, making the entire site completely uncrawlable by search engines.


It seems obvious, but when linking between pages try to use a plain text link or Hyperlink control whenever possible.




## **2. Duplicate Page Titles**


With any dynamically generated site, it can be difficult to generate unique page titles for each and every page, but it really is important.  If you have a quality site, then the search engines are working hard to drive traffic to your site.  After all, that is their core business - to provide links to the best resources on whatever the searcher is looking for. 

So you need to make it easy for the search engines to figure out exactly what your pages are about, and the page title is an important part of that.  Not only that, but once the search engine _does_ rank your page highly, the title is the primary text that searchers will be seeing and using to determine whether to click on your listing or not!  


On dynamically generated pages, try to to use a keyword-rich page title, such as the full name of the product on a product page, for best results.  If you don't have any appropriate field, provide the ability for the user to specify their own page titles for each item being displayed.  It's worth their time and effort.




## **3. Duplicate Meta Descriptions**


Much like the duplicate page title issue, the meta description tag should not be duplicated across your pages either.  Like the page title, this text is used (although to a lesser extent) by the search engines to determine the content of your page and also appears underneath your title in the search engine listing.  Depending on the number of pages of dynamic content on your site, it might not be practical to add multi-sentence descriptions for every single page.  In this case, simply remove the meta description tag altogether.  The major search engines are pretty good at improvising when the description tag is missing by displaying portions of the page body that match the user's search keywords instead.


In my experience, the SEO benefit of adding a keyword-rich meta description is not enough to warrant spending a great deal of time creating custom descriptions for sites with 100+ pages.




## **4. State-Dependent Pages**


Search engines rely heavily on the idea that every unique page has it's own unique URL.  That means that if you are basing a page's content on session variables or viewstate parameters, you are probably going to have problems getting that content indexed.  Once a search engine finds a URL, Google will continue spidering that page, but you can bet that the search engine robot will not navigate through your site again to get there.  So you need to make sure that any content you want indexed by search engines can be accessed by simply opening your browser and typing in the URL of that content.  That means unique URLs for every product in your ecommerce store, ever category in your directory, etc. 


My recommendation is to use viewstate rarely and session variables almost never.




## **5. Duplicate Content When Rewriting URLs With ASP.NET**


When you rewrite a URL, the browser is displaying a keyword-rich URL, but internally the URL of the page being displayed is still the ugly URL with the querystring parameters.  In technical terms, the Request.RawURL value might be something like:



	
  1. http://www.store.com/products/coffee-cup.aspx




but the Request.Url value would still be something like:






	
  1. http://www.store.com/products.aspx?productID=15  


All of that is just fine, but a problem can arise if you have a Button or LinkButton control that posts back on that page.  **_By default, the button control will post back to the Request.URL value_**. causing the URL to change after postback.  This can be a problem if some users end up linking to your 'ugly' URLs, because the search engines will find that link and spider it.  To the search engine the two different URLs signify two different pages and both will be indexed seperately, causing a pretty ugly duplicate content problem.  


Thankfully, starting with .NET 2.0, there is a [PostBackUrl](http://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.button.postbackurl%28VS.80%29.aspx) property on the button controls.  Set this property to the Request.RawUrl value and your button will postback to the 'pretty' URL.


 The original article that I read this from was on this [blog](http://www.dexign.net/post/2008/07/08/Five-ASPNET-SEO-Mistakes.aspx)
