+++
title = "How to Calculate Sportsbook Hold"
subtitle = ""

# Add a summary to display on homepage (optional).
summary = ""

date = 2019-06-10T22:37:47-04:00
draft = false

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ["Mitch McLachlan"]

# Is this a featured post? (true/false)
featured = false

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = []
categories = []

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



I've been reading a lot of books about sports analytics lately. My current read is [The Logic of Sports Betting](https://amzn.to/31qcs0Q) by Ed Miller and Matthew Davidow. One of the first concepts discussed in the book is understanding the balanced hold of a sportsbook on a two-way market. In a two-way market, you take either one side of a bet or the other, for example Team A will win, or Team B will. The hold is simply the profit a book "holds" onto after all of the winning bets have been paid, divided by the total wagers placed. A balanced hold assumes (naively) that the book is holding balanced action on the wagers, meaning that no matter which side wins, the book will have the same amount of profit. This is rarely realty for a number of reasons. However, the concept is emphasized as important, presumably because it will be needed for later concepts. While a couple of contrived examples are presented, the _how_ of to calculate the balanced hold is not spelled out&mdash;at least in the chapters I've read thus far.

I wanted to be able to quickly calculate the hold myself, so I [tried a few unsuccessful Google searches]({{< ref "/post/google-search-history-for-post-inspiration/index.md" >}}). So in about 15 minutes I figured out how to calculate it myself, first via doing the math on paper, then building a spreadsheet calculator to make it easier to calculate on the fly as needed.

Here's some examples laid out in the book in American Odds:

| **Example 1** | Team A | Team B |
| --- | --- | --- |
| Odds | -150 | +130 |
| Wagers Placed | $150.00 | $108.69 |
| Potential Payout | $100.00 | $141.30 |
| Potential Profit</br>(Losing Wager Kept minus Winning Bet Payed Out) | $8.69 | $8.70 |
| Balanced Hold</br>(Payout divided by total wagers taken) | 3.4% | 3.4% |

The last part, calculating the hold percentage is the easiest: you just divide the profit by total money wagered. The harder part is to figure out what the profit and amount wagered need to be in order for the action to be balanced. For that to happen, we need to have the amount paid to each possible winner subtracted from the amount wagered by the respective loser to be equal. For example 1 that looks like:

Profit from Team A winning = Wagers placed on Team B minus payout on Team A wagers = $108.69 - $100.00 = $8.69

Profit from Team B winning = Wagers placed on Team A minus payout on Team B wagers = $150.00 - $141.30 = $8.70

Profit from Team A winning = Profit from Team B winning

The way I tackled this problem was to start with calculating the wager to payout ratio for each bet. Since we'll need to be calculating both the wager and payout amounts, we need this ratio to solve later variables. I won't go into how dumb American odds are, but a quick overview of what they represent. If a wager's odds are negative (-150), that means you have to risk $150 to win $100. If a wager's odds are positive (+130), then if you wager $100 you will receive the odds ($130) back. Therefore, in example 1, the ratio of wager to payout for each bet is:

| **Example 1** | Team A | Team B |
| --- | --- | --- |
| Odds | -150 | +130 |
| Wagers Placed | $150.00 | $100.00 |
| Potential Payout | $100.00 | $130.00 |
| Payout-to-Wager Ratio | 0.667 | 1.3 |

You can do this in a spreadsheet formula would look something like this:

```
=IF(odds<0, 100/ABS(odds), ABS(odds)/100)
```

In order to solve the problem, I started by assigning an arbitrary wager and payout for one side of the bet. In this case, I just set side A with potential payout of $100, so risking $150. If it was a plus bet, I'd set it to risk $100 and payout to the odds. The formula for the risk would look like:

```
=IF(odds<0, ABS(odds), 100)
```

Then for the payout you would just multiply your risk to the ratio calculated above.

| **Example 1** | Team A | Team B |
| --- | --- | --- |
| Odds | -150 | +130 |
| Wagers Placed | $150.00 | X |
| Potential Payout | $100.00 | Y |
| Payout-to-Wager Ratio | 0.667 | 1.3 |
| Potential Profit | $? | $? |
| Balanced Hold | ?% | ?% |

Since our objective is to have the profit for both columns to be equal, we can setup the equation like this:

```
Wagers placed on Team B minus payout on Team A wagers =
  Wagers placed on Team A minus payout on Team B wagers

X - 100 = 150 - Y
```
Since previously calculated the ratio between X and Y, we can replace Y with a factor of X:

```
Y = 1.3X
X - 100 = 150 - 1.3X
```

Now we can solve for X:

```
2.3X - 100 = 150
2.3X = 250
X = 108.6956...
Y = 1.3X = 141.3043...
```

Let's plug in these new values for wagers and payouts on Team B, and calculate the profit on each outcome:

| **Example 1** | Team A | Team B |
| --- | --- | --- |
| Odds | -150 | +130 |
| Wagers Placed | $150.00 | $108.70 |
| Potential Payout | $100.00 | $141.30 |
| Payout-to-Wager Ratio | 0.667 | 1.3 |
| Potential Profit | $8.70 | $8.70 |
| Balanced Hold | 3.4% | 3.4% |

Success! If you aren't interested in recreating this yourself, you can just grab a copy of my [Balanced Sportsbook Hold Calculator](https://docs.google.com/spreadsheets/d/1rDHQXbUSeyeHXhxSUImxnFSV3EAk_Dc2_8Lcsq9IntY/edit?usp=sharing) Google Sheet.

## Multi-way Markets

I was feeling super proud of myself until I got to the next chapter, which spelled out how it's equally important, but _much harder,_ to calculate a multi-way market. A two-way market takes the form of

* Who will win the 2019 NBA Playoffs?
 1. Toronto Raptors
 1. Golden State Warriors
* Or How many games will the 2019 Boston Red Sox win?
 1. More than (Over) 94.5
 1. Fewer than (Under) 94.5

 A multi-way market has more than two options. Such as:

* Result of 2019 Women's World Cup Match between USA and Thailand:
  1. USA wins
  1. Thailand wins
  1. Tie
* Or Who will win the 2019 Women's World Cup (24 options):
 1. France
 2. USA
 3. Germany
 4. England
 5. Netherlands
 6. Australia
 24. et c.

 After much more scratchpad/Google Sheet math, I realized that for each option in a bet, regardless of how many options there were, the sum of the wagers and payouts would be equal. In the example above, the sum of wagers and payouts were both $250. What changed for each option was the ratio of the risk and payout, and that adding the two together was the key. In the example above, the ratio of 1:1.3 became 2.3 when summed. It just so happens that 2.3 is the [_decimal odds_](https://www.pinnacle.com/en/betting-articles/educational/odds-formats-available-at-pinnacle-sports/ZWSJD9PPX69V3YXZ) version of a +130 bet. The second tab of my google sheet can [calculate the hold on any bet with up to 24 positions](https://docs.google.com/spreadsheets/d/1rDHQXbUSeyeHXhxSUImxnFSV3EAk_Dc2_8Lcsq9IntY/edit?usp=sharing).
