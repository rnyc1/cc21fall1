# (PART) Tutorials {-}

# Tutorial for six basic graphs by using ggplot

Wenxin Zhang (wz2585)




```r
library(ggplot2)
```

There are two parts of this tutorial, the first part is the cheat sheet for some basic R functions. It includes some basic functions we will use when doing the R project. These functions include read in data, output data, plot
graphs by using basic and ggplot2 ways and other basic functions. It can be found as ‘cheatsheet.pdf’ in the web address below:
https://github.com/Annie9837/EDAV_CC/blob/main/cheatsheet.pdf


The second part is the tutorial for how to use ggplot. Because there are different types of plots that can be used to do the data analysis, however, a suitable choice can have a better visualization, thus give us a better understanding of our data. Therefore, before we select the method to do the visualization, we need to consider what we want to see about our data. For example, whether we want to see the summary, frequency, distribution of a variable or the relationship between two features. Different purpose will lead to different choice of plot. In this tutorial, I want to introduce six basic choice of graphs.

We are going to use the 'BigMart Sales Data' to explain the graphs.
Data source: https://www.kaggle.com/brijbhushannanda1979/bigmart-sales-data

```r
# read in data
df <- readr::read_csv("https://storage.googleapis.com/kagglesdsdata/datasets/9961/14084/Train.csv?X-Goog-Algorithm=GOOG4-RSA-SHA256&X-Goog-Credential=gcp-kaggle-com%40kaggle-161607.iam.gserviceaccount.com%2F20211102%2Fauto%2Fstorage%2Fgoog4_request&X-Goog-Date=20211102T150038Z&X-Goog-Expires=259199&X-Goog-SignedHeaders=host&X-Goog-Signature=22b981f7d24fe5629802b5c5556955d7609a9bd1a5d12171320b9cc76662012a43e69dbd6d62fd199765e8168948168d7c793bd7f6006efd1b51dfc816649b80f24dc88d5e70f20a60cf7f6a5580093b62f7fb7648c532b791f97019fc2a726fb543de575ba7811afa656c2c59a3516dc90d4afc601292d5fc7125dca38d612d928f6766f1ea89d17e0ee261cd83fb4aa2fff24aa796db172b2a90c4e9b8cac6619f56f831e016221a028810a4442a69bc393c46a3a25b4ac9e6bbf89f80c1c01a4b873bd484bc7c7ea06d478985495c762eaea3226a2e0968a6e9ccf7ae22db64d8def323425dc785f2478d4c0e05322275cabe3f987b60fdd3f99d08f1eec6")
```

## Scatter Plot
Generally, scatter plot is used to show the relationship between two continuous variables. 

1. First, we want to see the relationship between variables Item_Visibility (The % of total display area of all products in a store allocated to the particular product) and Item_MRP (Maximum Retail Price (list price) of the product).

```r
ggplot(df,aes(Item_Visibility, Item_MRP))+
  geom_point()+
  theme_bw()
```

We did not see the particular pattern between these two variables, but we do can see that for most of products, no matter what prices they have, Item_Visibility is about (0, 0.2).

2. Now, we want to use color in the plot to show the distribution of different types of product. Instead to just show them in the same graph, we want separate graphs for each type of product. Then we can see the relationship between Item_Visibility and Item_MRP of each type of product by using the facet function: facet_wrap.

```r
ggplot(df,aes(Item_Visibility, Item_MRP))+
  geom_point(aes(color =Item_Type))+
  theme_bw()+
  labs(title="Scatterplot")+
  facet_wrap( ~ Item_Type)
```



## Histogram
Histogram graph is used to count the frequency of a variable in a particular range. It can be used for both continuous and discrete variables. For discrete variable, the frequency is plotted for each level of the variable. For continuous variable, R will separate data into different ranges and show the frequencies of each range. We can set these ranges manually, and thus, by changing the number of bins, we can find the most suitable number of bins to show the distribution of the continuous variable.

```r
# Plot for discrete variable
ggplot(df,aes(Item_Fat_Content))+
  geom_histogram(stat="count")+
  labs(title = "Histogram for discrete variable")


# Plot for continuous variable
ggplot(df,aes(Item_Outlet_Sales))+
  geom_histogram(bins = 30, color="black", fill="grey")+
  labs(title = "Histogram for continuous variable")
```


We can also add a density curve on the graph. The density curve can show us the distribution of our variable more clearly. For the graph below, we can tell that the distribution of Item_Outlet_Sales is right skewed. Note that the scale on y is changed from count to a percentage. The total area under the density curve is equal to 1.


```r
ggplot(df,aes(Item_Outlet_Sales))+
  geom_histogram(aes(y=..density..),bins = 30, color="black", fill="grey")+
  geom_density(color="red", adjust=2, size = 1)+
  labs(title = "Histogram with density curve")
```


## Bar chart

Bar chart always used when we want to plot for categorical data. It presents with rectangular bars with heights or lengths proportional to the values that they represent.


```r
ggplot(df,aes(Item_Type))+
  geom_bar(fill = "red")+
  coord_flip()+
  labs(title = "BarChart")+
  theme_gray()
```

Then, from the graph above, we can see that type Fruit and vegetables has the highest percentage and seafood has the lowest percentage from all types. 

We can also plot the Stacked Bar chart to see the count values across to two categorical variables. Each bar in a standard bar chart is divided into a number of sub-bars stacked end to end, each one corresponding to a level of the second categorical variable. Such as the graph shows below. We plot the standard bar chart for Outlet_Location_Type, but also divide each level of Outlet_Location_Type into different parts based on the levels of Outlet_Type. In the graph below, we can see that in Tier 2, there is only Supermarket Type 1, and for Tire 3, it contains 4 different types of supermarket. However, since for each part of the single rectangle, they are not in the same horizontal line, so it is hard to compare the frequency of them. So if we care more about comparing the frequency between different sub categories, then group bar chart may be a better choice.

```r
ggplot(df,aes(Outlet_Location_Type, fill = Outlet_Type))+
  geom_bar()+
  labs(title ="Stacked Bar Chart", x = "Outlet Location Type", y="Count of Outlets")
```

## Box Plot

Generally, box plot is used for the combination of numerical and categorical variables. It shows the five-number summary of a set of data: including the minimum score, first (lower) quartile, median, third (upper) quartile, and maximum score. It also indicates the outliers if the point is 1.5 times above or below the max or min value. It visually shows the distribution of numerical data and skewness through displaying the data quartiles (or percentiles) and averages.


```r
ggplot(df,aes(x=reorder(Outlet_Identifier, Item_Outlet_Sales,median),y=Item_Outlet_Sales))+
  geom_boxplot(orientation = "x", outlier.color = "red", outlier.size = 1.75)+
  coord_flip() +
  labs(title = "BoxPlot", x = "Outlet Identifier")
```

In the above graph, we plot box plot for each level of Outlet Identifier to see how Item_Outlet_Sales distrobuted in each outlet. We also ordered the outlet based on the median value of sales. The graph shows us the minimum, lower quartile, median, upper quartile and maximum value for each outlet's sales amount, and use the red point to indicate the outliers in each outlet. So that we can compare the sales between different outlet quickly.


## Area Chart

Area chart combines the line chart and bar chart to show how one or more groups’ numeric values change over the progression of a second variable, typically that of time. Which means it is useful for visualizing one or more variables over time. In the graph below, it shows how the sales amount of items in outlet changes as times goes by.

```r
ggplot(df,aes(Item_Outlet_Sales))+
  geom_area(stat = "bin", bins =30, fill ="steelblue")+
  labs(title = "Area Chart", x = "Item Outlet Sales", y ="Count")
```

## Heat map
Heat map is a two-dimensional representation of data in which values are represented by colors. It uses color to communicate relationships between data values that would be much harder to understand if presented numerically. Therefore, it is useful when we want to see the relationship between two or three variables. 

In the graph below, it shows the relationship between Outlet_Identifier, Item_Type and Item_MRP. Each rectangle in the graph represent a specific type of item in one specific outlet. The color of each small rectangle represents the maximum Retail Price (list price) of this type of product in that outlet. In this example, the lighter the color, the higher the price will be. 


```r
ggplot(df, aes(Outlet_Identifier,Item_Type))+
  geom_raster(aes(fill = Item_MRP))+
  labs(title ="Heat Map", x ="Outlet Identifier", y = "Item Type")+
  scale_fill_continuous(name = "ItemMRP")
```


