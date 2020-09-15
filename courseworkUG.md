---
follows: courseworkUGData
id: litvis
---

@import "../css/datavis.less"

# Undergraduate Coursework - Visualising college graduate data using Ethnicity demographics,salary and employemt

{(questions|}

- How has graduation rates for college majors changed over 20 years? Comparing increase in college graduates using ethnicity demographics and what majors are popular among different ethnic groups.
- How has college enrollment changed among different ethnic groups over the years? What can this tell us about future demographics? Closer look at ethnic demographics in genreral for ethnicities not considered previously.
- How well off are graduates from different majors? Considering increase in employment rates and average salaries for graduates over the years.

{|questions)}

{(visualization|}

#### Visualization 1: Comparing graduation Rates accross college majors over a 20 year period

```elm {v interactive}
vlChart : Spec
vlChart =
    let
        cfg =
            configure
                << configuration (coView [ vicoStroke Nothing ])
                << configuration (coAxis [ axcoTicks False, axcoDomain False, axcoLabelAngle 0 ])

        data =
            dataFromColumns []
                << dataColumn "Year" (numColumn "Year" tidyDemographicTable |> nums)
                << dataColumn "Total" (numColumn "Total" tidyDemographicTable |> nums)
                << dataColumn "count" (numColumn "count" tidyDemographicTable |> nums)
                << dataColumn "Major" (strColumn "Major" tidyDemographicTable |> strs)
                << dataColumn "Ethnicity" (strColumn "Ethnicity" tidyDemographicTable |> strs)

        sel =
            selection
                << select "zoom" seInterval [ seBindScales ]

        trans =
            transform
                << calculateAs "datum.count / datum.Total * 100" "Percentage"

        enc =
            encoding
                << position Y [ pName "Major", pMType Nominal, pAxis [ axTitle "Major" ] ]
                << position X [ pName "count", pQuant ]
                << color
                    [ mName "Ethnicity"
                    , mNominal
                    , mScale [ scScheme "accent" [] ]
                    ]
                << column [ fName "Year", fNominal ]
                << tooltips
                    [ [ tName "Major", tMType Nominal ]
                    , [ tName "Ethnicity", tMType Nominal ]
                    , [ tName "count", tMType Ordinal ]
                    , [ tName "Percentage", tMType Quantitative, tFormat ".2f" ]
                    ]
    in
    toVegaLite [ width 400, height (400 / 1.618), data [], bar [], enc [], cfg [], trans [], sel [] ]
```

#### Visualization 2: Increase in college enrollment rates by ethnicity demographics

```elm{v interactive}
lineGraph : Spec
lineGraph =
    let
        data =
            dataFromColumns []
                << dataColumn "Year" (numColumn "Year" tidyLineDemographicTable |> nums)
                << dataColumn "Total" (numColumn "Total" tidyLineDemographicTable |> nums)
                << dataColumn "count" (numColumn "count" tidyLineDemographicTable |> nums)
                << dataColumn "Ethnicity" (strColumn "Ethnicity" tidyLineDemographicTable |> strs)

        sel =
            selection
                << select "zoom" seInterval [ seBindScales ]
                << select "mySelection"
                    seSingle
                    [ seFields [ "Ethnicity" ]
                    , seNearest True
                    , seBind
                        [ iSelect "Ethnicity"
                            [ inName "Select Ethnicity : "
                            , inOptions
                                [ "Whites"
                                , "AfricanAmerican"
                                , "Hispanic"
                                , "Asian/PacificIslander"
                                , "AmericanIndian/AlaskaNative"
                                , "NonResidentAllien"
                                ]
                            ]
                        ]
                    ]

        enc =
            encoding
                << position X [ pName "Year", pMType Nominal, pAxis [ axTitle "Year" ] ]
                << position Y [ pName "count", pQuant ]
                << color
                    [ mSelectionCondition (selectionName "mySelection")
                        [ mName "Ethnicity", mNominal, mScale [ scScheme "dark2" [] ] ]
                        [ mStr "black" ]
                    ]
                << opacity
                    [ mSelectionCondition (selectionName "mySelection")
                        [ mNum 1 ]
                        [ mNum 0.1 ]
                    ]
                << tooltips
                    [ [ tName "Ethnicity", tMType Nominal ]
                    , [ tName "count", tMType Quantitative ]
                    , [ tName "Year", tMType Nominal ]
                    ]
    in
    toVegaLite [ width 400, height (300 / 1.618), data [], line [ maPoint (pmMarker []) ], enc [], sel [] ]
```

#### Visualisation 3: Average salary and employment for graduates over the years for selected majors

1. Biological Sciences :

```elm {v interactive}
bioSciChart : Spec
bioSciChart =
    let
        data =
            dataFromColumns []
                << dataColumn "Year" (numColumn "Year" tidyBioSciSalaryEmploymentTable |> nums)
                << dataColumn "TotalGrads" (numColumn "TotalGrads" tidyBioSciSalaryEmploymentTable |> nums)
                << dataColumn "count" (numColumn "count" tidyBioSciSalaryEmploymentTable |> nums)
                << dataColumn "Status" (strColumn "Status" tidyBioSciSalaryEmploymentTable |> strs)
                << dataColumn "Salary" (numColumn "Salary" tidyBioSciSalaryEmploymentTable |> nums)

        trans =
            transform
                << calculateAs "datum.count / datum.TotalGrads * 100" "Percentage"

        enc1 =
            encoding
                << position Y [ pName "count", pMType Quantitative, pAxis [ axValues (nums [ 1500000 ]), axTitle "Total Graduates" ] ]
                << position X [ pName "Year", pNominal ]
                << color
                    [ mName "Status"
                    , mNominal
                    , mScale [ scScheme "pastel2" [] ]
                    ]
                << tooltips
                    [ [ tName "Year", tMType Nominal ]
                    , [ tName "count", tMType Ordinal ]
                    , [ tName "Percentage", tMType Quantitative ]
                    ]

        spec1 =
            asSpec [ enc1 [], bar [] ]

        enc2 =
            encoding
                << position X [ pName "Year", pMType Nominal, pAxis [ axTitle "Year" ] ]
                << position Y [ pName "Salary", pQuant, pScale [ scDomain (doUnionWith (doNums [ 40000, 120000 ])) ], pAxis [ axTitle "Average Salary $" ] ]
                << tooltips
                    [ [ tName "Year", tMType Nominal ]
                    , [ tName "Salary", tMType Ordinal ]
                    ]

        spec2 =
            asSpec [ enc2 [], line [ maPoint (pmMarker []) ] ]

        res =
            resolve
                << resolution (reScale [ ( chY, reIndependent ) ])
    in
    toVegaLite [ width 150, height (250 / 1.618), data [], bar [], layer [ spec1, spec2 ], res [], trans [] ]
```

2. Computer Science & Mathematics :

```elm {v interactive}
csChart : Spec
csChart =
    let
        data =
            dataFromColumns []
                << dataColumn "Year" (numColumn "Year" tidyCsSalaryEmploymentTable |> nums)
                << dataColumn "TotalGrads" (numColumn "TotalGrads" tidyCsSalaryEmploymentTable |> nums)
                << dataColumn "count" (numColumn "count" tidyCsSalaryEmploymentTable |> nums)
                << dataColumn "Status" (strColumn "Status" tidyCsSalaryEmploymentTable |> strs)
                << dataColumn "Salary" (numColumn "Salary" tidyCsSalaryEmploymentTable |> nums)

        trans =
            transform
                << calculateAs "datum.count / datum.TotalGrads * 100" "Percentage"

        enc1 =
            encoding
                << position Y [ pName "count", pMType Quantitative, pAxis [ axValues (nums [ 1500000 ]), axTitle "Total Graduates" ] ]
                << position X [ pName "Year", pNominal ]
                << color
                    [ mName "Status"
                    , mNominal
                    , mScale [ scScheme "pastel2" [] ]
                    ]
                << tooltips
                    [ [ tName "Year", tMType Nominal ]
                    , [ tName "count", tMType Ordinal ]
                    , [ tName "Percentage", tMType Quantitative ]
                    ]

        spec1 =
            asSpec [ enc1 [], bar [] ]

        enc2 =
            encoding
                << position X [ pName "Year", pMType Nominal, pAxis [ axTitle "Year" ] ]
                << position Y [ pName "Salary", pQuant, pScale [ scDomain (doUnionWith (doNums [ 40000, 120000 ])) ], pAxis [ axTitle "Average Salary $" ] ]
                << tooltips
                    [ [ tName "Year", tMType Nominal ]
                    , [ tName "Salary", tMType Ordinal ]
                    ]

        spec2 =
            asSpec [ enc2 [], line [ maPoint (pmMarker []) ] ]

        res =
            resolve
                << resolution (reScale [ ( chY, reIndependent ) ])
    in
    toVegaLite [ width 150, height (250 / 1.618), data [], bar [], layer [ spec1, spec2 ], res [], trans [] ]
```

3. Economics :

```elm {v interactive}
ecoChart : Spec
ecoChart =
    let
        data =
            dataFromColumns []
                << dataColumn "Year" (numColumn "Year" tidyEcoSalaryEmploymentTable |> nums)
                << dataColumn "TotalGrads" (numColumn "TotalGrads" tidyEcoSalaryEmploymentTable |> nums)
                << dataColumn "count" (numColumn "count" tidyEcoSalaryEmploymentTable |> nums)
                << dataColumn "Status" (strColumn "Status" tidyEcoSalaryEmploymentTable |> strs)
                << dataColumn "Salary" (numColumn "Salary" tidyEcoSalaryEmploymentTable |> nums)

        trans =
            transform
                << calculateAs "datum.count / datum.TotalGrads * 100" "Percentage"

        enc1 =
            encoding
                << position Y [ pName "count", pMType Quantitative, pAxis [ axValues (nums [ 1000000 ]), axTitle "Total Graduates" ] ]
                << position X [ pName "Year", pNominal ]
                << color
                    [ mName "Status"
                    , mNominal
                    , mScale [ scScheme "pastel2" [] ]
                    ]
                << tooltips
                    [ [ tName "Year", tMType Nominal ]
                    , [ tName "count", tMType Ordinal ]
                    , [ tName "Percentage", tMType Quantitative ]
                    ]

        spec1 =
            asSpec [ enc1 [], bar [] ]

        enc2 =
            encoding
                << position X [ pName "Year", pMType Nominal, pAxis [ axTitle "Year" ] ]
                << position Y [ pName "Salary", pQuant, pScale [ scDomain (doUnionWith (doNums [ 40000, 120000 ])) ], pAxis [ axTitle "Average Salary $" ] ]
                << tooltips
                    [ [ tName "Year", tMType Nominal ]
                    , [ tName "Salary", tMType Ordinal ]
                    ]

        spec2 =
            asSpec [ enc2 [], line [ maPoint (pmMarker []) ] ]

        res =
            resolve
                << resolution (reScale [ ( chY, reIndependent ) ])
    in
    toVegaLite [ width 150, height (250 / 1.618), data [], bar [], layer [ spec1, spec2 ], res [], trans [] ]
```

4. Psychology :

```elm {v interactive}
psycChart : Spec
psycChart =
    let
        data =
            dataFromColumns []
                << dataColumn "Year" (numColumn "Year" tidyPsycSalaryEmploymentTable |> nums)
                << dataColumn "TotalGrads" (numColumn "TotalGrads" tidyPsycSalaryEmploymentTable |> nums)
                << dataColumn "count" (numColumn "count" tidyPsycSalaryEmploymentTable |> nums)
                << dataColumn "Status" (strColumn "Status" tidyPsycSalaryEmploymentTable |> strs)
                << dataColumn "Salary" (numColumn "Salary" tidyPsycSalaryEmploymentTable |> nums)

        trans =
            transform
                << calculateAs "datum.count / datum.TotalGrads * 100" "Percentage"

        enc1 =
            encoding
                << position Y [ pName "count", pMType Quantitative, pAxis [ axValues (nums [ 1500000 ]), axTitle "Total Graduates" ] ]
                << position X [ pName "Year", pNominal ]
                << color
                    [ mName "Status"
                    , mNominal
                    , mScale [ scScheme "pastel2" [] ]
                    ]
                << tooltips
                    [ [ tName "Year", tMType Nominal ]
                    , [ tName "count", tMType Ordinal ]
                    , [ tName "Percentage", tMType Quantitative ]
                    ]

        spec1 =
            asSpec [ enc1 [], bar [] ]

        enc2 =
            encoding
                << position X [ pName "Year", pMType Nominal, pAxis [ axTitle "Year" ] ]
                << position Y [ pName "Salary", pQuant, pScale [ scDomain (doUnionWith (doNums [ 40000, 120000 ])) ], pAxis [ axTitle "Average Salary $" ] ]
                << tooltips
                    [ [ tName "Year", tMType Nominal ]
                    , [ tName "Salary", tMType Ordinal ]
                    ]

        spec2 =
            asSpec [ enc2 [], line [ maPoint (pmMarker []) ] ]

        res =
            resolve
                << resolution (reScale [ ( chY, reIndependent ) ])
    in
    toVegaLite [ width 150, height (250 / 1.618), data [], bar [], layer [ spec1, spec2 ], res [], trans [] ]
```

{|visualization)}

{(insights|}

The visualizations above provide an overview of graduates from the US by major which support in answering the research questions defined. The data used for the visualizations may not be up to date but can still provide insights into the future based on the trend lines they form over a long period of time. The time period for data used in my visualizations was over 20 years so it would be highly likely that similar trends are produced for the years that follow.

#### Visualization 1:

The horizontal bar charts used for this visualization provide an insight into ethnicity demographics for selected college majors over a twenty-year period. The majors were selected for which data was available and many more majors were introduced the following years(which have not been included). The visualization shows that graduate numbers for minorities and asians have increased at a faster rate every ten years compared to the graduates belonging to the ‘whites’ ethnicity group. The number of Asian graduates mostly increased for computer science and mathematics major (from 7.84% in 1993 to 17.35% in 2013) while graduates belonging to minorities increased the most for the psychology major (from 10.03% in 1993 to 19.51% in 2013).
The visualization shows the increase in the popularity of mathematics related majors among Asians when compared minorities and whites. The visualization mostly conideres mjors belonging to the STEM category and could be expanded to provide more valuable insights if data for other majors was available.

#### Visualization 2:

This interactive line graph takes a closer look at college enrolment rates by considering more ethnic categories than the previous graph. It does stay true to the first visualization as both show there being more minorities than Asians enrolled in US colleges. While visualization one shows low numbers for minorities in some majors it would be safe to assume their preferences in non-mathematics related majors as this visualization shows a higher enrolment rate for African Americans (minorities in visualisation one). The visualization also shows the increase in Non-resident aliens (international students) being enrolled in US colleges. This category being the second most popular from 1980 to 2005 shows the increase in popularity of US colleges in the world.
While American Indian/Alaska Native have the lowest enrolment rates, I would expect a steady increase in enrolment for the future as more Non-resident aliens find jobs and become citizens of the US.

#### Visualization 3:

This visualization follows the same dataset used in the bar charts above and it attempts to answer research question 3 by looking at employment and salary data for different majors. The line graph represents the average salary for graduates during that year and the bars represent graduates employed and unemployed for that year. It doesn’t consider underemployment and people not in the labour force fall under the unemployed category. On initial inspection, graduates with the economics major have the highest average salary at the end of 2015 with USD 109940 and graduates with a psychology degree are paid the lowest with USD 63618. This is mainly due to the higher number of graduates bringing the overall average down but still represents how well off are graduates with the particular major. Computer science and mathematics graduates saw a steady increase in their average salary over the years which ended better with USD 89471 than when compared to graduates with a major in biological sciences by the end of 2015 with USD 80039.
Employment rates across all majors are shown to drop with more graduates coming into the workforce. Psychology is shown to have the worst employment rates with 77% at the end of 2015 and computer science has it the best with 81% and the rest finishing around 80%.

{|insights)}

{(designJustification|}

#### Visualization 1:

A horizontal bar chart was used in visualization one to compare most popular majors among different ethnicity groups. An alternative approach involved creating line graphs for different ethnicities with the lines depicting majors. This approach was disregarded since it failed to directly compare majors against each other. Munzner’s approach ranks position on a common scale to be the most effective when depicting ordered attributes by magnitude. The visualisation compares the number of graduates for different majors by ethnicity with the number of graduates being on the common x axis. This allows us to easily compare major data against each other. The colours depict the different ethnicity groups which are ranked higher in Munzner’s effectiveness rankings than motion which would have otherwise been used under the alternative line graph design. The colours chosen for the ethnicity groups were encoded using the accent colour scheme, which is categorical, so they are easily distinguishable from each other.

To depict the change in total grads by major by a ten-year scale a column encoding was added by year showing data for 1993, 2003 and 2013. This encodes data horizontally rather than vertically making it easier for showing change in number of graduates by major. All three years use a common scale for in the Y axis for the major which might bring some difficulties when looking at data located far from the scale. The visualization was made interactive for this purpose so the user can identify majors when hovering over the bars. A percentage value was also calculated so the user can view what % of the total grads each ethnicity is for each of the majors depicted. This makes it easier to compare change in popularity of majors for ethnic groups over the years.

Following Tufte’s approach to maximise data ink ratio all non-essential data ink was removed. The column encoding ensures that labels for the majors are not being repeated for the years and the horizontal version of the bar chart aligns major labels horizontally making them easier to read. The height of the chart is divided by the golden ratio to make it more pleasing to the eye. Data for some majors are bigger on the scale when compared to other majors making it hard to read smaller data especially when accessing data by ethnicity for some majors. The chart was made to be zoomable using the scroll wheel to solve this issue.

#### Visualization 2:

Visualization 2 is a line graph which looks at ethnicity demographics in more detail using enrolment data. Kindlmann and Scheidegger ‘s (2014) principal of visual data correspondence states reading changes in visualization should allow us to infer the nature of changes in the data that caused them. This visualization deals with enrollment data as opposed to graduate data in visualization one and both stay true to each other. Additionally, it gives us more insight into the data we are looking at. Ethnicity demographics have been encoded with the dark2 categorical colour scheme which makes them easily distinguishable from each other. Since all other ethnicities other than ‘whites’ have data values closer to each other, they appear to be grouped at the bottom of the line graph. Having colour values that are easily distinguishable from each other makes them easier to read.

Munzner’s rankings for identity channels has spatial region as higher ranking than colour or motion so following this approach a zoom feature is provided with the line graph making it easier for the end user to focus on the closely grouped ethnicities at the bottom. This visualization also provides the end user with a dropdown feature which allows them to highlight lines by ethnicity increasing the focus of the graph.Tool tips have also been implemented to improve readability of the graph as a whole.

#### Visualization 3:

This visualization is a line graph depicting change in salary layered on top of a bar chart which depicts change in graduates employed over the years. The focus of the graph is depicting change in salary over time so labels showing total graduates for the bar chart have been minimised following Tufte’s minimalistic approach. Labels for showing average salary in the Y axis are shown on the right and has been modified so the line graph falls in line with the underlying bar graph. This is done in hopes of shifting user attention to the right and to avoid any confusions when looking at the chart. The underlying bar chart uses a pastel categorical colour scheme so that the immediate focus is not given to the bar chart but to the line graph depicting average salary for the year. Both the bar chart and line graph use tooltips to provide the user with more information and an additional percentage value has been calculated so show change in graduates employed over the years for the bar chart.

The intended design for this chart was a dashboard with both rows and columns showing data for all majors but I faced issues encoding them. I believe this is due to the layering of the two graphs. This would also have uniform scales across all majors and would have provided more insight into the data. This design uses four individual charts for the which three of the total graduate scales are uniform. Tool tips have been used in hopes of not overestimating the data for economics graduates as the scale shows 1 million instead of the usual 1.5 million. The graphs under this visualization also have their heights divided by the golden ratio to make them more visually appealing.

{|designJustification)}

{(references|}

**Haroz, S., and Whitney, D.** (2012) How capacity limits of attention influence information visualization effectiveness. _IEEE Transactions on Visualization and Computer Graphics_, 18(12) pp.2402-2410.

**Munzner, T.** 2015 Visualization Analysis and Design, CRC Press.

**Tufte, E.** 2001 The Visual Display of Quantitative Information. Graphics Press.

**Wickham, H.** 2014 Tidy Data, Journal of Statistical Software.

**Kindlmann, G. and Scheidegger, C.** (2014) [An algebraic process for visualization design](http://vis.cs.ucdavis.edu/vis2014papers/TVCG/papers/2181_20tvcg12-kindlmann-2346325.pdf) _IEEE transactions on Visualization and Computer Braphics_ 20(12), pp.2181-2190.

{|references)}

```

```
