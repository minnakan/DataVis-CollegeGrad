---
id: litvis

narrative-schemas:
  - narrative-schemas/courseworkUG.yml

elm:
  dependencies:
    gicentre/elm-vegalite: latest
    gicentre/tidy: latest
---

@import "css/datavis.less"

```elm {l=hidden}
import Tidy exposing (..)
import VegaLite exposing (..)
```

```elm{l=hidden}
demographicTable : Table
demographicTable =
    """Year\tTotal\tMajor\tAsians\tMinorities\tWhites
1993\t1294286\tBiologicalSciences\t84495\t115016\t1094775
1993\t1069349\tComputerScience&Math\t83826\t98047\t887476
1993\t641634\tEconomics\t43953\t41702\t555979
1993\t507076\tChemistry\t49984\t35181\t421911
1993\t1301889\tPsychology\t34473\t130616\t1136725
1993\t211876\tChemicalEngineering\t27531\t11278\t173067
1993\t336366\tCivilEngineering\t37295\t17421\t281442
1993\t653614\tElectricalEngineering\t78979\t40355\t534220
1993\t512533\tMechanicalEngineering\t48879\t22032\t441398
1993\t704259\tOtherEngineering\t52986\t37638\t612910
1993\t220883\tPhysics&Astronomy\t26170\t10484\t184125
2003\t1515749\tBiologicalSciences\t129543\t194109\t1192097
2003\t1379971\tComputerScience&Math\t169137\t164110\t1046724
2003\t752502\tEconomics\t101496\t65258\t585748
2003\t603240\tChemistry\t88878\t63911\t450451
2003\t1846865\tPsychology\t67018\t290544\t1489303
2003\t242089\tChemicalEngineering\t41995\t22265\t1777830
2003\t420742\tCivilEngineering\t54575\t53857\t312310
2003\t828727\tElectricalEngineering\t164480\t90659\t573587
2003\t702873\tMechanicalEngineering\t96678\t46136\t560059
2003\t893274\tOtherEngineering\t114023\t97875\t681377
2003\t248495\tPhysics&Astronomy\t54924\t14544\t179027
2013\t2259499\tBiologicalSciences\t255820\t368313\t1635366
2013\t2242124\tComputerScience&Math\t389093\t308667\t1544364
2013\t1280482\tEconomics\t165604\t157633\t957245
2013\t660846\tChemistry\t110468\t71518\t478860
2013\t3063565\tPsychology\t140496\t597622\t2325447
2013\t296151\tChemicalEngineering\t55090\t39555\t201506
2013\t538158\tCivilEngineering\t81636\t65733\t390789
2013\t1003084\tElectricalEngineering\t292670\t124765\t585649
2013\t837279\tMechanicalEngineering\t146696\t90124\t600459
2013\t1205041\tOtherEngineering\t185347\t191711\t827983
2013\t291317\tPhysics&Astronomy\t58991\t23332\t208994"""
        |> fromDelimited '\t'


lineDemographicTable : Table
lineDemographicTable =
    """Year\tTotal\tWhites\tAfricanAmerican\tHispanic\tAsian/PacificIslander\tAmericanIndian/AlaskaNative\tNonResidentAllien
1976\t1566644\t1335646\t89670\t30897\t28587\t6381\t75463
1980\t1617720\t1352351\t87910\t38642\t37735\t6003\t95079
1990\t1859531\t1449830\t99819\t57888\t71954\t7319\t172721
2000\t2156896\t1478644\t181425\t110781\t132679\t12644\t240723
2003\t2431117\t1616272\t230342\t136488\t152834\t14825\t280356
2005\t2523511\t1666846\t259205\t148420\t163029\t15899\t270112
2008\t2737076\t1749565\t315194\t169364\t184932\t17737\t300284"""
        |> fromDelimited '\t'


csSalaryEmploymentTable : Table
csSalaryEmploymentTable =
    """Year\tTotalGrads\tEmployed\tUnemployed\tSalary
1995\t1114519\t971588\t142931\t49672.61
1997\t1168978\t1031542\t137436\t56385.76
1999\t1221788\t1076208\t145580\t62377.24
2003\t1379971\t1141939\t238032\t74335.41
2010\t1981187\t1613827\t367360\t78681.35
2013\t2242124\t1831666\t410958\t82696.72
2015\t2489621\t2022240\t467381\t89471.56"""
        |> fromDelimited '\t'


ecoSalaryEmploymentTable : Table
ecoSalaryEmploymentTable =
    """Year\tTotalGrads\tEmployed\tUnemployed\tSalary
1995\t658039\t550690\t107349\t50778.48
1997\t666572\t562930\t103642\t58014.4
1999\t683013\t579907\t85106\t63571.99
2003\t752503\t611876\t140627\t76329.57
2010\t1100771\t893578\t207193\t81043.54
2013\t1280482\t1033496\t246986\t99932.09
2015\t1302458\t1046569\t255889\t109940.97"""
        |> fromDelimited '\t'


bioSciSalaryEmploymentTable : Table
bioSciSalaryEmploymentTable =
    """Year\tTotalGrads\tEmployed\tUnemployed\tSalary
  1995\t1338108\t1123413\t214695\t54523.54
  1997\t1424192\t1195514\t228678\t56631.83
  1999\t1515896\t1274871\t241025\t58445.62
  2003\t1515659\t1267159\t248500\t69317.51
  2010\t2034213\t1661480\t372733\t69541.25
  2013\t2259499\t1831630\t427869\t77267.2
  2015\t2627847\t2110636\t517211\t80039.13"""
        |> fromDelimited '\t'


psycSalaryEmploymentTable : Table
psycSalaryEmploymentTable =
    """Year\tTotalGrads\tEmployed\tUnemployed\tSalary
1995\t1404991\t1182531\t222460\t42852.08
1997\t1520163\t1274488\t245675\t45771.76
1999\t1619802\t1360456\t259346\t48769.15
2003\t1846866\t1464710\t382156\t55759.49
2010\t2734299\t2094611\t639688\t57583.93
2013\t3063565\t2381966\t681599\t58984.01
2015\t3355678\t2593450\t762228\t63618.92"""
        |> fromDelimited '\t'
```

```elm{l=hidden}
tidyDemographicTable : Table
tidyDemographicTable =
    """Year\tTotal\tMajor\tAsians\tMinorities\tWhites
1993\t1294286\tBiologicalSciences\t84495\t115016\t1094775
1993\t1069349\tComputerScience&Math\t83826\t98047\t887476
1993\t641634\tEconomics\t43953\t41702\t555979
1993\t507076\tChemistry\t49984\t35181\t421911
1993\t1301889\tPsychology\t34473\t130616\t1136725
1993\t211876\tChemicalEngineering\t27531\t11278\t173067
1993\t336366\tCivilEngineering\t37295\t17421\t281442
1993\t653614\tElectricalEngineering\t78979\t40355\t534220
1993\t512533\tMechanicalEngineering\t48879\t22032\t441398
1993\t704259\tOtherEngineering\t52986\t37638\t612910
1993\t220883\tPhysics&Astronomy\t26170\t10484\t184125
2003\t1515749\tBiologicalSciences\t129543\t194109\t1192097
2003\t1379971\tComputerScience&Math\t169137\t164110\t1046724
2003\t752502\tEconomics\t101496\t65258\t585748
2003\t603240\tChemistry\t88878\t63911\t450451
2003\t1846865\tPsychology\t67018\t290544\t1489303
2003\t242089\tChemicalEngineering\t41995\t22265\t177830
2003\t420742\tCivilEngineering\t54575\t53857\t312310
2003\t828727\tElectricalEngineering\t164480\t90659\t573587
2003\t702873\tMechanicalEngineering\t96678\t46136\t560059
2003\t893274\tOtherEngineering\t114023\t97875\t681377
2003\t248495\tPhysics&Astronomy\t54924\t14544\t179027
2013\t2259499\tBiologicalSciences\t255820\t368313\t1635366
2013\t2242124\tComputerScience&Math\t389093\t308667\t1544364
2013\t1280482\tEconomics\t165604\t157633\t957245
2013\t660846\tChemistry\t110468\t71518\t478860
2013\t3063565\tPsychology\t140496\t597622\t2325447
2013\t296151\tChemicalEngineering\t55090\t39555\t201506
2013\t538158\tCivilEngineering\t81636\t65733\t390789
2013\t1003084\tElectricalEngineering\t292670\t124765\t585649
2013\t837279\tMechanicalEngineering\t146696\t90124\t600459
2013\t1205041\tOtherEngineering\t185347\t191711\t827983
2013\t291317\tPhysics&Astronomy\t58991\t23332\t208994"""
        |> fromDelimited '\t'
        |> gather "Ethnicity" "count" [ ( "Asians", "asians" ), ( "Minorities", "minorities" ), ( "Whites", "whites" ) ]


tidyLineDemographicTable : Table
tidyLineDemographicTable =
    """Year\tTotal\tWhites\tAfricanAmerican\tHispanic\tAsian/PacificIslander\tAmericanIndian/AlaskaNative\tNonResidentAllien
1976\t1566644\t1335646\t89670\t30897\t28587\t6381\t75463
1980\t1617720\t1352351\t87910\t38642\t37735\t6003\t95079
1990\t1859531\t1449830\t99819\t57888\t71954\t7319\t172721
2000\t2156896\t1478644\t181425\t110781\t132679\t12644\t240723
2003\t2431117\t1616272\t230342\t136488\t152834\t14825\t280356
2005\t2523511\t1666846\t259205\t148420\t163029\t15899\t270112
2008\t2737076\t1749565\t315194\t169364\t184932\t17737\t300284"""
        |> fromDelimited '\t'
        |> gather "Ethnicity" "count" [ ( "Whites", "Whites" ), ( "AfricanAmerican", "AfricanAmerican" ), ( "Hispanic", "Hispanic" ), ( "Asian/PacificIslander", "Asian/PacificIslander" ), ( "AmericanIndian/AlaskaNative", "AmericanIndian/AlaskaNative" ), ( "NonResidentAllien", "NonResidentAllien" ) ]


tidyBioSciSalaryEmploymentTable : Table
tidyBioSciSalaryEmploymentTable =
    """Year\tTotalGrads\tEmployed\tUnemployed\tSalary
1995\t1338108\t1123413\t214695\t54523.54
1997\t1424192\t1195514\t228678\t56631.83
1999\t1515896\t1274871\t241025\t58445.62
2003\t1515659\t1267159\t248500\t69317.51
2010\t2034213\t1661480\t372733\t69541.25
2013\t2259499\t1831630\t427869\t77267.2
2015\t2627847\t2110636\t517211\t80039.13"""
        |> fromDelimited '\t'
        |> gather "Status" "count" [ ( "Employed", "Employed" ), ( "Unemployed", "Unemployed" ) ]


tidyCsSalaryEmploymentTable : Table
tidyCsSalaryEmploymentTable =
    """Year\tTotalGrads\tEmployed\tUnemployed\tSalary
1995\t1114519\t971588\t142931\t49672.61
1997\t1168978\t1031542\t137436\t56385.76
1999\t1221788\t1076208\t145580\t62377.24
2003\t1379971\t1141939\t238032\t74335.41
2010\t1981187\t1613827\t367360\t78681.35
2013\t2242124\t1831666\t410958\t82696.72
2015\t2489621\t2022240\t467381\t89471.56"""
        |> fromDelimited '\t'
        |> gather "Status" "count" [ ( "Employed", "Employed" ), ( "Unemployed", "Unemployed" ) ]


tidyEcoSalaryEmploymentTable : Table
tidyEcoSalaryEmploymentTable =
    """Year\tTotalGrads\tEmployed\tUnemployed\tSalary
1995\t658039\t550690\t107349\t50778.48
1997\t666572\t562930\t103642\t58014.4
1999\t683013\t579907\t85106\t63571.99
2003\t752503\t611876\t140627\t76329.57
2010\t1100771\t893578\t207193\t81043.54
2013\t1280482\t1033496\t246986\t99932.09
2015\t1302458\t1046569\t255889\t109940.97"""
        |> fromDelimited '\t'
        |> gather "Status" "count" [ ( "Employed", "Employed" ), ( "Unemployed", "Unemployed" ) ]


tidyPsycSalaryEmploymentTable : Table
tidyPsycSalaryEmploymentTable =
    """Year\tTotalGrads\tEmployed\tUnemployed\tSalary
1995\t1404991\t1182531\t222460\t42852.08
1997\t1520163\t1274488\t245675\t45771.76
1999\t1619802\t1360456\t259346\t48769.15
2003\t1846866\t1464710\t382156\t55759.49
2010\t2734299\t2094611\t639688\t57583.93
2013\t3063565\t2381966\t681599\t58984.01
2015\t3355678\t2593450\t762228\t63618.92"""
        |> fromDelimited '\t'
        |> gather "Status" "count" [ ( "Employed", "Employed" ), ( "Unemployed", "Unemployed" ) ]
```

## Data Shaping

College Graduate data was obtained as a large csv file with most of the fields being irrelevant to the research questions defined. Required data was manually inputed to tables shown below and were converted to a tidy format following Wickham's descriptions(2014). Tidy data organises data such that columns always refer to variables and rows always to observations.

### Data source 1

The data for college graduates by major was obtained from https://corgis-edu.github.io/corgis/csv/graduates/.

^^^elm m=(tableSummary 5 demographicTable)^^^

#### Tidy data.

^^^elm{m=(tableSummary 5 tidyDemographicTable)}^^^

### Data Source 2

https://nces.ed.gov/pubs2010/2010015/tables/table_24_2.asp

^^^elm{m=(tableSummary 5 lineDemographicTable)}^^^

#### Tidy Data

^^^elm{m=(tableSummary 5 tidyLineDemographicTable)}^^^

### Data source 3

Same dataset used as data source 1 https://corgis-edu.github.io/corgis/csv/graduates/.

Employment and salary data for Bio Sciences:
^^^elm{m=(tableSummary 5 bioSciSalaryEmploymentTable)}^^^

Employment and salary data for Computer Science and Mathematics
^^^elm{m=(tableSummary 5 csSalaryEmploymentTable)}^^^

Employment and salary data for Economics
^^^elm{m=(tableSummary 5 ecoSalaryEmploymentTable)}^^^

Employment and salary data for Psychology
^^^elm{m=(tableSummary 5 psycSalaryEmploymentTable)}^^^

#### Tidy Data

Employment and salary data for Bio Sciences:
^^^elm{m=(tableSummary 5 tidyBioSciSalaryEmploymentTable)}^^^

Employment and salary data for Computer Science and Mathematics
^^^elm{m=(tableSummary 5 tidyCsSalaryEmploymentTable)}^^^

Employment and salary data for Economics
^^^elm{m=(tableSummary 5 tidyEcoSalaryEmploymentTable)}^^^

Employment and salary data for Psychology
^^^elm{m=(tableSummary 5 tidyPsycSalaryEmploymentTable)}^^^
