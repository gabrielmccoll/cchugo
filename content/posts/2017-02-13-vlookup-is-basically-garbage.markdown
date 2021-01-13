---
title: Stop using Vlookup, It's basically garbage
date: '2017-02-13 14:00:09'
layout: single
tags:
- excel
- index
- microsoft-excel
- office-2007
- intermediate
- office-2013
- office-2010
- office-2016
- office365
- vlookup
---


### Okay that was click bait. Vlookup is merely bad.

Ohhh the controversy. Intermediate Excel Lesson time.  
 I've tried putting plenty of pictures to explain but tbh it's so much easier just to download the sheet and play along. So click [here](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/vlookupsucks-1.xlsx) and do that.

> I've used Vlookup for years , in a million spreadsheets.  
>  A million? Literally a million ?  
>  Yes.  
>  You are ridiculous and I refuse to engage with you further.

This has now become a monologue.

If you don't know what Vlookup does and how it works I shall tell you.  
 After which, never use it and use the other thing I‘m going to tell you again. Index Match (Match). 
 If you do know what it does then just read it all anyway. I put secret knowledge in amongst the stuff you already know. Hidden in the white spaces.

Below is our sample piece of data, which is a highly classified piece of documentation from my existing work.  
 It's part of the HR departments appraisal process. Only the top left part is the actual data table. The other sections are things I‘ve put in to explain at you.

![vlookup 1](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/Vlookupsucks1.jpg?resize=525%2C115)

Value 1 and Value 2 are the things we're interested in finding out. However, for Vlookup, only worry about Value 1 as it can't handle 2 Values. (it's garbage)

What Vlookup does for you is to pull the content out of a cell based on matching a row and an entered column number so if you look at syntax above in the green highlighted cell at bottom right or along the bar at the top of the imagr. The lookup_value is 102, the table_array is the bit in red and the col_index_number (number of columns from left) is 3.

So in the sample above, VLookup returns in the highlighted green square Kitchen. It‘s the line with 102 in it and it‘s 3 along. But what if my data isn't to the right of the Value I‘m using to search on? Well, then you‘re stuffed if you‘re using Vlookupª you know why by now.

Time to bring in the competition. **Index Match Match**

I‘ll break it down into the 3 parts. Index first.

INDEX is kind of like an even more crappy Vlookup on it‘s own. You give it an array. In blue below. You then tell it how many rows to come down and then columns to go along and it returns the value. In this case it‘s 1,1 so would return Priscilla Desert.

![vlookup 2](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/Vlookupsucks2.jpg?resize=525%2C204)

Let‘s give it a buddy. MATCH. With Match, you select a lookup Value (blue below), point it to an array (in red below) and then tell it how accurate a match you want with **1**,**0** or **-1** in the last comma space.Always use **0** for exact match. ┬áIt then returns the row or column number that it corresponds to. So in the below Match of Value 1 gives you 3. Third row down. (can you see what‘s coming?)

Column numbers are relative to the array area you select. So it‘s the third column in the array. Not the third in the sheet

![vlookup 3](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/Vlookupsucks3.jpg?resize=525%2C179)

What about another MATCH┬because you‘re wanting a value that goes left to right (columns) instead of up and down (rows), We got you covered buddy ! Exactly the same as before. In this below example, you get back **1** as it‘s the first column number that matches the number within the specified Array. 

![vlookup 5](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/Vlookupsucks4.jpg?resize=525%2C171)



So to sum up. INDEX returns the contents┬of a cell based on a row and column number and MATCH gives you a row or column number based on values you put  (see where this going NOW? ... please?)

So check the picture below. In that case we Have INDEX that has the two MATCH functions nested into it. That means it can bring you back the cell content based on two values.
SO in the below, you get back Stefan Legendary as he has Emp No. 102 and We want the Name column.

If we change Value 2 to Department it would say Kitchen, and so on.

![vlookup 6](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/Vlookupsucks5.jpg?resize=525%2C192)

Still not convinced it‘s better? okay so here‘s the data below. The Vlookup and Index Match Match are giving back the same value of Stefan Legendary.

![vlookup 6](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/Vlookupsucks6.jpg?resize=525%2C165)



What if the columns on the top left get switched about. Your data dump gets moved or you have to add columns ?

Now you see, INDEX MATCH MATCH still shows the correct answer of Stefan Legendary
 What about Vlookup that‘s only using Value 1 and a column number though ?

Ah.

Kitchen.

![vlookup 7](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/Vlookupsucks7.jpg?resize=525%2C164)

guess you better redo all those formulas then.

Told you !

VLOOKUP is basically Garbage.



####So what can I use IMM for?

Well I use it to put together an MI pack from 12,000 lines of data that fills in about 30 graphs. Also used it for making up Report card style things for teams from lines of data into nicely organised stuff. I just change the name. I can even change which pieces of data I want to bring through on the fly!

So handy whenever I needed to add new columns to my data.

Always have your data as unbroken columns with headers and boom, life is good.


