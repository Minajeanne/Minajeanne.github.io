---
layout: post
title:      "A React-Redux Trivia App"
date:       2019-11-14 14:59:17 +0000
permalink:  a_react-redux_trivia_app
---

Goal: Use React-Redux to create a single-page trivia application.

Building my trivia app was a long but rewarding process. I was tasked with creating two apps, essentially: a Rails API backend to handle data persistence and a React-Redux frontend. To me, it made the most sense to begin with the backend as that’s where my users’ data would be stored, and, though it wasn’t a project requirement, I knew that other features of my app would require ‘log in’/’sign up’ functionality.

To create a Rails API skeleton I ran:
`rails new [api_name]  — api`

I then versioned my controllers to I could access my API via /api/v1.
`app/controllers --> app/controllers/api/v1`

Before generating my resources I had to decide what my app would require. I knew I needed to generate a resource for users so I started with that. I also knew that a user would have stats (e.g. a high score), so that was my next resource. 

Once I’d generated the resources I thought I would need, I edited my tables to add the necessary attributes and ran my migrations. 

I then created a user in my seeds.rb file:
`User.create(username: “QuizWhiz”, password: “password”)`

Once I had a working backend, I shifted focus to my React-Redux frontend. I used `npx create-react-app my-app` to set up my development environment, then `npm start` to begin running my app.
