---
author: Joseph
comments: false
date: 2010-4-14 09:05:58
layout: post
title: Creating an PagedList<T> that uses AJAX
categories: [ajax, asp.net mvc, linkedIn, paging]
---

I’ve been using this PagedList functionality that i found from a [blog article Rob Conery](http://blog.wekeroad.com/2007/12/10/aspnet-mvc-pagedlistt/) put up, and [a control I found by Robert Muehsig](http://code-inside.de/blog-in/2008/04/08/aspnet-mvc-pagination-view-user-control/) which I’ve really enjoyed using so far.

One of the things that was missing from the functional set that I ended up needing was the ability to page the list, but through issuing AJAX requests instead of the typical post back.

So I went off and extended the existing model to support AJAX requests, and thought I would share it in case anyone else needed to do the same thing.<!-- more -->

I guess the best place to start would be the use case.  So to start I created a control that encapsulates the Paging UI layout and calls I need.  The use of the original control looks like this:

``` c#

Html.RenderPartial("AjaxPagination",
    new AjaxPaginationViewData
        {
            PageIndex = Model.PageIndex,
            Action = "CondoPage",
            Controller = "Home",
            AjaxOptions =
                new AjaxOptions { 
                    UpdateTargetId = "updatedContent" },
            TotalCount = Model.TotalCount,
            PageSize = Model.PageSize,
            NumberOfPagesToEachSide = 2
        }
);

```

The new AJAX functionality is called similarly:

``` c#

<% using (Ajax.BeginForm("SomePage",
        "SomeController",
        new AjaxOptions { UpdateTargetId = "updatedContent" })) { %>

        <% Html.RenderPartial("AjaxPagination",
            new AjaxPaginationViewData {
                    PageIndex = Model.PageIndex,
                    Action = "SomeAction",
                    Controller = "SomeController",
                    AjaxOptions = new AjaxOptions
                        { UpdateTargetId = "updatedContent" },
                    TotalCount = Model.TotalCount,
                    PageSize = Model.PageSize,
                    NumberOfPagesToEachSide = 2
        });%>

<% } %>

```

A couple things to note. You’ll notice that the AJAX control is rendered inside a Ajax.BeginForm. This is because I’m using the Microsoft.Ajax way of making AJAX calls.  This could also be done using jQuery or something else that can process AJAX calls. I just went this way because the scripts are already included in asp.net mvc app when you first create the project.  The result of the AJAX call will be a partial view, and we’ll need to put that somewhere.  That’s where the UpdatedTargetId comes into play. Other things we include in the AJAX control that are not in the original are the Action and the Controller, and some AjaxOptions. PageActionLink doesn’t work with the AJAX control, because we’ll be using Ajax.ActionLink to build the link, which is why I broke it up into Action, and Controller. For the AjaxOptions, we need those to specify the target of the call.

So now that’s been explained, let’s look at the controls themselves.  Here’s a comparison of the original control and the ajax control.

The original is one this way:

``` c#

<% if (Model.HasPreviousPage) { %>
    <a href="<%=Model.PageActionLink.Replace("%7Bpage%7D", (Model.PageIndex - 1).ToString())%>">Previous</a>
<% } %>

<% if (Model.GetFirstPageToLink() != 1) { %>...<% } %>

<%for (var page = Model.GetFirstPageToLink(); page <= Model.GetLastPageToLink(); page++) {
    if (page == Model.PageIndex) { %>
        <%=page.ToString()%>
<% } else { %>
    <a href="<%=Model.PageActionLink.Replace("%7Bpage%7D", page.ToString())%>"><%=page.ToString()%></a>
<% }

    if (page != Model.GetLastPageToLink()) { %>|<% } } %>

<% if (Model.GetLastPageToLink() != Model.PageCount) { %>...<% } %>

<% if (Model.HasNextPage) { %>
    <a href="<%=Model.PageActionLink.Replace("%7Bpage%7D", (Model.PageIndex + 1).ToString())%>">Next</a>
<% } %>

```

And the AJAX control is done this way:

``` c#

<% if (Model.HasPreviousPage) { %>

<%= Ajax.ActionLink("Previous", Model.Action, Model.Controller, new { page = (Model.PageIndex - 1).ToString() }, Model.AjaxOptions)%>

<% } %>

<% if (Model.GetFirstPageToLink() != 1) { %>...<% } %>

<%for (var page = Model.GetFirstPageToLink(); page <= Model.GetLastPageToLink(); page++) {
    if (page == Model.PageIndex) { %>
        <%=page.ToString()%>
    <% } else { %>

<%= Ajax.ActionLink(page.ToString(), Model.Action, Model.Controller, new { page = page.ToString() }, Model.AjaxOptions)%>

<% } if (page != Model.GetLastPageToLink()) { %> | <% } } %>

<% if (Model.GetLastPageToLink() != Model.PageCount) { %>...<% } %>

<% if (Model.HasNextPage) { %>

<%= Ajax.ActionLink("Next", Model.Action, Model.Controller, new { page = (Model.PageIndex + 1).ToString() }, Model.AjaxOptions)%>

<% } %>

```

The big difference here is the way that the links are generated. The original control simply creates an anchor tag and passes in the url generated by the Model. The AJAX control uses AJAX.ActionLink() instead, so we can have the link support AJAX.

So knowing how the control looks, this is the Model for the AJAX control itself:

``` c#

public class AjaxPaginationViewData
{
    public int NumberOfPagesToEachSide { get; set; }
    public int PageIndex { get; set; }
    public int PageSize { get; set; }
    public int TotalCount { get; set; }

    public string Action { get; set; }
    public string Controller { get; set; }

    public AjaxOptions AjaxOptions { get; set; }

    public int PageCount
    {
        get
        {
            return (int)Math.Ceiling((double)TotalCount / PageSize);
        }
    }
    public bool HasPreviousPage
    {
        get
        {
            return (PageIndex > 1);
        }
    }

    public bool HasNextPage
    {
        get
        {
            return (PageIndex * PageSize) <= TotalCount;
        }
    }

    public int GetFirstPageToLink()
    {
        return (PageIndex - NumberOfPagesToEachSide > 1 ? PageIndex - NumberOfPagesToEachSide : 1);
    }

    public int GetLastPageToLink()
    {
        return (PageIndex + NumberOfPagesToEachSide < PageCount ? PageIndex + NumberOfPagesToEachSide : PageCount);
    }
}

```

That pretty much explains how the control is built.

The only thing left is how the interaction with PagedList happens.  For that we look at the action that the control calls.  In this example, we’re calling SomeAction in SomeController, and it would look something like this:

``` c#

public ActionResult SomeAction(int page)
{
    CachedPage = page;
    var query = GetSearchQuery(CachedSearchParameters);
    var model = query.ToPagedList(page, DefaultPageSize);
    return PartialView("AjaxResults", model);
}

```

The ToPagedList performs the functionality that is included with the PagedList classes which you can find [here](http://pagedlist.codeplex.com/).

Let me know what you think, and if you’d like some demo source to see this in action I can happily provide, just let me know.
