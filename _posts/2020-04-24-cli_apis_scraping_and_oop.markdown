---
layout: post
title:      "CLI: APIs, Scraping, and OOP"
date:       2020-04-24 16:35:26 +0000
permalink:  cli_apis_scraping_and_oop
---


Starting this project, I was determined to do more than the requirements in order to push myself. I knew that I need to pick something that I would find interesting. I decided I wanted to make a CLI that would provide the user with more information about videogames. My first step was finding a valid API to use. I wanted to find a pretty well made database of games that was cross-platform (PS4, XBOX, PC, Android, etc.) and that would allow me to go gather multiple layers of information about each game. I found that https://rawg.io/apidocs had all the information I would like to gather.

RAWG was great, but then I was stuck. How would I use this database to find the information I needed? ENDPOINTS! The glorious, sometimes tedious and confusing, information I needed was located at the API's endpoints. I opened the endpoints and saw pages of information. This was good! I took a day to really focus on which endpoints would be most valid for the user and the experience I wanted to curate. Since this CLI was meant to gather more information about the games themselves, I focused in on that. The endpoint for this data was located here: https://api.rawg.io/api/games.

Using bin/console, to play with the data types and determine what I was working with I found that all the games were located in an array of hashes. I was able to determine my first level of data could be accessed by the string "id" and then I would be able to display the string "name". I decided to use a gem called HTTParty to make requests to the network and got started in my API class. I knew I needed to also create a class for the objects themselves, which I called Videogame. I knew I would need to iterate through the array of hashes at the string "id" and "name". Using #each, I was able to interate through that data and pull the information I wanted! First steps, check! 

Now for the second level, I knew the information was located  at https://api.rawg.io/api/games/#{"id"}  and I knew I wanted to give the user more information about the game. I decided to provide rating data, genres, a synposis, and a link to the games website. I thought this information would be helpful for someone using the CLI to find out what games they might want to play. Then I needed to weed out the information I didn't want to display. Thank goodness for attr_accessor: because I had a lot of them. One for each piece of data I wanted to display. After trial and error (many, many errors), I was able to display the second layer of data. This is when I noticed that my synposis had HTML syntax strung throughout it. Great, now what? 

I attended office hours after trying to #chomp and #strip the syntax away, even sometimes knowing it wouldn't help. My cohort lead said, "you are going to have to scrape that data." So I installed Nokogiri and scraped the description of the game using #text. It worked! My second level of data displayed cleanly and I was happy with it. 

Throughout the entire project, I second guessed myself. I forgot things I though I knew. I made silly errors that cost me days of trouble shooting. Overall, I think I got my first taste of what being a software engineer is really about. I felt like I got "in the zone" a few days and lost track of time while working on my program. I drank way too many cups of coffee. I trouble shooted over an underscore versus a period for two days, but I did it. My program works. It does what I wanted it to do. It does more than I thought it could do. I am proud to have done it and looking forward to the next one! 


