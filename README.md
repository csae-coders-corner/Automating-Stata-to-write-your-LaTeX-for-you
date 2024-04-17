# Automating-Stata-to-write-your-LaTeX-for-you

This post isn’t going to teach you how to make Stata write the words in your LaTeX file but it will show you how to automatically update tables, figures, and statistics. This is useful for a number of reasons. First, it streamlines changes and makes writing and updating much faster. Whenever you change a regression or update the sample, all you have to do is run the do-file again and everything will update in LaTeX as well. Second, it makes mistakes a lot less likely. The room for human error is decreased because there is no need for manual copy-pasting!

Here is a collection of my favourite lines of code to clean string variables: 

Set up
In Stata, make sure you save your output in a location where LaTeX will look for it. For simplicity, put it in the same folder that your main .tex file is in. Make sure you have the graphicx package loaded in LaTeX.

Tables
We can use esttab (see the Coders-Corner posts ‘Adding Statistics to a Table in Stata’ and ‘Flexible Code for Balance and Summary Tables’ for details about esttab) To output the file. Consider the following example- 
sysuse auto.dta, clear
eststo reg1 : reg price mpg
eststo reg2 : reg price rep78
esttab reg1 reg2 using auto_table.tex
You can now input the table directly into your LaTeX file:
\input{auto_table}
This gives the desired output:
|     |(1) price   |(2) price  | 
|-----|------------|-----------|
|mpg  |	-238.9***	 |           |
|     |	(-4.50)	   |           |
|rep78|	           |	19.28    |
|     |            |		(0.05) |
|cons	| 11253.1*** |	6080.4***|
|     |	(9.61)     |	(4.77)   |
|------------------------------|
|N    |	74         |	69       |
|------------------------------|
t statistics in parentheses
* p < 0.05, ** p < 0.01, *** p < 0.001



