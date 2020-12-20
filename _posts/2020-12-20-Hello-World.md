---
layout: post
title: 'Data Analyst Projects Blog'
published: true
---

Producing a helper-script for political election 2020 in Python

The main points of the script were to create a way to come up with an election choice much faster with web-scrapping and keyword searching based on user input. The language of the program was Python.

The first script is to scrape the web for the candidates from the Michigan election in my voting district. Then when the websites are in HTML within text files, the user is given the open to 
move over to the keyword search script. The goal here is to get all the data on the candidates from scraping 1 webpage from their website. The idea was to get the computer to do
the hardwork of researching the candidates rather than reading the websites myself. Note the references for script structures in the comments of the code:

Then the keyword search program is run. It looks at all the HTML text and searches for a keyword based on the user's input. The goal here is to get the lines of the website that relate to the issues that I care enough to search for. This helps narrow down the research to the lines that I want to know about. The other part,, is that the code generates what website the return lines are from, this way I know what candidate I am reading. It speeds up the process of deciding a little bit, since I narrowed down my reading and research to a few dozen lines. 

Looking back the the script, it could be more beneificial to scrape only those pages ofd candidates that I cared about by some personal preference. It would also be better to scrape the whole website and not only a certain page. The other part is that the keyword search needs to be expanded to multiple searches or keywords per search, so that it can return the most valuable information right
away and not through multiple searches. I had some ideas to work out the project into a another script where the keywords could be ranked by order of interest and then the results of where those keywords are along with the corresponding lines from the HTML could be counted and given in a summary form (statistics) for each website. So, for exampl, if I'm interested in three issues: Guns, Abortion, and Marriage, I can search those terms
and then the return from the func tion would be a list of the kyword and the candidatess websites (or names) whre they results where found and the number of lines where those keywords show up. (i.e, Guns: Donald Trump: 27...). Or, the results of the keyword
search could be putin to order in the printing thus ranking them out of the gate. Lots of ways to make new creative ways to make it easier to decide on a candidate.
