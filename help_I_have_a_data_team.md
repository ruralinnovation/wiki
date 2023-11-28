# Help I Have Data Team

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


Naming is hard but some conventions can greatly help. 

We can take one FCC files as an example: 

`bdc_40_Other_fixed_broadband_J23_14nov2023.csv`

We have nearly everything we need nicely separated by `_`:   
- "40" is the state fips  
- "Other" is the technology category  
- "fixed broadband" is what FCC is calling "data type"  
- "J23" is teh release (June 2023)   
- "14nov2023" is the date of release  

We could improve it by by changing a `_` to `-` between "fixed" and "broadband" (because they are part of the category):

`bdc_40_Other_fixed-broadband_J23_14nov2023.csv`

