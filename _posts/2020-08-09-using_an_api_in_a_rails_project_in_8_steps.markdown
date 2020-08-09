---
layout: post
title:      "Using an API in a Rails Project in 8 Steps"
date:       2020-08-09 15:50:54 -0400
permalink:  using_an_api_in_a_rails_project_in_8_steps
---


When starting my Rails project, I knew I wanted to pull entertainment content from an already sourced source, like IMDB so that my users would not have to look up the information each time they wanted to add something to the database. The good news: there are many APIs that handle just this. The bad news: How do I do it? 

**Step One**

Pick an API. 

I picked OMDB - [The Open Movie Database](http://www.omdbapi.com/). My decision was largely based on the docs for this API. They were clear and easy to manuver. You can even test your endpoints on site. This is an example response from the API: 

```
{"Title":"Starship Troopers","Year":"1997","Rated":"R","Released":"07 Nov 1997","Runtime":"129 min","Genre":"Action, Adventure, Sci-Fi, Thriller","Director":"Paul Verhoeven","Writer":"Edward Neumeier (screenplay), Robert A. Heinlein (book)","Actors":"Casper Van Dien, Dina Meyer, Denise Richards, Jake Busey","Plot":"Humans in a fascist, militaristic future wage war with giant alien bugs.","Language":"English","Country":"USA","Awards":"Nominated for 1 Oscar. Another 3 wins & 15 nominations.","Poster":"https://m.media -amazon.com/images/M/MV5BNWExNzg3MmMtYjc3MS00MzFlLWJiOWQtNWYxZTgxNjhlZTQ2XkEyXkFqcGdeQXVyNzkwMjQ5NzM@._V1_SX300.jpg","Ratings":[{"Source":"Internet Movie Database","Value":"7.2/10"},{"Source":"Rotten Tomatoes","Value":"64%"},{"Source":"Metacritic","Value":"51/100"}],"Metascore":"51","imdbRating":"7.2","imdbVotes":"263,248","imdbID":"tt0120201","Type":"movie","DVD":"19 May 1998","BoxOffice":"N/A","Production":"Sony Pictures Home Entertainment","Website":"N/A","Response":"True"}
```

**Step Two**

Get an API Key. 

With OMDB there is a tab in the Navbar. Once you click it you can provide them with your email and get you key via email. Save this for later, it's important! 

**Step Three**

Set up your secret file with your API key and your .gitignore. 

Create a .gitignore file by running `touch .gitignore`. Name is important here. It is case sensitive. Git is looking for that exact file and once it finds it it will not stage or commit from that file. 

Using .env for our secret file means we have to add a [gem](https://www.npmjs.com/package/dotenv).

In the terminal, `bundle add dotenv` and then `bundle install`. Once completed, you should be able to `touch .env` in your terminal, which will create our super secret folder of wonder. 

**It is important to note that you must add your .env to your .gitignore to ensure that your secrets aren't pushed to github!**

Now that we have our .env file ready and securely hidden in our .gitignore, we can add the API key and get rolling. 

Add your API Key to your .env like so: 
```
PROJECT_API_KEY=3ebf52c0-3ebf52c0
```

*That is an emaple key, it is useless and will not work with anything.*

**Step Four**

The call. 

We have access to this lovely database, full of all of the content we are binging throughout this global pandemic. I want access to all that hard work those folks at IMDB and OMDB have worked so hard to create, manage, catalog, etc.. I am lazy. This is where we call to our API but how? To the DOCS! 

They have a very large heading called Usage on OMDB with an example url for all data requests, which we will be making. 
```
http://www.omdbapi.com/?apikey=[yourkey]&
```

Following that we see a header called Parameters, in my project I wanted to search by title. 

In the parameters table I see a key of t that that searches by title. 

```
http://www.omdbapi.com/?t=Starship+Troopers
```

That will return the lovely example data in step one, with all the parameters I could dream of adding to my project. 

So we know we need both the API key and this mystical t in order to search for content by title. 

```
http://www.omdbapi.com/?t=Starship+Troopers&apikey=YOURKEYHERE
```

*If your API's docs are difficult to understand or you want to play around with the parameters for using your API I highly recommend using [Postman](https://www.postman.com/) to play with the data.*

We now know what we need to call our API and get information about a searched title back. Now lets add that to our project and make a call to our API. 

I created a model called OmdbService.rb to handle my API call. I wanted to keep seperation of concerns and have a dedicated space for my API business. 

```
BASE_URL = "http://www.omdbapi.com/?t="  
API_PARTIAL_URL = "&apikey=#{ENV['OMDB_API_KEY']}" 

class OmdbService 

    attr_reader :results

    def self.search(search)
        @results = HTTParty.get(BASE_URL + "#{search}" + API_PARTIAL_URL)
        @results
    end 
    
```

Let's break this down step by step. 

First I have set the base_url to the first portion of our url from OMDB, `"http://www.omdbapi.com/?t=" ` and then I have a partial_url set with my API key stored in an [environmental variable](https://www.rubyguides.com/2019/01/ruby-environment-variables/) `"&apikey=#{ENV['OMDB_API_KEY']}"`. 

Then in my search method in the model I am building up the URL to include an interpolated search value into the URL so that a user is able to type in our argument and recieve the information they are requesting back. I used the [HTTParty gem](https://github.com/jnunemaker/httparty) within a class, which gives your class a "get" method that can retrieve data over HTTP. Make sure to bundle add and bundle install HTTParty. 

I am setting the API 'get' call to an instance variable (@results) and returning the @results. 

This is an example of our results if the user searched for Starship Troopers. 

```
"Title":"Starship Troopers","Year":"1997","Rated":"R","Released":"07 Nov 1997","Runtime":"129 min","Genre":"Action, Adventure, Sci-Fi, Thriller","Director":"Paul Verhoeven","Writer":"Edward Neumeier (screenplay), Robert A. Heinlein (book)","Actors":"Casper Van Dien, Dina Meyer, Denise Richards, Jake Busey","Plot":"Humans in a fascist, militaristic future wage war with giant alien bugs.","Language":"English","Country":"USA","Awards":"Nominated for 1 Oscar. Another 3 wins & 15 nominations.","Poster":"https://m.media -amazon.com/images/M/MV5BNWExNzg3MmMtYjc3MS00MzFlLWJiOWQtNWYxZTgxNjhlZTQ2XkEyXkFqcGdeQXVyNzkwMjQ5NzM@._V1_SX300.jpg","Ratings":[{"Source":"Internet Movie Database","Value":"7.2/10"},{"Source":"Rotten Tomatoes","Value":"64%"},{"Source":"Metacritic","Value":"51/100"}],"Metascore":"51","imdbRating":"7.2","imdbVotes":"263,248","imdbID":"tt0120201","Type":"movie","DVD":"19 May 1998","BoxOffice":"N/A","Production":"Sony Pictures Home Entertainment","Website":"N/A","Response":"True"}
```

It is important to understand the type of data we are handling, in this case we have a hash containg key value pairs that allow us to access all of the data we need to create content in our database. 

The keys we have access to are as follows: 
* Title
* Year
* Rated
* Released
* Runtime
* Genre
* Director 
* Writer
* Actors
* Plot
* Language 
* Country
* Poster
* Ratings - which is an array of hashes
* imdbID 
* Type
* Production
* Website

These are important to for our database migrations as these will be the parameters we have access to through the API. 

**Step Five**

The generation. 

I wanted to create Movies(or all entertainment content) with a lot of stuff. I wanted robust content for my users to look at. After looking through all those keys I decided to use: Title, Released, Runtime, Genre, Director, Actors, Plot, Ratings, and Poster. 

We will need a model, controller, views and a migration. To get all of that in one shot you can use `rails generate resource`. Generating a resource will give you all of the above and then some. My generation looked like so: 

`rails g resource Movie title:string released_date:date runtime:integer genre:string directors:string poster:string actors:string plot:string imdbRating:float`

*Note: when using generators you do not have to specifiy string data type as it is the default value set when using a generator.*

This will give us access to a Movie table in our database with all of that data, a MoviesController, a Movie model, the 7 restful routes, and basic restful views. Nice! 

**Step Six**

Creation. 

Now we need to use all that stuff! In our model, we will need to create objects from the data we are getting from the API. I only wanted to create content that was not already in our database and therefore, I used a find_or_create_by method in the newly generated Movie.rb. 

```
def self.find_or_create_from_api(search)
        movie = OmdbService.search(search)
        @movie = Movie.find_or_create_by(title: movie["Title"], 
        runtime: movie["Runtime"], 
        genre: movie["Genre"], 
        release_date: movie["Released"],
        poster: movie["Poster"],
        actors: movie["Actors"],
        plot: movie["Plot"],
        imdbRating: movie["imdbRating"],
        director: movie["Director"]) 

    end 
```

Breaking this down, we are using our search method from OmdbService.rb and setting that to a local variable. This will be used to find our information at the key provided. Then we are using find_or_create_by on our movie model and setting that information to an instance variable we can use in the controller. The search argument is coming from a search method we will be creating shortly as the goal is to allow users to search for content. 


**Step Seven**

The controller. 

We want to handle two things in our index route, a search and a typical index. That is, we want the user to be able to see all the content as well as see their search results. We have access to @movie from our find_or_create_by which will return search data with the params in the model method. We call also call Movie.all to have access to all the content in the database. 

```
def index 
        @search = params[:search]
        if @search
            @movie = Movie.find_or_create_from_api(params[:search]) 
        else 
            @movies = Movie.all
        end 
    end 
```

If the user is searching, we are setting our params to their input data. The conditional if, else statement handles whether or not we have that data. If the user searches, we output the method from the model onto our index.html.erb if they don't search we display all the content. 

**Step Eight**

The search. 

```
<%= form_tag(movies_path, method: :get) do %>   
     <%= text_field_tag :search, nil, placeholder: 'Search by Title' %>
     <%= submit_tag ("Search")%>        
<% end %>
```

This is the search method I used. It uses a form_tag in which the user can input a title into the text-field and submit. The :search holds our params for all of the hard work we did before! 


**DONE!**

That's it. Calling the the API was a journey. Understanding the data in the hash and how to access it was tough, but totally worth it. All you have to do now is determine how you want to show the user all that beautiful data in the views! 

Feel free to access my [repo](https://github.com/jordles113/cinephile) to look at all of the code associated with this project. 
