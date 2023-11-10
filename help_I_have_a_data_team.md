🏗️ This page is under construction! 🏗️
=======================================

### When I want to share data with them it is better to do it in `.csv`!

`csv` stand for comma separated value: it is a *plain* text file (with usually the first row containing the header) that divide every fields with a comma ("a separator"). 

Sometimes you can also get `;`, `|` or even `^` as separator!

What matter here is that a csv file are a plain text file format, unlike `.xls`, were what you get is not what you see (when you share the files)!

### files name should not have white space and weird character!

Example: `final % 16 21.txt` is a bad name!

A lot of tools we are using are command line based (docker as an example) and here white spaces are used to separate stuff (arguments). 

Now, weird characters (#,  $, &) are somewhat "fine". The "somewhat" is hard to predict, sometimes this character are used for other purpose and sometimes the tools we are using (or a dependency of the tool we are using) is old enough to only use ASCI characters!


