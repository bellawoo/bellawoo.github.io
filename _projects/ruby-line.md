---
layout: project
title:  The Ruby Line/Navigate DC
date:   2015-08-19
categories: projects
---
The Ruby Line is an API that provide information about the upcoming WMATA metrorail arrivals, nearby Bikeshare dock information, WMATA metrorail and Uber fare estimates and walking directions.

The Ruby Line has evolved over three stages. A small feature set was originally assigned as a partnership with the Front-End Engineering course at The Iron Yard DC. In this first release, the API was built in Sinatra.

The second iteration came as a second partnership with the iOS Development course at The Iron Yard DC. For this assignment, the project was to be refactored for iOS front-end consumption and imported into Rails.

This project was refactored once again and added a fare calculator feature. In total, this API mashes up five different public APIs. There was also a switch from conventional Rails framework to using the rails-api gem (which will be folded into Rails 5 core) to cut down on the unecessary middleware. The API uses Rollbar for error monitoring and source code for all three versions are up on GitHub.

* [v3 - rails-api-gem API](https://github.com/bellawoo/Ruby-Line)
* [v2 - iOS/Rails API](https://github.com/bellawoo/Green-Apple-Line)
* [v1 - Sinatra API](https://github.com/bellawoo/Ruby-Line-Sinatra)

The API is paired with a sleek and user-friendly front-end to create the Navigate DC app. Navigate DC is currently [deployed on Heroku](http://navigatedc.herokuapp.com/).
