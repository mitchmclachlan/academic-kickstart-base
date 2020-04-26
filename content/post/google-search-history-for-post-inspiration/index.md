+++
title = "Using Your Google Search History for Content Inspiration"
subtitle = ""

# Add a summary to display on homepage (optional).
summary = "How to use your Google Search History to inspire your writing."

date = 2019-06-10T20:34:34-04:00
draft = false

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ["Mitch McLachlan"]

# Is this a featured post? (true/false)
featured = false

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["Web", "SEO"]
categories = []

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["deep-learning"]` references
#   `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects = ["Web"]

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
[image]
  # Caption (optional)
  caption = ""

  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = ""
+++
One of my goals when I [migrated my personal site]({{< ref "/post/relaunch/index.md" >}}) from Wordpress to a static site was to write more often. I was happy with how quickly I was able to bang out a few posts but soon ran out of topics that I wanted to write about. I made a list to hold ideas as they came to me, but I haven't added anything in months.

I recently came across a question that I couldn't get answered within a few quick Google searches, so I had to figure it out myself. This happens a couple of times a month, whether because what I'm searching for is novel, or niche, or simply phrased differently from how an expert might approach the topic. Perhaps this presents opportunities to publish content for searches that are result poor, and that can drive some site traffic.

So I decided to dig into my Google search history to see topics that I have searched for in the past, but for which I had to discover the answers on my own. Here's how:

1. Go to [Google's Account Takeout feature](https://takeout.google.com/)
1. In the "Select data to include" section, under the "Products" header, click Deselect all
1. Select "My Activity", click on the "all activity data selected", deselect all, then select the products you are interested in. I chose just "Search" for this proof-of-concept, but you may want to also include Books, Image Search, Shopping, Video Search and YouTube
1. Click on the "Multiple formats" button, and select JSON instead HTML for the "Activity records" option.
1. Select "Next Step", one-time export, create archive, you'll get an email. The default format is HTML, but if you are going to do anything interesting with your data, I recommend selecting JSON as your format.

You'll receive an email alerting you that a data archive request has been requested, then a second once it's available for download.

Here's some simple playing I did as part of the proof-of-concept.



```python
import pandas as pd
from PIL import Image
from wordcloud import WordCloud, ImageColorGenerator
import matplotlib.pyplot as plt


# Read the JSON into a data frame, convert the time column into datetime
# and add a flag that distinguishes between searches and other activity.
# You could also dump the JSON back to a CSV if you want to analyze in Excel.

activity = pd.read_json('Takeout/My Activity/Search/MyActivity.json')
activity['time'] = pd.to_datetime(activity['time'])
activity['searches'] = activity['title'].apply(lambda x:
        'Yes' if x[:13]=="Searched for " else "No")


# Create a temporary copy of the activity DF, but only include
# actual searches since the beginning of the year.
# Strip off the first 13 chars from the title to leave just the query,
# then put the queries in a list.

_ = activity[(activity['searches']=='Yes') &
              (activity['time'] >= "2019-01-01")].copy()
_['query'] = _['title'].apply(lambda x: x[13:])
queries = list(_['query'])


# Join each query into a single string, then run it through WordCloud.

text = " ".join(query for query in queries)
wordcloud = WordCloud(max_font_size=50, max_words=25,
                      background_color="white").generate(text)
plt.figure()
plt.imshow(wordcloud, interpolation="bilinear")
plt.axis("off")
plt.show()
wordcloud.to_file("my_search_history.png")


```


And the result:

{{< figure src="featured.jpg" title="Sports, Data, Python, and Grilling. Sounds about right." >}}  


A couple of notes:

* If you download data from multiple products, they will be zipped up into separate folders by product. You could step through the folders and join them into a single data frame if so inclined, or look at them separately. For instance, if also conduct a lot of YouTube searches looking for "how-to" content. I expect video content for these queries, so I might mine video search and YouTube data for ideas of video content to create, but may not find much blog-worthy ideas there.
* I use separate multiple Google accounts: one personal, one each for each business email domain I have. I have created separate Chrome profiles for each account. I can mine my history for each account to find more specific types of activities. For example, the above is from my personal account. But when I look at the search history tied to my main work account, I find a lot more searches about Python, data science, and other platforms my company uses.

## Update

It's been about 6 weeks since I published my [first post based on this approach]({{< ref "/post/how-to-calculate-sportsbook-hold/index.md" >}}) and the results are very encouraging. My organic search sessions are up a whopping 740% period-over-period since. It's still small potatoes traffic-wise, but I have not done any SEO work other than writing for a topic that I could not find answer for in Google search results.

{{< figure src="organic_search_traffic.png" title="Organic Search Traffic up 740%" >}}

What's more, I'm ranking in the top 3 for a number of queries that I originally searched for, out-ranking some very established properties and even have my first featured snippet for "[calculating sportsbook hold](https://www.google.com/search?q=calculating+sportsbook+hold)":

{{< figure src="SERP_calculating_sportsbook_hold_feat_snippet.png" title="First Featured Snippet" >}}
