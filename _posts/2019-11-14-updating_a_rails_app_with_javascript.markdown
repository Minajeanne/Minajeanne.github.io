---
layout: post
title:      "Updating a Rails App with JavaScript"
date:       2019-11-14 14:57:00 +0000
permalink:  updating_a_rails_app_with_javascript
---

User Experience will make or break a website (and the company behind it). Page taking too long to load? On to the next. Site too difficult to navigate? There goes your revenue.

I am just wading into JavaScript, but I can already see its immense power and possibilities. For example, take something as basic as a form. In a Rails-only application, you have to redirect to or render a view page every time your user accesses a form. Meh. In a simple app, this is not such a big deal. But in a more complex app this can lead to longer load times and a lot more clicking. In other words, very inefficient.

With JavaScript, you can get real-time information without the page refresh because the requests are being handled within the user’s browser, not the server. So instead of the user clicking a link to a form and being redirected to an entirely new page, the user can click a link and access the form all on the same page, with zero time wasted refreshing. Simple features like this not only make your app more efficient, they boost its perceived credibility. And I’m guessing it’s probably harder to sell your product or service if your user doesn’t think you’re trustworthy.

With these benefits in mind, I’ve begun the process of adding JavaScript to my Rails app, starting with all of my forms. The app itself already looks like a better quality app, even though the functionality is essentially the same. However, it’s becoming clear from a UX standpoint that it will be a better product overall (and much better UX) if I make it a single-page app, so that is my next step. This was not even an option without JavaScript, so that in and of itself is powerful.
