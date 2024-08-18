---
title: "Lung Cancer Data || EDA and Data Visualization || R"
description: |
  Explored and visualized lungs data in R, performing EDA to uncover patterns and trends. Utilized R packages for effective data visualization, providing insights into respiratory health indicators.
author:
  - name: Prasanth Kumar
    url: https://www.linkedin.com/in/prasanthkumar28/
date: 08-17-2024
output:
  distill::distill_article:
    self_contained: false
    df_print: kable
---


## Setup Chunks in RMarkdown



## Loading Required Libraries

 Loading several essential libraries, including "tidyverse", "highcharter". These libraries offer diverse functionalities for data manipulation, visualization, and reporting.

 And also we need the "survival" package for the NCCTG Lung Cancer data set in it.
 
<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='kw'><a href='https://rdrr.io/r/base/library.html'>library</a></span><span class='op'>(</span><span class='va'><a href='https://tidyverse.tidyverse.org'>tidyverse</a></span><span class='op'>)</span></span>
<span><span class='kw'><a href='https://rdrr.io/r/base/library.html'>library</a></span><span class='op'>(</span><span class='va'><a href='https://jkunst.com/highcharter/'>highcharter</a></span><span class='op'>)</span></span>
<span><span class='kw'><a href='https://rdrr.io/r/base/library.html'>library</a></span><span class='op'>(</span><span class='va'><a href='https://github.com/therneau/survival'>survival</a></span><span class='op'>)</span></span>
<span><span class='kw'><a href='https://rdrr.io/r/base/library.html'>library</a></span><span class='op'>(</span><span class='va'><a href='https://yihui.org/knitr/'>knitr</a></span><span class='op'>)</span></span></code></pre></div>

</div>


## Data Description 

### NCCTG Lung Cancer Data

Survival in patients with advanced lung cancer from the North Central Cancer Treatment Group. Performance scores rate how well the patient can perform usual daily activities.


### Format 

inst:	Institution code.  
time:	Survival time in days.  
status:	censoring status 1=censored, 2=dead.  
age:	Age in years.  
sex:	Male=1 Female=2.  
ph.ecog:	ECOG performance score as rated by the physician. 0=asymptomatic, 1= symptomatic but completely ambulatory, 2= in bed <50% of the day, 3= in bed > 50% of the day but not bedbound, 4 = bedbound.  
ph.karno:	Karnofsky performance score (bad=0-good=100) rated by physician.    
pat.karno:	Karnofsky performance score as rated by patient.    
meal.cal:	Calories consumed at meals.  
wt.loss:	Weight loss in last six months (pounds).  

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='va'>lung_data</span> <span class='op'>&lt;-</span> <span class='va'>lung</span> </span>
<span><span class='fu'><a href='https://rdrr.io/pkg/knitr/man/kable.html'>kable</a></span><span class='op'>(</span><span class='fu'><a href='https://rdrr.io/r/utils/head.html'>head</a></span><span class='op'>(</span><span class='va'>lung_data</span><span class='op'>)</span><span class='op'>)</span></span></code></pre></div>


| inst| time| status| age| sex| ph.ecog| ph.karno| pat.karno| meal.cal| wt.loss|
|----:|----:|------:|---:|---:|-------:|--------:|---------:|--------:|-------:|
|    3|  306|      2|  74|   1|       1|       90|       100|     1175|      NA|
|    3|  455|      2|  68|   1|       0|       90|        90|     1225|      15|
|    3| 1010|      1|  56|   1|       0|       90|        90|       NA|      15|
|    5|  210|      2|  57|   1|       1|       90|        60|     1150|      11|
|    1|  883|      2|  60|   1|       0|      100|        90|       NA|       0|
|   12| 1022|      1|  74|   1|       1|       50|        80|      513|       0|

</div>


#### Dimensions

The dataset consists of 228 rows and 10 columns.

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='fu'><a href='https://rdrr.io/r/base/dim.html'>dim</a></span><span class='op'>(</span><span class='va'>lung_data</span><span class='op'>)</span></span></code></pre></div>

```
[1] 228  10
```

</div>


#### Names in the dataset

The names of the columns in the dataset are:

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='fu'><a href='https://rdrr.io/r/base/names.html'>names</a></span><span class='op'>(</span><span class='va'>lung_data</span><span class='op'>)</span></span></code></pre></div>

```
 [1] "inst"      "time"      "status"    "age"       "sex"      
 [6] "ph.ecog"   "ph.karno"  "pat.karno" "meal.cal"  "wt.loss"  
```

</div>


#### Data Types

The data types of the columns are as follows: 

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='fu'><a href='https://rdrr.io/r/base/lapply.html'>sapply</a></span><span class='op'>(</span><span class='va'>lung_data</span>, <span class='va'>typeof</span><span class='op'>)</span></span></code></pre></div>

```
     inst      time    status       age       sex   ph.ecog  ph.karno 
 "double"  "double"  "double"  "double"  "double"  "double"  "double" 
pat.karno  meal.cal   wt.loss 
 "double"  "double"  "double" 
```

</div>


#### Data Summary
  
A summary of the dataset provides descriptive statistics for numerical variables, including measures such as mean, median, minimum, maximum, and quartiles. For categorical variables, it displays their class, length, and mode. This overview helps understand the central tendencies and distributions of numerical data, as well as the characteristics of categorical variables.    

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='fu'><a href='https://rdrr.io/r/base/summary.html'>summary</a></span><span class='op'>(</span><span class='va'>lung_data</span><span class='op'>)</span></span></code></pre></div>

```
      inst            time            status           age       
 Min.   : 1.00   Min.   :   5.0   Min.   :1.000   Min.   :39.00  
 1st Qu.: 3.00   1st Qu.: 166.8   1st Qu.:1.000   1st Qu.:56.00  
 Median :11.00   Median : 255.5   Median :2.000   Median :63.00  
 Mean   :11.09   Mean   : 305.2   Mean   :1.724   Mean   :62.45  
 3rd Qu.:16.00   3rd Qu.: 396.5   3rd Qu.:2.000   3rd Qu.:69.00  
 Max.   :33.00   Max.   :1022.0   Max.   :2.000   Max.   :82.00  
 NA's   :1                                                       
      sex           ph.ecog          ph.karno        pat.karno     
 Min.   :1.000   Min.   :0.0000   Min.   : 50.00   Min.   : 30.00  
 1st Qu.:1.000   1st Qu.:0.0000   1st Qu.: 75.00   1st Qu.: 70.00  
 Median :1.000   Median :1.0000   Median : 80.00   Median : 80.00  
 Mean   :1.395   Mean   :0.9515   Mean   : 81.94   Mean   : 79.96  
 3rd Qu.:2.000   3rd Qu.:1.0000   3rd Qu.: 90.00   3rd Qu.: 90.00  
 Max.   :2.000   Max.   :3.0000   Max.   :100.00   Max.   :100.00  
                 NA's   :1        NA's   :1        NA's   :3       
    meal.cal         wt.loss       
 Min.   :  96.0   Min.   :-24.000  
 1st Qu.: 635.0   1st Qu.:  0.000  
 Median : 975.0   Median :  7.000  
 Mean   : 928.8   Mean   :  9.832  
 3rd Qu.:1150.0   3rd Qu.: 15.750  
 Max.   :2600.0   Max.   : 68.000  
 NA's   :47       NA's   :14       
```

</div>


#### Missing Values

The dataset contains missing values in columns:

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='fu'><a href='https://rdrr.io/r/base/colSums.html'>colSums</a></span><span class='op'>(</span><span class='fu'><a href='https://rdrr.io/r/base/NA.html'>is.na</a></span><span class='op'>(</span><span class='va'>lung_data</span><span class='op'>)</span><span class='op'>)</span></span></code></pre></div>

```
     inst      time    status       age       sex   ph.ecog  ph.karno 
        1         0         0         0         0         1         1 
pat.karno  meal.cal   wt.loss 
        3        47        14 
```

</div>

 
 The Lung data set has some missing values in it.
 
## Data Cleaning
 
#### Removing NA in the Dataset

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='co'># Remove rows with NA values</span></span>
<span><span class='va'>lung_data</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://rdrr.io/r/stats/na.fail.html'>na.omit</a></span><span class='op'>(</span><span class='va'>lung_data</span><span class='op'>)</span></span>
<span></span>
<span><span class='fu'><a href='https://rdrr.io/r/base/colSums.html'>colSums</a></span><span class='op'>(</span><span class='fu'><a href='https://rdrr.io/r/base/NA.html'>is.na</a></span><span class='op'>(</span><span class='va'>lung_data</span><span class='op'>)</span><span class='op'>)</span></span></code></pre></div>

```
     inst      time    status       age       sex   ph.ecog  ph.karno 
        0         0         0         0         0         0         0 
pat.karno  meal.cal   wt.loss 
        0         0         0 
```

</div>



### Quick Summary

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='fu'><a href='https://rdrr.io/r/base/summary.html'>summary</a></span><span class='op'>(</span><span class='va'>lung_data</span><span class='op'>)</span></span></code></pre></div>

```
      inst            time            status           age       
 Min.   : 1.00   Min.   :   5.0   Min.   :1.000   Min.   :39.00  
 1st Qu.: 3.00   1st Qu.: 174.5   1st Qu.:1.000   1st Qu.:57.00  
 Median :11.00   Median : 268.0   Median :2.000   Median :64.00  
 Mean   :10.71   Mean   : 309.9   Mean   :1.719   Mean   :62.57  
 3rd Qu.:15.00   3rd Qu.: 419.5   3rd Qu.:2.000   3rd Qu.:70.00  
 Max.   :32.00   Max.   :1022.0   Max.   :2.000   Max.   :82.00  
      sex           ph.ecog          ph.karno        pat.karno     
 Min.   :1.000   Min.   :0.0000   Min.   : 50.00   Min.   : 30.00  
 1st Qu.:1.000   1st Qu.:0.0000   1st Qu.: 70.00   1st Qu.: 70.00  
 Median :1.000   Median :1.0000   Median : 80.00   Median : 80.00  
 Mean   :1.383   Mean   :0.9581   Mean   : 82.04   Mean   : 79.58  
 3rd Qu.:2.000   3rd Qu.:1.0000   3rd Qu.: 90.00   3rd Qu.: 90.00  
 Max.   :2.000   Max.   :3.0000   Max.   :100.00   Max.   :100.00  
    meal.cal         wt.loss       
 Min.   :  96.0   Min.   :-24.000  
 1st Qu.: 619.0   1st Qu.:  0.000  
 Median : 975.0   Median :  7.000  
 Mean   : 929.1   Mean   :  9.719  
 3rd Qu.:1162.5   3rd Qu.: 15.000  
 Max.   :2600.0   Max.   : 68.000  
```

</div>


### Identifying Unique Values

To understand the diversity and uniqueness of data, we will analyze the dataset to identify all unique values across various columns. This process will provide insights into the distinct categories and variations present in the dataset.

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='fu'><a href='https://rdrr.io/r/base/lapply.html'>sapply</a></span><span class='op'>(</span><span class='va'>lung_data</span>, <span class='kw'>function</span><span class='op'>(</span><span class='va'>x</span><span class='op'>)</span> <span class='fu'><a href='https://rdrr.io/r/base/length.html'>length</a></span><span class='op'>(</span><span class='fu'><a href='https://rdrr.io/r/base/unique.html'>unique</a></span><span class='op'>(</span><span class='va'>x</span><span class='op'>)</span><span class='op'>)</span><span class='op'>)</span></span></code></pre></div>

```
     inst      time    status       age       sex   ph.ecog  ph.karno 
       17       149         2        39         2         4         6 
pat.karno  meal.cal   wt.loss 
        8        58        46 
```

</div>


## Data Visualization

#### Distribution of Sex - Bar Chart

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='va'>lung_data</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://dplyr.tidyverse.org/reference/mutate.html'>mutate</a></span><span class='op'>(</span>sex <span class='op'>=</span> <span class='fu'><a href='https://rdrr.io/r/base/ifelse.html'>ifelse</a></span><span class='op'>(</span><span class='va'>sex</span> <span class='op'>==</span> <span class='fl'>1</span>, <span class='st'>"Male"</span>, <span class='st'>"Female"</span><span class='op'>)</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://dplyr.tidyverse.org/reference/count.html'>count</a></span><span class='op'>(</span><span class='va'>sex</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://jkunst.com/highcharter/reference/hchart.html'>hchart</a></span><span class='op'>(</span><span class='st'>"pie"</span>, <span class='fu'><a href='https://jkunst.com/highcharter/reference/hcaes.html'>hcaes</a></span><span class='op'>(</span>x <span class='op'>=</span> <span class='st'>"sex"</span>, y <span class='op'>=</span> <span class='st'>"n"</span><span class='op'>)</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://jkunst.com/highcharter/reference/hc_title.html'>hc_title</a></span><span class='op'>(</span>text <span class='op'>=</span> <span class='st'>"Distribution of Sex"</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='fu'><a href='https://jkunst.com/highcharter/reference/hc_colors.html'>hc_colors</a></span><span class='op'>(</span>colors <span class='op'>=</span> <span class='fu'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='op'>(</span><span class='st'>"#1f77b4"</span>, <span class='st'>"#ff7f0e"</span><span class='op'>)</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://jkunst.com/highcharter/reference/hc_add_theme.html'>hc_add_theme</a></span><span class='op'>(</span><span class='fu'><a href='https://jkunst.com/highcharter/reference/hc_theme_google.html'>hc_theme_google</a></span><span class='op'>(</span><span class='op'>)</span><span class='op'>)</span></span></code></pre></div>

```{=html}
<div class="highchart html-widget html-fill-item" id="htmlwidget-9f0f9cc9ae6bbe28f32a" style="width:624px;height:384px;"></div>
<script type="application/json" data-for="htmlwidget-9f0f9cc9ae6bbe28f32a">{"x":{"hc_opts":{"chart":{"reflow":true},"title":{"text":"Distribution of Sex"},"yAxis":{"title":{"text":"n"},"type":"linear"},"credits":{"enabled":false},"exporting":{"enabled":false},"boost":{"enabled":false},"plotOptions":{"series":{"label":{"enabled":false},"turboThreshold":0,"showInLegend":false},"treemap":{"layoutAlgorithm":"squarified"},"scatter":{"marker":{"symbol":"circle"}}},"series":[{"group":"group","data":[{"sex":"Female","n":64,"y":64,"name":"Female"},{"sex":"Male","n":103,"y":103,"name":"Male"}],"type":"pie"}],"xAxis":{"type":"category","title":{"text":"sex"},"categories":null},"colors":["#1f77b4","#ff7f0e"]},"theme":{"colors":["#0266C8","#F90101","#F2B50F","#00933B"],"chart":{"style":{"fontFamily":"Roboto","color":"#444444"}},"xAxis":{"gridLineWidth":1,"gridLineColor":"#F3F3F3","lineColor":"#F3F3F3","minorGridLineColor":"#F3F3F3","tickColor":"#F3F3F3","tickWidth":1},"yAxis":{"gridLineColor":"#F3F3F3","lineColor":"#F3F3F3","minorGridLineColor":"#F3F3F3","tickColor":"#F3F3F3","tickWidth":1},"legendBackgroundColor":"rgba(0, 0, 0, 0.5)","background2":"#505053","dataLabelsColor":"#B0B0B3","textColor":"#C0C0C0","contrastTextColor":"#F0F0F3","maskColor":"rgba(255,255,255,0.3)"},"conf_opts":{"global":{"Date":null,"VMLRadialGradientURL":"http =//code.highcharts.com/list(version)/gfx/vml-radial-gradient.png","canvasToolsURL":"http =//code.highcharts.com/list(version)/modules/canvas-tools.js","getTimezoneOffset":null,"timezoneOffset":0,"useUTC":true},"lang":{"contextButtonTitle":"Chart context menu","decimalPoint":".","downloadCSV":"Download CSV","downloadJPEG":"Download JPEG image","downloadPDF":"Download PDF document","downloadPNG":"Download PNG image","downloadSVG":"Download SVG vector image","downloadXLS":"Download XLS","drillUpText":"◁ Back to {series.name}","exitFullscreen":"Exit from full screen","exportData":{"annotationHeader":"Annotations","categoryDatetimeHeader":"DateTime","categoryHeader":"Category"},"hideData":"Hide data table","invalidDate":null,"loading":"Loading...","months":["January","February","March","April","May","June","July","August","September","October","November","December"],"noData":"No data to display","numericSymbolMagnitude":1000,"numericSymbols":["k","M","G","T","P","E"],"printChart":"Print chart","resetZoom":"Reset zoom","resetZoomTitle":"Reset zoom level 1:1","shortMonths":["Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"],"shortWeekdays":["Sat","Sun","Mon","Tue","Wed","Thu","Fri"],"thousandsSep":" ","viewData":"View data table","viewFullscreen":"View in full screen","weekdays":["Sunday","Monday","Tuesday","Wednesday","Thursday","Friday","Saturday"]}},"type":"chart","fonts":"Roboto","debug":false},"evals":[],"jsHooks":[]}</script>
```

</div>


#### Distribution of Status

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='va'>lung_data</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://dplyr.tidyverse.org/reference/mutate.html'>mutate</a></span><span class='op'>(</span>status <span class='op'>=</span> <span class='fu'><a href='https://rdrr.io/r/base/ifelse.html'>ifelse</a></span><span class='op'>(</span><span class='va'>status</span> <span class='op'>==</span> <span class='fl'>2</span>, <span class='st'>"Dead"</span>, <span class='st'>"Censored"</span><span class='op'>)</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://dplyr.tidyverse.org/reference/count.html'>count</a></span><span class='op'>(</span><span class='va'>status</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://jkunst.com/highcharter/reference/hchart.html'>hchart</a></span><span class='op'>(</span><span class='st'>"pie"</span>, <span class='fu'><a href='https://jkunst.com/highcharter/reference/hcaes.html'>hcaes</a></span><span class='op'>(</span>x <span class='op'>=</span> <span class='st'>"status"</span>, y <span class='op'>=</span> <span class='st'>"n"</span><span class='op'>)</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://jkunst.com/highcharter/reference/hc_title.html'>hc_title</a></span><span class='op'>(</span>text <span class='op'>=</span> <span class='st'>"Distribution of Status"</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='fu'><a href='https://jkunst.com/highcharter/reference/hc_colors.html'>hc_colors</a></span><span class='op'>(</span>colors <span class='op'>=</span> <span class='fu'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='op'>(</span><span class='st'>"seagreen"</span>,<span class='st'>"maroon"</span><span class='op'>)</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://jkunst.com/highcharter/reference/hc_add_theme.html'>hc_add_theme</a></span><span class='op'>(</span><span class='fu'><a href='https://jkunst.com/highcharter/reference/hc_theme_google.html'>hc_theme_google</a></span><span class='op'>(</span><span class='op'>)</span><span class='op'>)</span></span></code></pre></div>

```{=html}
<div class="highchart html-widget html-fill-item" id="htmlwidget-6503808017bf5b7b9c01" style="width:624px;height:384px;"></div>
<script type="application/json" data-for="htmlwidget-6503808017bf5b7b9c01">{"x":{"hc_opts":{"chart":{"reflow":true},"title":{"text":"Distribution of Status"},"yAxis":{"title":{"text":"n"},"type":"linear"},"credits":{"enabled":false},"exporting":{"enabled":false},"boost":{"enabled":false},"plotOptions":{"series":{"label":{"enabled":false},"turboThreshold":0,"showInLegend":false},"treemap":{"layoutAlgorithm":"squarified"},"scatter":{"marker":{"symbol":"circle"}}},"series":[{"group":"group","data":[{"status":"Censored","n":47,"y":47,"name":"Censored"},{"status":"Dead","n":120,"y":120,"name":"Dead"}],"type":"pie"}],"xAxis":{"type":"category","title":{"text":"status"},"categories":null},"colors":["seagreen","maroon"]},"theme":{"colors":["#0266C8","#F90101","#F2B50F","#00933B"],"chart":{"style":{"fontFamily":"Roboto","color":"#444444"}},"xAxis":{"gridLineWidth":1,"gridLineColor":"#F3F3F3","lineColor":"#F3F3F3","minorGridLineColor":"#F3F3F3","tickColor":"#F3F3F3","tickWidth":1},"yAxis":{"gridLineColor":"#F3F3F3","lineColor":"#F3F3F3","minorGridLineColor":"#F3F3F3","tickColor":"#F3F3F3","tickWidth":1},"legendBackgroundColor":"rgba(0, 0, 0, 0.5)","background2":"#505053","dataLabelsColor":"#B0B0B3","textColor":"#C0C0C0","contrastTextColor":"#F0F0F3","maskColor":"rgba(255,255,255,0.3)"},"conf_opts":{"global":{"Date":null,"VMLRadialGradientURL":"http =//code.highcharts.com/list(version)/gfx/vml-radial-gradient.png","canvasToolsURL":"http =//code.highcharts.com/list(version)/modules/canvas-tools.js","getTimezoneOffset":null,"timezoneOffset":0,"useUTC":true},"lang":{"contextButtonTitle":"Chart context menu","decimalPoint":".","downloadCSV":"Download CSV","downloadJPEG":"Download JPEG image","downloadPDF":"Download PDF document","downloadPNG":"Download PNG image","downloadSVG":"Download SVG vector image","downloadXLS":"Download XLS","drillUpText":"◁ Back to {series.name}","exitFullscreen":"Exit from full screen","exportData":{"annotationHeader":"Annotations","categoryDatetimeHeader":"DateTime","categoryHeader":"Category"},"hideData":"Hide data table","invalidDate":null,"loading":"Loading...","months":["January","February","March","April","May","June","July","August","September","October","November","December"],"noData":"No data to display","numericSymbolMagnitude":1000,"numericSymbols":["k","M","G","T","P","E"],"printChart":"Print chart","resetZoom":"Reset zoom","resetZoomTitle":"Reset zoom level 1:1","shortMonths":["Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"],"shortWeekdays":["Sat","Sun","Mon","Tue","Wed","Thu","Fri"],"thousandsSep":" ","viewData":"View data table","viewFullscreen":"View in full screen","weekdays":["Sunday","Monday","Tuesday","Wednesday","Thursday","Friday","Saturday"]}},"type":"chart","fonts":"Roboto","debug":false},"evals":[],"jsHooks":[]}</script>
```

</div>


### Distribution of Sex & ECOG Performance Score by Physician

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='va'>lung_data</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='fu'><a href='https://dplyr.tidyverse.org/reference/mutate.html'>mutate</a></span><span class='op'>(</span>sex <span class='op'>=</span> <span class='fu'><a href='https://rdrr.io/r/base/ifelse.html'>ifelse</a></span><span class='op'>(</span><span class='va'>sex</span> <span class='op'>==</span> <span class='fl'>1</span>, <span class='st'>"Male"</span>, <span class='st'>"Female"</span><span class='op'>)</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://dplyr.tidyverse.org/reference/mutate.html'>mutate</a></span><span class='op'>(</span>ph.ecog <span class='op'>=</span> <span class='fu'><a href='https://rdrr.io/r/base/ifelse.html'>ifelse</a></span><span class='op'>(</span><span class='va'>ph.ecog</span> <span class='op'>==</span> <span class='fl'>0</span>, <span class='st'>"Fully active"</span>, <span class='fu'><a href='https://rdrr.io/r/base/ifelse.html'>ifelse</a></span><span class='op'>(</span><span class='va'>ph.ecog</span> <span class='op'>==</span> <span class='fl'>1</span>, <span class='st'>"Light work"</span>, <span class='fu'><a href='https://rdrr.io/r/base/ifelse.html'>ifelse</a></span><span class='op'>(</span><span class='va'>ph.ecog</span> <span class='op'>==</span> <span class='fl'>2</span>, <span class='st'>"Self-care capable"</span>, <span class='fu'><a href='https://rdrr.io/r/base/ifelse.html'>ifelse</a></span><span class='op'>(</span><span class='va'>ph.ecog</span> <span class='op'>==</span> <span class='fl'>3</span>, <span class='st'>"Limited Self-care"</span>, <span class='st'>"Completely disabled"</span><span class='op'>)</span><span class='op'>)</span><span class='op'>)</span><span class='op'>)</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://dplyr.tidyverse.org/reference/count.html'>count</a></span><span class='op'>(</span><span class='va'>sex</span>, <span class='va'>ph.ecog</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span> </span>
<span>  <span class='fu'><a href='https://jkunst.com/highcharter/reference/hchart.html'>hchart</a></span><span class='op'>(</span><span class='st'>"bar"</span>, <span class='fu'><a href='https://jkunst.com/highcharter/reference/hcaes.html'>hcaes</a></span><span class='op'>(</span>x <span class='op'>=</span> <span class='va'>sex</span>, y <span class='op'>=</span> <span class='st'>"n"</span>,</span>
<span>                      group <span class='op'>=</span> <span class='va'>ph.ecog</span><span class='op'>)</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='fu'><a href='https://jkunst.com/highcharter/reference/hc_title.html'>hc_title</a></span><span class='op'>(</span>text <span class='op'>=</span> <span class='st'>"Distribution of Sex &amp; ECOG Performance Score"</span><span class='op'>)</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='fu'><a href='https://jkunst.com/highcharter/reference/hc_add_theme.html'>hc_add_theme</a></span><span class='op'>(</span><span class='fu'><a href='https://jkunst.com/highcharter/reference/hc_theme_ggplot2.html'>hc_theme_ggplot2</a></span><span class='op'>(</span><span class='op'>)</span><span class='op'>)</span></span></code></pre></div>

```{=html}
<div class="highchart html-widget html-fill-item" id="htmlwidget-4928e97ce1799a1cbdfd" style="width:624px;height:384px;"></div>
<script type="application/json" data-for="htmlwidget-4928e97ce1799a1cbdfd">{"x":{"hc_opts":{"chart":{"reflow":true},"title":{"text":"Distribution of Sex & ECOG Performance Score"},"yAxis":{"title":{"text":"n"},"type":"linear"},"credits":{"enabled":false},"exporting":{"enabled":false},"boost":{"enabled":false},"plotOptions":{"series":{"label":{"enabled":false},"turboThreshold":0,"showInLegend":true},"treemap":{"layoutAlgorithm":"squarified"},"scatter":{"marker":{"symbol":"circle"}}},"series":[{"name":"Fully active","data":[{"sex":"Female","ph.ecog":"Fully active","n":19,"y":19,"name":"Female"},{"sex":"Male","ph.ecog":"Fully active","n":28,"y":28,"name":"Male"}],"type":"bar"},{"name":"Light work","data":[{"sex":"Female","ph.ecog":"Light work","n":29,"y":29,"name":"Female"},{"sex":"Male","ph.ecog":"Light work","n":52,"y":52,"name":"Male"}],"type":"bar"},{"name":"Limited Self-care","data":[{"sex":"Male","ph.ecog":"Limited Self-care","n":1,"y":1,"name":"Male"}],"type":"bar"},{"name":"Self-care capable","data":[{"sex":"Female","ph.ecog":"Self-care capable","n":16,"y":16,"name":"Female"},{"sex":"Male","ph.ecog":"Self-care capable","n":22,"y":22,"name":"Male"}],"type":"bar"}],"xAxis":{"type":"category","title":{"text":"sex"},"categories":null}},"theme":{"chart":{"plotBackgroundColor":"#EBEBEB","style":{"color":"#000000","fontFamily":"Arial, sans-serif"}},"colors":["#595959","#F8766D","#A3A500","#00BF7D","#00B0F6","#E76BF3"],"xAxis":{"labels":{"style":{"color":"#666666"}},"title":{"style":{"color":"#000000"}},"startOnTick":false,"endOnTick":false,"gridLineColor":"#FFFFFF","gridLineWidth":1.5,"tickWidth":1.5,"tickLength":5,"tickColor":"#666666","minorTickInterval":0,"minorGridLineColor":"#FFFFFF","minorGridLineWidth":0.5},"yAxis":{"labels":{"style":{"color":"#666666"}},"title":{"style":{"color":"#000000"}},"startOnTick":false,"endOnTick":false,"gridLineColor":"#FFFFFF","gridLineWidth":1.5,"tickWidth":1.5,"tickLength":5,"tickColor":"#666666","minorTickInterval":0,"minorGridLineColor":"#FFFFFF","minorGridLineWidth":0.5},"legendBackgroundColor":"rgba(0, 0, 0, 0.5)","background2":"#505053","dataLabelsColor":"#B0B0B3","textColor":"#C0C0C0","contrastTextColor":"#F0F0F3","maskColor":"rgba(255,255,255,0.3)"},"conf_opts":{"global":{"Date":null,"VMLRadialGradientURL":"http =//code.highcharts.com/list(version)/gfx/vml-radial-gradient.png","canvasToolsURL":"http =//code.highcharts.com/list(version)/modules/canvas-tools.js","getTimezoneOffset":null,"timezoneOffset":0,"useUTC":true},"lang":{"contextButtonTitle":"Chart context menu","decimalPoint":".","downloadCSV":"Download CSV","downloadJPEG":"Download JPEG image","downloadPDF":"Download PDF document","downloadPNG":"Download PNG image","downloadSVG":"Download SVG vector image","downloadXLS":"Download XLS","drillUpText":"◁ Back to {series.name}","exitFullscreen":"Exit from full screen","exportData":{"annotationHeader":"Annotations","categoryDatetimeHeader":"DateTime","categoryHeader":"Category"},"hideData":"Hide data table","invalidDate":null,"loading":"Loading...","months":["January","February","March","April","May","June","July","August","September","October","November","December"],"noData":"No data to display","numericSymbolMagnitude":1000,"numericSymbols":["k","M","G","T","P","E"],"printChart":"Print chart","resetZoom":"Reset zoom","resetZoomTitle":"Reset zoom level 1:1","shortMonths":["Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"],"shortWeekdays":["Sat","Sun","Mon","Tue","Wed","Thu","Fri"],"thousandsSep":" ","viewData":"View data table","viewFullscreen":"View in full screen","weekdays":["Sunday","Monday","Tuesday","Wednesday","Thursday","Friday","Saturday"]}},"type":"chart","fonts":"Arial","debug":false},"evals":[],"jsHooks":[]}</script>
```

</div>


#### Distribution of Age

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='va'>lung_data</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='op'>(</span><span class='fu'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='op'>(</span><span class='va'>age</span><span class='op'>)</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='op'>(</span>binwidth <span class='op'>=</span> <span class='fl'>10</span>, fill <span class='op'>=</span> <span class='st'>"seagreen2"</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggtheme.html'>theme_minimal</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/labs.html'>labs</a></span><span class='op'>(</span>title <span class='op'>=</span> <span class='st'>"Histogram of Age"</span>,</span>
<span>       x <span class='op'>=</span> <span class='st'>"Age"</span>,</span>
<span>       y <span class='op'>=</span> <span class='st'>"Counts"</span><span class='op'>)</span></span></code></pre></div>
<img src="lungs-data-eda-and-data-visualization-in-r_files/figure-html5/Histogram - Age-1.png" width="624" />

</div>



#### Distribution of Survival Time in Days

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='va'>lung_data</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='op'>(</span><span class='fu'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='op'>(</span><span class='va'>time</span><span class='op'>)</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='op'>(</span>binwidth <span class='op'>=</span> <span class='fl'>10</span>, fill <span class='op'>=</span> <span class='st'>"peachpuff2"</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggtheme.html'>theme_minimal</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/labs.html'>labs</a></span><span class='op'>(</span>title <span class='op'>=</span> <span class='st'>"Histogram of Survival time"</span>,</span>
<span>       x <span class='op'>=</span> <span class='st'>"Survival Time in Days"</span>,</span>
<span>       y <span class='op'>=</span> <span class='st'>"Counts"</span><span class='op'>)</span></span></code></pre></div>
<img src="lungs-data-eda-and-data-visualization-in-r_files/figure-html5/Histogram - Survival Time in Days-1.png" width="624" />

</div>



#### Distribution of Karnofsky Performance Score by Physician

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='va'>lung_data</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='op'>(</span><span class='fu'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='op'>(</span><span class='va'>ph.karno</span><span class='op'>)</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='op'>(</span>binwidth <span class='op'>=</span> <span class='fl'>10</span>, fill <span class='op'>=</span> <span class='st'>"mistyrose3"</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggtheme.html'>theme_minimal</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/labs.html'>labs</a></span><span class='op'>(</span>title <span class='op'>=</span> <span class='st'>"Histogram of Karnofsky Performance Score by Physician"</span>,</span>
<span>       x <span class='op'>=</span> <span class='st'>"Karnofsky Performance Score"</span>,</span>
<span>       y <span class='op'>=</span> <span class='st'>"Counts"</span><span class='op'>)</span></span></code></pre></div>
<img src="lungs-data-eda-and-data-visualization-in-r_files/figure-html5/Histogram - Karnofsky Performance Score - by Physician-1.png" width="624" />

</div>


#### Distribution of Karnofsky Performance Score by Patient

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='va'>lung_data</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='op'>(</span><span class='fu'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='op'>(</span><span class='va'>pat.karno</span><span class='op'>)</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='op'>(</span>binwidth <span class='op'>=</span> <span class='fl'>10</span>, fill <span class='op'>=</span> <span class='st'>"lightpink2"</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggtheme.html'>theme_minimal</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/labs.html'>labs</a></span><span class='op'>(</span>title <span class='op'>=</span> <span class='st'>"Histogram of Karnofsky Performance Score by Patient"</span>,</span>
<span>       x <span class='op'>=</span> <span class='st'>"Karnofsky Performance Score"</span>,</span>
<span>       y <span class='op'>=</span> <span class='st'>"Counts"</span><span class='op'>)</span></span></code></pre></div>
<img src="lungs-data-eda-and-data-visualization-in-r_files/figure-html5/Histogram - Karnofsky Performance Score - by Patient-1.png" width="624" />

</div>


#### Distribution of Calories consumed at meals

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='va'>lung_data</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='op'>(</span><span class='fu'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='op'>(</span><span class='va'>meal.cal</span><span class='op'>)</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='op'>(</span>binwidth <span class='op'>=</span> <span class='fl'>10</span>, fill <span class='op'>=</span> <span class='st'>"maroon4"</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggtheme.html'>theme_minimal</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/labs.html'>labs</a></span><span class='op'>(</span>title <span class='op'>=</span> <span class='st'>"Histogram of Caloreis consumed at meals"</span>,</span>
<span>       x <span class='op'>=</span> <span class='st'>"Calories Consumed"</span>,</span>
<span>       y <span class='op'>=</span> <span class='st'>"Counts"</span><span class='op'>)</span></span></code></pre></div>
<img src="lungs-data-eda-and-data-visualization-in-r_files/figure-html5/Histogram - Calories consumed at meals-1.png" width="624" />

</div>


#### Distribution of Weight loss in last six month (pounds)

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span><span class='va'>lung_data</span> <span class='op'><a href='https://magrittr.tidyverse.org/reference/pipe.html'>%&gt;%</a></span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggplot.html'>ggplot</a></span><span class='op'>(</span><span class='fu'><a href='https://ggplot2.tidyverse.org/reference/aes.html'>aes</a></span><span class='op'>(</span><span class='va'>pat.karno</span><span class='op'>)</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/geom_histogram.html'>geom_histogram</a></span><span class='op'>(</span>binwidth <span class='op'>=</span> <span class='fl'>10</span>, fill <span class='op'>=</span> <span class='st'>"chocolate3"</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/ggtheme.html'>theme_minimal</a></span><span class='op'>(</span><span class='op'>)</span> <span class='op'>+</span></span>
<span>  <span class='fu'><a href='https://ggplot2.tidyverse.org/reference/labs.html'>labs</a></span><span class='op'>(</span>title <span class='op'>=</span> <span class='st'>"Histogram of Weight Loss in last Six Month (Pounds)"</span>,</span>
<span>       x <span class='op'>=</span> <span class='st'>"Weight Loss"</span>,</span>
<span>       y <span class='op'>=</span> <span class='st'>"Counts"</span><span class='op'>)</span></span></code></pre></div>
<img src="lungs-data-eda-and-data-visualization-in-r_files/figure-html5/Histogram - Weigth Loss-1.png" width="624" />

</div>






```{.r .distill-force-highlighting-css}
```
