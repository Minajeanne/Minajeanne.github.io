---
layout: post
title:      "Creating a CRUD, MVC App Using Sinatra: My Three Biggest Takeaways"
date:       2019-11-14 14:55:48 +0000
permalink:  creating_a_crud_mvc_app_using_sinatra_my_three_biggest_takeaways
---

To say this was a grueling undertaking would be an understatement. At times my RESTful routes had me feeling like I was in a maze with no maps, despite four controllers full of them. I understood the concept and the logic, but why weren’t my routes taking me where I wanted them to?

A few days into building my app, it became glaringly, painfully clear that I should have created a diagram of my app behaving how I wanted it to and then built it from there.

Takeaway #1: Literally map out your CRUD (Create, Read, Update, Delete) functionalities, view pages, and app routes performing the HTTP requests you want. Some great resources for doing this, if you’re not inclined to the old fashioned pen and paper method, are Gliffy and Lucidchart. Having the visual representation of your routes and what they are connecting makes a huge difference when writing your actual route actions. It is easy to get lost and forget which view page is supposed to show what, or which route is supposed to redirect to where.

The time I spent lost, navigating through my routes made debugging unavoidable. It was honestly not a task I was very comfortable with outside of the occasional Pry session. But oh, did that change.

Takeaway #2: Make Pry and Tux your best friends. Pry has some pretty powerful capabilities. It can be invoked within a method (or any binding) and give you access to the object’s or binding’s scope. And this can all be done while running your program, so you can make real-time fixes and keep moving. Tux is another powerful Ruby gem. It allows you to perform all of your CRUD actions on your database via the command line, and it also loads a full environment in your console so you can review your ActiveRecord associations and inspect Ruby objects. This access to my database came in handy on several occasions, but especially when I wanted to remove all the new users I had created without actually deleting my database.

A bonus feature I wanted to add to my app was the ability to display validation confirmation and validation error messages. Of course, there is a Ruby gem for that: Sinatra-Flash. But it was almost too easy to be true.

Takeaway #3: Sinatra-Flash, when working, can do some cool things. It is a Sinatra extension, so it will only play nice with Sinatra, but it allows you to set, customize, and show Rails-like flash messages (ex. f`lash[:message] = “I’m pretty cool when you can get me to work!”`). In implementing flash messages, I learned two lessons the hard way: it does not like when ‘:session_secret’ is sourced from an ENV variable, and it does not like being placed before a CRUD action. I’m still working on getting to the bottom of the first issue, which is resolved when ‘:session_secret’ is set to ‘session_secret’, but the second issue seems obvious (when you’ve slept more than a few hours). Flash won’t know that your action (ex. @object.destroy) has taken place if you call it before the action. Many errors will ensue.

Someone once said, to really learn, fail — then fail again! If that’s true, I’ve learned a lot from creating this app.
You can view and download my app here. Thanks for reading!
