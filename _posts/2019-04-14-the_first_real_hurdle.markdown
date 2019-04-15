---
layout: post
title:      "The First (Real) Hurdle"
date:       2019-04-14 21:57:49 -0400
permalink:  the_first_real_hurdle
---


My first foray into writing code – from scratch – into my own functioning, albeit simplistic, program has come and gone and I am left with feelings of relief and hardly-contained excitement. The project allowed me the opportunity to utilize the full repertoire of coding skills I have been building simultaneously requiring me to make some of my own discoveries to get my code functioning. Surprisingly, the bulk of the code writing was not the biggest road block for me while completing this project, which incidentally might be a testament to the Flatiron curriculum. The real difficulties were rather unexpected and could have only been found through the completion of my own project.

## In The Beginning...There Was Nothing

The first thing that struck me at the onset of this project was just how daunting a blank file can be in my IDE. This was the first time I was truly creating something from scratch – no pre-written labs with clearly defined tests and expected output – and I must say, it took some time to wrap my head around where to even begin. Fortunately, as has been the case on numerous occasions during my software development learning, the greater coding community came to the rescue. After seeking the help of the usual suspects (Google and my Flatiron cohort), I was able to discover the nifty Bundler gem.  

`bundle gem hostel_finder`

After running the simple command above, I was able to provide a general structure to my IDE project tree. While the files within still did not contain any of my own code, just seeing files and a general structure within my project tree sent me well on my way to overcoming the writer's block that immediately set in when I first saw the blank IDE. While a seemingly innocuous "problem", getting over this hump was major progress towards my overall project completion. 

## Anyone Have a Map?

Once I was setup with a usable project tree within my IDE, I found myself rooted on the spot once again. I knew the ultimate goal of my program and had a general sense of the classes/objects that would be required to successfully meet this goal; however, I was at a loss as to which order the portions of my project should be constructed to ultimately guide me to the completed project. After giving it some thought, it occurred to me that it did not make sense to begin writing classes, objects, etc. without providing structure to the output that I desired using these elements. This led me to deciding that the CLI class, the portion responsible for output to the user, was a good place to start. 

I began by writing an initial call method that listed out, in plain english, the actions I desired. While writing these actions in plain english was not going to allow the program to properly function, it helped greatly in realizing which methods I would need. In the end, this initial plain english call method had a rather astounding resemblance to the finalized call method.

Initial call method:

```
  def call
    # Welcome user 
    # Scrape main webpage to collect categories
    # Display scraped categories
    # Request and collect user input for categories they want to view
    # Scrape page to collect hostels within category 
    # Request and collect user input for hostel they want info on
    # Allow user to select another hostel or quit program
    # Goodbye to user
  end
	```

Final call method:

```
  def call
    puts "\nWelcome! Let us help you find some of the world's best hostels!".green.bold
    puts "Enter 'exit' at any time to quit the program.\n".red
    HostelFinder::Scraper.new.scrape_categories
    print_categories
    category_select
    hostel_select
    restart?
    goodbye
  end
	```
	
While the initial call method had no other usable methods, it assisted me in two ways: 
	
1. 	It showed me that I was going to need to scrape fairly early on, which ultimately led me to building my Scraper class next.
2. 	It showed the need for display and selection methods, which relied on the scraped data. This indicated these methods could wait until after the Scraper class was built.

While containing no usable code, writing the sequence of events within my call method in plain english, provided a clear roadmap that would guide the way to my completed program.

## Harsh Reality of Scraping

I was one of the fortunate few that had no issues scraping my webpage of choice for this project...initially. Once I had successfully implemented the original functionality that I desired from my program, I was feeling rather ambitious and wanted to add an additional feature. This additional feature would require scraping an individual hostel's webpage on HostelWorld.com and providing the user with available beds during the requested date range and the corresponding price. At this point, I was feeling like an expert with the CSS selector and expected no issues pulling this information.
I confidently entered the code below into a Repl.it session to confirm I gathered the correct data:

```
webpage = Nokogiri::HTML(open(booking_page))

test = webpage.css("section#availability div.columns.small-12 form.form")
test
```

...and received something I had been dreading since the start of this project:

```
=> []
```

An empty array. After trying countless CSS selectors and consulting with my fantastic cohort lead, I had to face the harsh reality that scraping the data I needed was not possible with my current knowledge. While I was still riding the high of completing the original functionality of my program, realizing I was unable to complete this added functionality was able to humble me quickly! I have since recovered from this disappointment and fully intend to circle back and defeat this obstacle once I have the necessary skills.

Being able to create and successfully implement my own program was a fantastic feeling and an achievement of which I am proud. It both highlighted how far I have come as a developer-in-training but also threw the occasional reminder of how far I have yet to go. I'm feeling as enthusiastic as ever and am ready to progressing down this path towards becoming a true Software Developer.
