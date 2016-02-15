# Useful formulas for simple statistics

Sum of values in cell range
```
=SUM(A1:A10)
```

Average of values in cell range
```
=AVERAGE(A1:A10)
```

Median of values in cell range
```
=MEDIAN(A1:A10)
```

Standard deviation of values in cell range
```
=STDEV(A1:A10)
```

Min of values in cell range
```
=MIN(A1:A10)
```

Max of values in cell range
```
=MAX(A1:A10)
```

Sum product of values in cell ranges
```
=SUMPRODUCT(A1:A10;B1:B10)
```

Cumulative Sums: use the absolute reference operator
```
=SUM($D$2:D2)
=SUM($D$2:D3)
=SUM($D$2:D4)
```

Intra-quartile range (see more on quartile at http://stattrek.com/statistics/dictionary.aspx?definition=Interquartile%20range)
```
=QUARTILE(B2:B101,3)-QUARTILE(B2:B101,1)
```
Some operators end in **IF** (**COUNTIF**, **COUNTIFS**, **SUMIF**). They accept a range of cells and a conditional. The **&** evaluates the formula to a number to use in the expression. See more at https://support.google.com/docs/answer/3093480?hl=en.
```
=COUNTIF(B2:B101, ">"&AVERAGE(B2:B101))
```

More information on statistical functions in Google Sheets: https://support.google.com/docs/topic/3105600?hl=en&ref_topic=3046366
