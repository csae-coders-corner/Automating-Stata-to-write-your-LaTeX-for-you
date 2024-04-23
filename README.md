![CC Graphics 2024_AutomatingStata](https://github.com/csae-coders-corner/Automating-Stata-to-write-your-LaTeX-for-you/assets/148211163/220e440d-ef61-4588-bc80-51de0b4c3e3d)

# Automating-Stata-to-write-your-LaTeX-for-you

This post isn’t going to teach you how to make Stata write the words in your LaTeX file but it will show you how to automatically update tables, figures, and statistics. This is useful for a number of reasons. First, it streamlines changes and makes writing and updating much faster. Whenever you change a regression or update the sample, all you have to do is run the do-file again and everything will update in LaTeX as well. Second, it makes mistakes a lot less likely. The room for human error is decreased because there is no need for manual copy-pasting!

Here is a collection of my favourite lines of code to clean string variables: 

Set up
In Stata, make sure you save your output in a location where LaTeX will look for it. For simplicity, put it in the same folder that your main .tex file is in. Make sure you have the graphicx package loaded in LaTeX.

Tables
We can use esttab (see the Coders-Corner posts ‘Adding Statistics to a Table in Stata’ and ‘Flexible Code for Balance and Summary Tables’ for details about esttab) To output the file. Consider the following example- 
```
sysuse auto.dta, clear
eststo reg1 : reg price mpg
eststo reg2 : reg price rep78
esttab reg1 reg2 using auto_table.tex
```
```
# You can now input the table directly into your LaTeX file:
\input{auto_table}
```
This gives the desired output:
|     |(1) price         |(2) price        | 
|-----|------------------|-----------------|
|mpg  |	-238.9***	(-4.50)|                 |
|rep78|	                 |	19.28 (0.05)   |
|cons	| 11253.1*** (9.61)|	6080.4***(4.77)|
|N    |	74               |	69             |

t statistics in parentheses
*p < 0.05, **p < 0.01, ***p < 0.001

## Graphs

Graphs can be exported in a similar way. Create and save the graph in Stata. (See the Coders-Corner post ‘How to make Stata graphs look nice’ to learn more about creating graphs in Stata)
```
scatter price mpg
graph export price_vs_mpg_scatter.png , replace
```

```
# Then, we can use the include graphics command in LaTeX. 
\includegraphics[width=0.5\textwidth]{price_vs_mpg_scatter.png} 
```

This generates the following output:

![auto stata 1](https://github.com/csae-coders-corner/Automating-Stata-to-write-your-LaTeX-for-you/assets/148211163/ebfa5962-e4af-475d-a82f-a3ccf231a36f)

## Statistics
Automatically updating statistics in text is harder, but no less important. For example, suppose we want to discuss a coefficient from the above table in the LaTeX file. We might write the following in LaTeX.
An increase in mpg by one implies a decrease in price by 238.89.

We would want the coefficient in this sentence to also update whenever the table updates, without having to do it manually. To do this, we need a user-written program (learn more about creating programs in Stata from the Coders Corner post ‘How to write programs on Stata’) written by Prof. Johannes Abeler. The program is called postToFile.
```
qui: reg price mpg
local mpg_coef = _b[mpg]
postToFile , file("reg_price_mpg_coef") value(‘mpg_coef’) fmt("%5.2f")
```

And in the corresponding LaTeX file type:
An increase in mpg by one implies a change in price by \input{reg_price_mpg_coef}.
This gives the following output: “An increase in mpg by one implies a change in price by -238.89”.

How does postToFile work? The postToFile program takes some real number saved in a local and puts it in it’s very own .tex file to be dynamically inputted into a master .tex file. The program is written below.

```
capture program drop postToFile
program define postToFile
syntax , file(string) value(real) [ fmt(string) ]
capture file close myfile
file open myfile using "‘file’.tex" , write replace text
file write myfile ‘fmt’ (‘value’)
file write myfile "%" // to avoid trailing space in LateX
file close myfile 
end
```

In this program, a user specifies a file path in file(), a real number stored in a local in value(), and optionally a display format for the number in fmt()1. In the first line, we make sure that no program called myfile is open. In the second line, we open a file called myfile using the path the user assigned in file(), and open the file with write privileges to replace the previous content. The third line writes the value the user specified to the file using user specified formatting. The fourth line appends a “%” sign which removes pesky trailing spaces LaTeX automatically adds. Finally, we close myfile and end the program.

**Luke Milsom, DPhil candidate in Economics, University College, 18 January 2021**















