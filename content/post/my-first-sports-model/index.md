+++
title = "My First Sports Model, Part 1"
date = 2019-02-15T21:31:29-05:00
draft = false

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ["Mitch McLachlan"]

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["Modeling", "NBA"]
categories = ["Sports Analytics"]
summary = "Introducing: BasketballMitchster"

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["deep-learning"]` references
#   `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
# projects = ["internal-project"]

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
[image]
  # Caption (optional)
  caption = ""

  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = ""
+++
_Pictured above: 40% of the US squad of the NBA All-Star Game Rising Stars Challenge are on my fantasy team, including mercurial MVP, Kyle 'The Kuz' Kuzma._

_This will be a 'living' post for a bit. I'll be updating it over time as I make progress on the model. I've decide to split the post up into at least 2 parts. This is part one, where I'll cover how I planned my attack and built my initial model in Google Sheets. I'll probably publish a cleaned up version of that doc for reference._

_Part 2 will cover the migration of the model to Python, including use of the Yahoo Sports API. I'll likely put the code up on GitHub or the like._

_I may tack on more parts later for a retrospective review of performance, lessons learned and updates I'll be making for next season._

## Abstract
Last year I joined a bunch of my old fraternity buddies in the fantasy basketball league that they have been running in some form or another since college.

Once long, long ago I was a huge Pistons fan. However, when I moved to Chicago in 2008 I lost the ability to watch the team regularly. Having grown up in the Bad Boys vs Jordan-coming-of-age era, I couldn't very well pivot to being a Bulls fan. The 'Stones also quickly fell from being on the verge of multiple titles to being pathetic. All this combined to make me lose interest in the NBA.

As a result, I felt out of my element as I tried to prepare for my first ever draft last summer.
## Sports Modeling Resources
I've recently discovered some resources for getting started in building models for sports analytics.

* [A toolbox for football analytics](https://statsbomb.com/2016/09/a-toolbox-for-football-analytics/) By Marek Kwiatkowski
* [Deep Dive Ep 133 - NFL Lessons Learned ft @SamkonGado](https://soundcloud.com/whale_capper/deep-dive-ep-133-nfl-lessons-learned-ft-samkongado) by [@whale_capper](https://twitter.com/whale_capper) and [@AndyMSFW](https://twitter.com/AndyMSFW)
* [Deep Dive Ep 134 - Hoops Modeling](https://soundcloud.com/whale_capper/deep-dive-ep-134-hoops-modeling) by [@whale_capper](https://twitter.com/whale_capper) and [@AndyMSFW](https://twitter.com/AndyMSFW)

Unfortunately, I did not have these resources last summer. However, each of these are aligned with how my brain goes about solving problems anyway, so I followed the same basic approach from logic. There is a lot of good advice in these resources that I'll sketch out here as it parallels my own approach, but I recommend you consume them each for yourself.
## Problem Statement (Part 1)
So here I am, the new guy prepping for his first draft with _zero_ knowledge of fantasy NBA. Don't get me wrong, I watch a _lot_ of basketball; it just happens to be college basketball.

I was informed that our particular league generally prescribes to the player valuations of [Basketball Monster](https://basketballmonster.com/). I spent a lot of time last summer looking at Basketball Monster's player valuation projections. While I understood the data, I'm the kind of person that needs to know the __why__ not just the _what_. I downloaded the data a number of times, sliced, sorted, and highlighted it but never felt more prepared for the rapidly approaching draft.

Other than just looking for value, it bothered me that I couldn't devise a coherent strategy without the __why__.

### First Attempt
Last spring I read [Nate Silver's _The Signal and the Noise_](https://www.amazon.com/gp/product/0143125087?ie=UTF8&tag=middleweststu-20&camp=1789&linkCode=xm2&creativeASIN=0143125087). An early chapter covered how he built his own MLB player evaluation model. There isn't a lot of detail in the book about how his model, [PECOTA](https://en.wikipedia.org/wiki/PECOTA) was built other than:

* He built the original model in excel
* He built unique age curves for projecting future performance
* It was a [nearest neighbor model](https://en.wikipedia.org/wiki/Nearest_neighbor_search)

I did some googling and followed this [K nearest neighbors in Python tutorial](https://www.dataquest.io/blog/k-nearest-neighbors-in-python/) that focused on NBA players. I learned a bit, but the results were a little underwhelming when the comps for Carmelo and LeBron were Carmelo and LeBron from different years.

### Taking Maters Into My Hands
After a number of attempts of downloading and manipulating Basketball Monster's projections to attempt to create a coherent draft plan, I knew that I needed to build my own model. But how, and what was I trying to accomplish?

1. Don't look like an asshole. This was my first rodeo with some old friends. I didn't want to look like a tourist.
2. Compete. I'm extremely competitive. I wouldn't have even joined this league to just hang out.

But how do I compete? I had to look at past league results to wrap my head around what it took to compete for a championship.

#### If You Wish To Make a Model From Scratch You Must First Invent the Universe

This [old Cosmos remix video](https://www.youtube.com/watch?v=zSgiXGELjbc) is always the first thing to come to mind when I start a modeling project. I have a strong suspicion that this is going to be the banner I hold for the foreseeable future BUT in order to create your own model, first you have to build your own universe of known (or at least relevant) data.  I had to ping a number of my buddies to find links to past data. I compiled it into a Google sheet like the below. As you'll see in a later iteration of the model, if you had previous seasons for a league, there are more elegant ways to track down the IDs. While this is fairly extensive list, when I first started on my model, I only had access to the previous 3 years data.

| Year | League ID | URL |
| --- | --- | --- |
| 2018 | 103 |  https://basketball.fantasysports.yahoo.com/archive/nba/2018/103  |
| 2017 | 147 |  https://basketball.fantasysports.yahoo.com/archive/nba/2017/147 |
| 2016 | 117 |  https://basketball.fantasysports.yahoo.com/archive/nba/2016/117 |
| 2015 | 390 |  https://basketball.fantasysports.yahoo.com/archive/nba/2015/390 |
| 2014 | 1024 | https://basketball.fantasysports.yahoo.com/archive/nba/2014/1024 |
| 2013 | 4925 | https://basketball.fantasysports.yahoo.com/archive/nba/2013/4925 |
| 2012 | 498 |  https://basketball.fantasysports.yahoo.com/archive/nba/2012/498 |
| 2011 | 873 |  https://basketball.fantasysports.yahoo.com/archive/nba/2011/873 |
| 2010 | 361 |  https://basketball.fantasysports.yahoo.com/archive/nba/2010/361 |
| 2009 | 93959 | https://basketball.fantasysports.yahoo.com/archive/nba/2009/93959 |
| 2008 | 1770 | https://basketball.fantasysports.yahoo.com/archive/nba/2008/1770 |
| 2007 | unknown | unknown |
| ... | ... | ... |
| 2002 | unknown | unknown |

### Now We're Cooking
While I could have gone to each page and copy and pasted the tabular results data one at a time, I wanted to make a more sustainable and maintainable model. Given these data links, I was able to use Google Sheets [IMPORTHTML](https://support.google.com/docs/answer/3093339?hl=en) function to aggregate historical results. I had some issues with sheets not always being able to consistently get the data out of the Yahoo pages. So when they would load for the first time, I immediately copied and pasted values only in separate sheets to create persistent static copies of the data. In fact, after a system update to Yahoo, the data was being loaded later with javascript, so the IMPORTHTML function stopped working at all. Again, this will not be an issue in later versions of the model.

In our league, the top 4 managers finish 'in the money.' Once I had pulled in the data, I wanted to visualize it to understand what each category meant for these money winners.
{{< figure data-src="2017_radar.svg" src="data:image/png;base64,R0lGODlhAQABAAD/ACwAAAAAAQABAAACADs=" title="2017 In The Money Radar Chart" >}}  
{{< figure data-src="2016_radar.svg" src="data:image/png;base64,R0lGODlhAQABAAD/ACwAAAAAAQABAAACADs=" title="2016 In The Money Radar Chart" >}}  
{{< figure data-src="2015_radar.svg" src="data:image/png;base64,R0lGODlhAQABAAD/ACwAAAAAAQABAAACADs=" title="2015 In The Money Radar Chart" >}}   
### So What Do We See?
I had heard there was a brief 'Muck Free Throw' strategy era, that while registering as inclusive to the 'in the money' set, is not correlated to winning the league. I think it had also mostly been abandoned by the period in question. Most obviously, doing poorly in the turnover category is strongly correlated to finishing in the money.

To recap the placing analysis: be good at everything else at the expense of turnovers. This was not an extremely insightful input for my strategy. I knew I'd have to find a means for determining the relative value for each player. My assumption was that this was a case where the value of the whole was equal to the sum of the parts.

So I split out each category's historical results into their own tabs on my spreadsheet. We're a 9 cat league, so that meant 9 new sheets for my quickly ballooning 'model'. Here's an example of the three seasons of data I had for a single category, Blocks. I captured the season, the team's rank, the team name, actual totals for the stat, the final roto points that the player received for those stats.

| Season | Season Rank | Team | BLK Totals | BLK Points |
| --- | --- | --- | --- | --- |
| 2017 | 1 | Leonine Facies | 681 | 14 |
| 2017 | 2 | Chupacabras | 659 | 13 |
| 2017 | 3 | Sorry For Partying | 384 | 2 |
| ... | ... | ... | ... | ... |
| 2015 | 12 | Chupacabras | 457 | 3 |
| 2015 | 13 | the brass rats | 428 | 2 |
| 2015 | 14 | The Jabberwocks | 468 | 4 |

I could use the above to see if I could project roto points to raw stats _for a team season_. However, my objective is to understand the value of individual players. We have 10 starters in our league, so I divided the season number of stats AND roto points by 10 as two new columns.

| Season | Season | Team | BLK Totals | BLK Points | Adj BLK Totals | Adj BLK Points |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | Leonine Facies | 681 | 14 | 68.1 | 1.4 |
| 2017 | 2 | Chupacabras | 659 | 13 | 65.9 | 1.3 |
| 2017 | 3 | Sorry For Partying | 384 | 2 | 38.4 | 0.2 |
| ... | ... | ... | ... | ... | ... | ... |
| 2015 | 12 | Chupacabras | 457 | 3 | 45.7 | 0.3 |
| 2015 | 13 | the brass rats | 428 | 2 | 42.8 | 0.2 |
| 2015 | 14 | The Jabberwocks | 468 | 4 | 46.8 | 0.4 |

Now I could see if I could project roto points based on an individual player's counting stats. To do this in Google Sheets, I plotted the two player-adjusted columns as a scatter plot, added a trend line, and displayed the trend line's formula and R^2 score.

{{< figure data-src="blk_regression.svg" src="data:image/png;base64,R0lGODlhAQABAAD/ACwAAAAAAQABAAACADs=" title="" >}}  

Pretty good! Strong R^2 score with a simple linear regression. However, I was young and naive way back 8 months ago. When a simple linear function didn't match as well as my block model, I tried other regressions. Take for instance this simple linear regression for the FT% category:

{{< figure data-src="ftpt_lin_regression.svg" src="data:image/png;base64,R0lGODlhAQABAAD/ACwAAAAAAQABAAACADs=" title="" >}}

Honestly, that's pretty good. But those outliers bugged me since I had already seen the super tight fights for the counting stats. So I tried a polynomial regressor:

{{< figure data-src="ftpt_regression.svg" src="data:image/png;base64,R0lGODlhAQABAAD/ACwAAAAAAQABAAACADs=" title="" >}}

Oh, no. There's a few problems here. First, the R^2 is basically the same as the blocks thanks to the polynomial trend line. That's great, right?! I now know that this is overfitting. I don't even _need_ that left outlier point to fit well. It's terrible and all the other points would lead to a great R^2 with a simple linear regression.

Second, I've backed into player values by dividing historical team points by the 10 roster spots afforded. For counting stats, I also divided the stat itself by 10. But for percentages that doesn't work, so I left that scaled at 1. However, the worst _team_ FT% in the sample was a shade under 65%. However, for the 2017-18 season (the data I was going to use to project values from) a full 21% of the league shot worse that 65%. When you apply my overfit model to *that* data you'll find this:

{{< figure data-src="ftpt_act_regression.svg" src="data:image/png;base64,R0lGODlhAQABAAD/ACwAAAAAAQABAAACADs=" title="The Dwight Howard Experience" >}}

I couldn't figure out why Basketball Mitchster loved guys like Dwight Howard and Andre Drummond so much more than Basketball Monster. Turns out my overfit regressor, thanks to it's polynomial function, __rewarded__ poor free throw shooters. A guy who missed all his free throws was more valuable to my model than a guy who made every one. By 400%. IN THE FREE THROW PERCENTAGE CATEGORY. My overall model rewards centers for picking up blocks, which are a very scarce counting stat concentrated in their position group, AND due to overfitting, over values their poor free throw shooting. Here's what a more reasonable model would have looked like:

{{< figure data-src="ftpt_act_lin_regression.svg" src="data:image/png;base64,R0lGODlhAQABAAD/ACwAAAAAAQABAAACADs=" >}}

Don't overfit, kids. Basically any player under ~70% FT% should drag _down_ his team's points.

Once I had found my equations (via the the regression equations from the trendlines in my graphs) I still had to figure out how to correctly model the FG% and FT% categories. While my polynomial regressors were problematic due to overfitting, I wouldn't know that for months. No, the issue I knew I had to deal with was shot _volume_.

Say player A is projected to provide 1 roto point for free throw percentage, and another 0.5 points, it would seem putting the two together would lead to 1.5 roto points. For counting stats, this is true. But for percentages, the volume of shots impacts the category greatly because the entire team's stats are compiled to calculate the percentage.

For instance, let's say I started my team with just two draft picks, Chris Paul and Dwight Howard:

* Chris Paul:
 - Attempted 221 free throws in the 2017-18 season, the *69th most* in the league that season
 - Made 91.1% of them, *tied for 5th in the league* for players who attempted more than 10 free throws
* Dwight:
 - Attempted 582 free throws in the 2017-18 season, the *forth most* in the league that season
 - Made just 57.4% of them, making him the *443rd worst* player by percentage out of 500 players who attempted a free throw that season

 So let's see how these players stack up alone and combined:

| Player | FTM | FTA | FT% |
| --- | --- | ---| --- |
| Chris Paul | 203 | 221 | 91.86% |
| Dwight Howard | 334 | 582 | 57.39% |
| __'Team'__ | **537** | **803** | **66.87%** |

Chris Paul is the *fifth best* free throw shooter in the league AND is top 70 in attempts. BUT Dwight Howard is _so bad_ AND shoots so many more shots, that he drags this lineup down to be on par with the worst shooting team in my sample set.


## All Models Are Wrong...

## ...Some Models Are Useful
