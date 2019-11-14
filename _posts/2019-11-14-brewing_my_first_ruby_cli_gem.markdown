---
layout: post
title:      "Brewing My First Ruby CLI Gem"
date:       2019-11-14 14:48:15 +0000
permalink:  brewing_my_first_ruby_cli_gem
---

A short guide to how I built and published a Ruby CLI Gem using Bundler and RubyGems.org

Like the beginning of any project, staring at a blank slate was by far the scariest part. First, I had to decide what I wanted my gem to be able to do, so I asked myself what I would find fun or helpful, or both. Beer is fun. Knowing which beers I absolutely must try is helpful. How about a gem that can access Beeradvocate’s top rated new beers? Yes, please!

Once I had my concept, I did a quick Google search for ways to create a Ruby gem. One of the top hits was Bundler, which generates all the files and dependencies I needed to get started (Bundler’s detailed read-along guide was easy to follow and really simplified what would otherwise have been a daunting process). After checking to make sure my version of bundler was up to date by running `bundle -v`, I was able to create my scaffold directory by running `bundle gem tap_rated_new_beers` (for recommendations on naming your gem, visit this guide at RubyGems.org). This step alone boosted my confidence because I now had the basic structure of my gem. Next step, set up my files!

I wanted make sure I followed the Single Responsibility Principle (SRP), so I created four files in which to write my code:

lib/tap_rated_new_beers.rb (This serves as my environment.)
lib/tap_rated_new_beers/scraper.rb (This file contains all the code that scrapes information from Beeradvocate’s Top Rated Beer: New page.)
lib/tap_rated_new_beers/cli.rb (This file contains all the code that makes my CLI work.)
lib/tap_rated_new_beers/beer.rb (this file contains all the code that instantiates each beer and it’s attributes. It also contains my open_in_browser method, which enables the user to directly access a beer’s brewery from their terminal.)

Before going much further, I wanted to make sure that my environment was set up correctly, so I added some “fake” code to my lib/tap_rated_new_beers files and ran my executable file in my terminal. My executable file, bin/tap_rated_new_beers, contains the following:
```
#/usr/bin/env ruby
require 'pry'
require 'nokogiri'
require 'open-uri'
require 'colorize'

require_relative 'tap_rated_new_beers/version.rb'
require_relative 'tap_rated_new_beers/cli.rb'
require_relative 'tap_rated_new_beers/scraper.rb'
require_relative 'tap_rated_new_beers/beer.rb'
```

Success! My files and classes were able to communicate with each other. Now I could start brewing my TapRatedNewBeers::Scraper class.

Building my scraper methods ended up being more of a challenge than I was expecting. I knew that I wanted to provide my gem user with two levels of information:

A list of the top rated new beers (by rank).
Each beer’s score, number of ratings, style, ABV, availability, description (if provided), and the brewery’s name, location and website url.

This meant that I needed to create two scraper methods — one that would get the rank and name of each beer, and one that would get each beer’s unique information. I was able to scrape my first level of information by isolating the table that contained the rows I wanted to access, then I iterated through each row to get the rank and name of each beer.

```
  def self.scrape_index_page
    table_row_nodes = self.index_url.css("table").css("tr")
    table_row_nodes = table_row_nodes.slice(2, 50)

    table_row_nodes.each do |beer_row|
      rank = beer_row.css("td")[0].text
      name = beer_row.css("td")[1].css("a").first.text
      beer_url = beer_row.css("td")[1].css("a").first.attributes["href"].value

      temp_beer = TapRatedNewBeers::Beer.new(name)
      temp_beer.rank = rank
      temp_beer.beer_url = "https://www.beeradvocate.com"+ beer_url
    end
  end
```
	
My second scraper method needed to utilize the the beer_url for each beer to access the individual beer’s attributes. Most attributes were easy to access, but I had to apply some enumerable methods to isolate the ABV, availability and notes/description.

```
def self.scrape_beer_page(beer)
    beer_page = Nokogiri::HTML(open(beer.beer_url))

      beer.score = beer_page.css("div#score_box").css("span.BAscore_big").css("span.ba-ravg").text
      beer.ratings = beer_page.css("div#score_box").css("span.ba-ratings").text
      beer.style = beer_page.css("div#info_box.break").css("a")[4].text
      beer.brewery = beer_page.css("div#info_box.break").css("a")[0].text
      beer.location = beer_page.css("div#info_box.break").css("a")[1].text + ", " + beer_page.css("div#info_box.break").css("a")[2].text
      beer.brewery_url = beer_page.css("div#info_box.break").css("a")[3].attributes["href"].value
      array = beer_page.css("div#info_box.break").text.split("\n\n")
      array.find do |phrase|
        if phrase.include?("%")
          beer.abv = phrase
        elsif phrase.include?("Availability")
          beer.availability = phrase
        end
      end
      beer.notes = array.last
  end
end
```

After scraping all the information I needed, I started building out my TapRatedNewBeers::Beer class. I created an attr_accessor for each attribute and a class variable @@all set equal to an array in which to store each beer instance that could then be shared between classes. I also added the open_in_browser method that could be called in my TapRatedNewBeers::CLI class and enable the user to access each brewery’s website (I love this feature!).

```
class TapRatedNewBeers::Beer
attr_accessor :name, :rank, :beer_url, :brewery, :style, :abv, :ratings, :score, :location, :brewery_url, :availability, :notes

  @@all = []

  def initialize(name = nil)
    @name = name
    @@all << self
  end

  def self.all
    @@all
  end

  def open_in_browser
    system("open '#{brewery_url}'")
  end
end
```

Last but not least, I need to build the working file of my gem: TapRatedNewBeers::CLI. This final step took the longest BY FAR. I think the most challenging method to get working was select_beer. I needed to be able to account for invalid user input, and that meant converting the rank associated with each beer from a string to an integer so I could then define valid input as being 1 to 50 (or the length of the array containing all the beers, in case I decide to change the number of beers provided in the future). Thankfully, I got some help figuring this out from my amazing cohort at the Flatiron School.

```
def select_beer
    puts "Select the rank number of the beer you'd like to sample:".red.bold
      input = gets.strip
        if input.to_i.between?(1, TapRatedNewBeers::Beer.all.length)

          beer = TapRatedNewBeers::Beer.all.find do |beer|
            beer.rank == input
          end

          TapRatedNewBeers::Scraper.scrape_beer_page(beer)
          print_beer_info(beer)

        else
        puts ""
        puts "Have you been drinking? Please try again.".red.bold
        puts ""
        display_list
        end
  end
```
	
After many, many trials and errors, I finally had a user-friendly, error-free gem! All I had left to do was publish it and crack open a beer (selected off my tap_rated_new_beer list, of course). I created a RubyGems account and followed the steps in the Publishing Your Gem guide…

`tap_rated_new_beers 🔥 gem push pkg/tap_rated_new_beers-0.1.0.gem`

Pushing gem to https://rubygems.org...
Successfully registered gem: tap_rated_new_beers (0.1.0)
…and before I knew it I had 30 downloads! That alone makes the past week’s efforts well worth it.

You can sample my Ruby CLI Gem by typing the following prompts in your terminal:
gem install tap_rated_new_beers
bin/tap_rated_new_beers

Happy coding/drinking!
