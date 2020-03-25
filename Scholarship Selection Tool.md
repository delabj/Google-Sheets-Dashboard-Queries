# Scholarship Selection Tool

I was asked to make an excel/google sheets tool to take a table of scholarships and applicants and to remove applicatents in the listings that have already been selected for another scholarship.

There were a few parameteres to be aware of:

* student names were in a single cell seperated by a return (`char(10)`). 
* The tabel with those selected already did not match the naming convention for those in the cells
  + Scholarship table had students as last_name, first_name. 
  + List of students already selected was slplit into two columns last_name first_name
* This had to be done in excel or sheets
  
### Scholarship Table
| Scholarship |                     List of Names                     |
|:-----------:|:-----------------------------------------------------:|
|   Award 1   | Olson, Sylvia <br>Smith, John <br>Kirt, Jame <br>Williams, Jenny |
|   Award 2   |        Smith, John <br>Kirt, James <br>Williams, Jenny        |
|   Award 3   |               McAvoy, James <br>Blaske, Tina              |

### Already Awarded Table
| Last name | First Name |
|:---------:|:----------:|
|   Smith   |    John    |
|  Williams |    Jenny   |
|   Blaske  |    Tina    |



## My Solution

I was pretty sure I could do this task in Google Sheets using *Array Formulas* I was less confident in completing this in a timly fashion in excel. 


My process uses vlookup, split, transpose, and joining again to produce a modified list.

The formula I used to create the new cell with values replaced dynamically is the following.

`=ArrayFormula(join(char(10),vlookup(TRANSPOSE(trim(split(B2,char(10)))),{Sheet1!$A$2:$A,Sheet1!$B$2:$B},2,0)))`

I'm documenting the process for future adaptation. I found the base formula after quite a bit of searching, on a Mr. Excel forum. But that formula was replacing every value. They used an example with plu# and fruit names, I didn't have every students name in the table I was looking up. This lead me to my first step, change the awarded table. 

### Rebuild the Awarded Table

The previous lookup table doesn't work. If the Vlookup fails, I want it to return the value it was trying to replace. There's probably a better way to do this, but my solution was to just change the table. 
The new table would need a row for every student. As I'm not the one filling out the table, nor working on the actuall selection process, I opted to create a new table that contained every student's name. 

I added two columns to the left, and three to the right. The two to the left are hidden and what I used for the vlookup. 

The resulting table looked like this:
|  Match Source   | Replace         | Last Name | First Name | Been Selected for a Scholarship | Which Scholarship? | Notes |
|:---------------:|-----------------|:---------:|------------|---------------------------------|--------------------|-------|
|   Smith, John   |                 |   Smith   |    John    | TRUE                            |                    |       |
| Williams, Jenny | Williams, Jenny |  Williams | Jenny      | FALSE                           |                    | **    |
|   Blaske, Tina  |                 |   Blaske  | Tina       | TRUE                            |                    |       |

The columns on the right, provide a control to adjust the hidden fields, and make the resulting table neater for later adaptation/use. 

In column A, I used a simple concatenation formula: `=C1&", "&D1`
In column B I used another simple if statement to make the cell blank if true, and keep the same value if FALSE. `=if(E2, "", A2)`

Columns C/D I left as is, and editable. 

Column E, I used response validation to create the options of TRUE/FALSE as the only valid responses. This could easily be made more aesthetically pleasing by adjusting the values checked in the if statement, but this was a quick and dirty dashboard/tool.

Column F, I left blank, for the selection committee to fill in as needed. 

Column G, I noticed that some students had some extra notes attached to their name. I wanted to be sure that student names matched exactly what was in the cell, and didn't want to play with regex/other data cleaning in my concat formula, so I manually moved the notes over

Finally, I had to add a list of all students and ensure that the formulas were ready to go. This leds to the next problem I had to solve.


### Generating the list of students

There is almost certainly a better/more efficent way to do this, but the turn around time was ASAP, so I did this in a hybrid between manual/formulic way. 

I made a new sheet, and in cell A1, I put in this formula: `=join(char(10), 'Scholarship and '!B:B)` This formula takes all applicants for each scholarship and joins them into a single text string seperated by a return. I had to join them before I could split them out, otherwise it only was splitting list of the first scholarship. 

In cell B2, I used this formula to split them: `=transpose(SPLIT(A1, char(10)))` Which did a simple split putting each applicant on a line, but as students could apply for as many scholarships as they wanted, there were a lot of duplicates. 

I then copied column B and pasted it to cell C as values. I disabled the formulas using an `'` before the `=` and removed duplicates and split by commas. I added these students to the previous list on the Awarded page. 


## Wrapping up

After doing that prep work, I was almost done. I was able to use the formula I'd modified before to replace the items in a list, but there were a few double returns. I used a simple subsitution of double `char(10)` to remove those, and then hid a lot of the formula squares. 


`=ArrayFormula(join(char(10),vlookup(TRANSPOSE(trim(split(B2,char(10)))),{Sheet1!$A$2:$A,Sheet1!$B$2:$B},2,0)))`
