---
id: litvis

narrative-schemas:
  - ../../narrative-schemas/project.yml

elm:
  dependencies:
    gicentre/elm-vegalite: latest
    gicentre/tidy: latest
---

@import "../../css/datavis.less"

```elm {l=hidden}
import Tidy exposing (..)
import VegaLite exposing (..)
```

# Data Visualization Project Summary

{(whoami|} David Adeniji david.adeniji@city.ac.uk {|whoami)}

{(task|}

You should complete this datavis project summary document and submit it, along with any necessary supplementary files to Moodle by **Sunday 18th April, 5pm UK time (16:00 GMT)**. Submissions will be awarded up to **80 marks** towards your coursework assessment total.

You are also encouraged to regularly commit and push changes to your datavis project throughout the term as you develop your project.

{|task)}

{(questions|}

The aim of this paper is to understand more about my trading behaviour in my first year of trading and how this may be influenced by the market and trader sentiment. My data was collected from Trading 212 and pre-processed in python. Particular questions I would like to investigate are:

- What trading behaviour happened during this period?
  - Seasonility
- To what extent have my trading habits changed over my first year trading?
  - Did performance improve?
- Is there a correlation between general trader sentiment of a given stock (trading volume, news and google search volume) and my trades?

Hypothesis

{|questions)}

{(visualization|}

Visualisations available at https://data-viz-city.herokuapp.com/ & 'Visualisations.html'

Please note web server hosting visualisations closes after 30min of inactivity and takes afew seconds to load back up

```elm {l=hidden interactive}
myBarchart2 : Spec
myBarchart2 =
    let
        data =
            dataFromUrl "https://raw.githubusercontent.com/addenergyx/datasets/main/day_count.csv" []

        enc =
            encoding
                << position X [ pName "Trading day", pTemporal ]
                << position Y [ pName "Trading Activity", pQuant ]
                << tooltips
                    [ [ tName "Trading day", tTemporal ]
                    , [ tName "Trading Activity" ]
                    ]

    in
    toVegaLite [ width 600, data, enc [], bar [] ]
```

```elm {l=hidden interactive}
euChart : Spec
euChart =
    let
        data =
            dataFromUrl "https://raw.githubusercontent.com/addenergyx/datasets/main/execution_pct.csv" []

        enc =
            encoding
                << position X [ pName "Trading day", pTemporal ]
                << position Y [ pName "Percent", pQuant ]
                -- << color [ mName "Type" ]
                << color
                  [ mName "Type"
                --   , mScale
                --       (categoricalDomainMap
                --           [ ( "Buy", "rgb(49,148,247)" )
                --           , ( "Sell", "rgb(247,0,0)" )
                --           ]
                --       )
                  ]
                << tooltips
                [ [ tName "Trading day", tTemporal ]
                , [ tName "Percent", tTitle "Percent" ]
                --, [ tName "Buy", tTitle "Buy Executions" ]
                --, [ tName "Sell", tTitle "Sell Executions" ]
                ]
    in
    toVegaLite [ width 600, data, enc [], bar [] ]
```

```elm {l=hidden}
myVis : Spec
myVis =

    let
        data =
            dataFromUrl "https://raw.githubusercontent.com/addenergyx/datasets/main/bubble_plot1.csv" []

        enc =
            encoding
                << position Y [ pName "Trading day", pNominal
                , pSort [soCustom (strs ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday"])]
                ]
                << position X [ pName "Trading_time", pNominal
                , pSort [soCustom (strs ["07:00", "08:00", "09:00", "10:00", "11:00", "12:00", "13:00", "14:00", "15:00", "16:00", "17:00", "18:00", "19:00", "20:00", "21:00"])]
                ]
                <<size [ mName "Count", mQuant ]
                << tooltips
                    [ [ tName "Trading day", tNominal]
                    , [ tName "Count", tTitle "Count" ]
                    ]
                << color
                [ mName "pct_buy"
                ,mQuant
                ,mScale
                    [scScheme "redblue"[] ]
                ]
    in
    toVegaLite [ width 600, height 300, data, enc [], circle []] 
```

{|visualization)}

{(insights|}

1. When looking at the dataset as a whole, there are some interesting patterns. To consider the difference in trading activity for each day and hour of the week, V1-V2 used a bubble map. The graph was a useful tool for identifying trends that could be predicted. Such as there was increased trading activity at UK Open, US market open (14:30) and US market close. This is also very much a beginner’s approach to trading. Based on research[1] it would be advisable not to do this as the odds of having the best price for a stock are smaller as the fluctuations are generally higher due to the higher trading volume during this period. This can result in paying a premium for the stock. Professional traders often know that a lot of “dumb money” is flowing at this time. We could also see that the overall peak time for trades is around 2-3pm, which can be discerned from the size of the bubble plots and confirmed with the help of tooltips. I have less trading activity on Friday, and it is more evenly distributed throughout the day. Markets tend to have less movement towards the end of the week.
     <br />Looking at the animated bubble plot, we can see I mainly traded in the first half of the day when I started trading but progressively moved to the later part of the day as the year moved on.  By adding interactivity, it is easy to see over time my trading behaviour. However, we must acknowledge that animation can also blind people to essential changes in data. As a result, there is a slider to allow analysts to review trends at their own pace. 

2. By looking at Vis 3, my trading activity seemed to follow fiscal quarters. Increases in trading activity on the lead up to earning reports then decrease. The philosophy behind this was traders would buy stocks on the lead up to earnings then sell after the report. This is the ideology of ‘Buy the hype, sell the news’. Looking at some stocks in Vis 5 is also the case with increased trading activity around earnings. Vis 4 emphasises the fact I was a relatively successful trader, given that stocks I made the most trades in gave the highest return and vice versa.
   
3. Vis 5 shows I was more hesitant to sell at a loss early in my trading journey than later on. The idea of this visualisation was to build on the classic candlestick graph synonymous with trading while also suitably visualising my trading performance in another way other than profit/loss. The problem with using profit/loss is that these values only occur in sell executions. As a result, we looked to the general traders’ ideology of “Buy low, Sell high”. For a buy order to be considered successful on a given day, it must be in the lower 50% of the candlestick for that day and vice versa for a sell order. This chart is very good at illustrating good and bad plays over a long trading period. Statistically, overall, my traders were successful 46% of the time for tesla stock, with my successful trader increasing over the year. Looking at Tesla, trading activity increased, and I cut losses quicker. 

{|insights)}

{(designJustification|}

## Layout

V1-2 aimed to understand how my volume of trades varied over time. The number of trades is encoded by the size of circle marks and linked to a slider that selects periods of an hour. Profit/Loss is encoded by colour of marks. One issue of encoding size in this way is that some users may misinterpret the values each mark represents. Some may understand the markers as half past each hour. Using a heatmap instead, as shown in the mockups resolves this issue but limit the amount of information portrayed. As a result, in this visualisation, tooltips help the viewer explore what is being presented. It was a design choice to set a minimum size for the circle marks simply because a smaller size meant the marks were difficult to discern. 

The choice of bubble chart allowed showing four dimensions at once; the number of trades encoded by the size of bubbles, profit/loss encoded by the colour of the bubble and the day of the week and time of day on the X and Y-axis. A conventional alternative would be a heatmap. The benefit of a heatmap is that colour is simpler to distinguish than size.

Candlestick Chart visualisations provides daily opening, highest, lowest and closing prices. A stick encodes the information for each day. Two colours are used to distinguish positive and negative price trends. The user can easily estimate the development of the data by looking at the colours of the sticks. Superimposing volume and trades on chart over juxtaposition allows for smaller occupancy of display space. Granted, a simple line chart of the closing price would suffice in showing performance over time, but these graphs lack vital information.

All visualisations with a time dimension encoded on the x-axis as this is common and intuitive for the viewer.[4] Generally, independent variables are encoded to the x-axis and dependent variables are encoded to the y axis. Most financial charts are a variation of a simple time-series line graph showing the time on the X-axis, the price (or return) on the Y-axis, and other indicators as a subplot with the same x-axis. For a single asset, it's an effective way to show multiple dimensions in a compact view. 

## Colour

Usually, financial data is displayed in a green/red colour scheme, with green being positive and red being negative. However, this colour scheme is not very inclusive as many people are red-green colour blind and therefore find it harder to distinguish the colours, especially in the treemaps and bubble plot where shading is used. As a result, I opted for a red/blue diverging colour scale. This scheme is still generally well known as ‘red is bad, blue is good’ so I do not loss that enate feature of the green/red scheme but has the added benefit of being colour blind safe. However, at first glance people many think these visualisations were about republicans/democrats’ elections. The midpoint of the domain was set to 0 to avoid mispresenting the data.

## Interaction

Having the option to disaggregate the data through user interaction allows users to compare time periods to the whole dataset easily. More analytic operations can be performed through demonstration, which interaction through time allows. Interactivity engages users in visualisation construction and data analysis processes which foster cognitive activities of understanding data and sensemaking. Especially in a dataset like this using only aggregated data can hide a lot of important information. A trader’s mindset between making a sell or buy execution is very different so it is vital wherever possible to allow for the separation of this data to be visualised to provide further insight.  Multiple aggregations are used throughout the visualisations including order type, stock and date.

{|designJustification)}

{(validation|}

1.  My first idea to show seasonality was to do a heatmap. Heatmaps are very popular for showing magnitude of a phenomenon as colour but have some limitations. Colour hue is the only differentiator so it limits the amount of information that can be shown. By using the bubble plot, we can convey more information at one time. Leveraging another visual variable, such as size, supports more comparisons over multiple dimensions.
2.  The accurate representation of data is essential in communication. However, colour maps such as rainbow and red–green that visually distort data through uneven colour gradients or are unreadable to those with colour-vision deficiency remain prevalent in data vis. Rainbow colourmaps produce visually appealing graphs, but they often introduce arbitrary distinctions and distort importance disparities in ways that have led to a slew of erroneous assumptions. More detailed perspectives into seamlessly varying datasets can be gained by using a sequential/divergent colour map. People often visually group colours of the same name, such as shades of blue. In practice, this grouping makes rainbows helpful in visualising categorical data. Using rainbows for continuous values, on the other hand, incorporates imaginary distinctions of data that is smoothly varying. A considerable portion of the viewers would be unable to interpret colour maps that contain both red and green colours of equal lightness. According to general estimates, 0.5% of women and 8% of men worldwide have a colour perception deficit.[9]

3.  Purely animated data can leave people blind to important changes. People can only differentiate between a few different speeds and motion directions[2], and they can only track the movement of three or four data points at a time[3]. Because of our poor abilities to detect moving objects, describing data using motion can only help us recognise a few high-level patterns with little understanding of what they mean. As a result, animation speed was reduced to a stepwise presentation pace over a movie-like continuous presentation making it easier to comprehend the information presented. <br />
   Tversky's review of animation[6] claims that no good benefits of animation could be seen based on the educational literature existing at the time. She also suggests that interactivity may be a way to get around the issues of animations. Users can form more cohesive mental models because they can actively monitor the animations rather than passively watching them. This is definitely true with Vis Being able to control the animation made it easier to discern outlier weeks and the general increasing trading activity. In a thorough study[7], Lowe addressed this issue. He found that users did take advantage of this were necessary. <br />
   Although it was acknowledged that animation could be perplexing and that it was not a suitable technique for every person, the findings of the assessment studies[8] revealed that the animation was often presented as entertaining, thrilling, and very useful for interpreting time-oriented data and seeing what was going on in the data. Archambault et al.[5] found that the usage of a slider helped analyse trends in the data faster. Further emphasising the importance of sliders.

{|validation)}

## Critical Reflection

Personally, I would use green/red but give the option for red/blue just like how dark theme options are becoming more popular in web design. Could add the option to change the colour of the graphs. Inclusivity is important in all aspects of life and needs to be in the forefront of design.<br />
Although superposition allows for less display space and can make it easier for users to see patterns between charts better, superposition may be too cluttered given the amount of data on the figure. I tried to alleviate this by changing the transparency of the volume bar chart, but it may be more appropriate to have volume juxtaposed instead under the candlestick chart. Other issues with Vis 5 include the fact that the tooltip will only display one of each marker. This is a limitation of plotly. Also, it is hard to distinguish multiple trades on the same day on the chart even when zoomed in.<br />
When reflecting on using the Bubble plot over Heatmap, one con of using the bubble is that it is harder to differentiate activity by size than using colour hue. But ultimately bubble plot allows for more information. Furthermore, the bubble plot can be misinterpreted. A bigger bubble marker suggests they represent half-past to half-past instead of hourly. <br />
In conclusion, the developed data visualisation allows me to answer the research questions, but it could still be improving the ability to explore the complete dataset, not only the aggregated results. 

{(references|}

[1]	Lee, W.Y., Jiang, C.X. and Indro, D.C., 2002. Stock market volatility, excess returns, and the role of investor sentiment. Journal of banking & Finance, 26(12), pp.2277-2299.
[2]	Ball, K. and Sekuler, R., 1982. A specific and enduring improvement in visual motion discrimination. Science, 218(4573), pp.697-698.
[3]	Franconeri, S.L., Jonathan, S.V. and Scimeca, J.M., 2010. Tracking multiple objects is limited only by object spacing, not by speed, time, or capacity. Psychological science, 21(7), pp.920-925.
[4]	Aigner, W., Miksch, S., Schumann, H. and Tominski, C., 2011. Visualisation of time-oriented data. Springer Science & Business Media.
[5]	Archambault, D., Purchase, H. and Pinaud, B., 2010. Animation, small multiples, and the effect of mental map preservation in dynamic graphs. IEEE transactions on visualisation and computer graphics, 17(4), pp.539-552.
[6]	Tversky, B., Morrison, J.B. and Betrancourt, M., 2002. Animation: can it facilitate?. International journal of human-computer studies, 57(4), pp.247-262.
[7]	Lowe, R., 2008. Learning from animation: Where to look, when to look. In Learning with animation: Research implications for design (pp. 49-68). Cambridge University Press.
[8]	Kriglstein, S., Pohl, M. and Stachl, C., 2012, July. Animation for time-oriented data: An overview of empirical research. In 2012 16th International Conference on Information Visualisation (pp. 30-35). IEEE.
[9]	Crameri, F., Shephard, G.E. and Heron, P.J., 2020. The misuse of colour in science communication. Nature communications, 11(1), pp.1-10.


{|references)}

