## Set the end date for an article

     =arrayformula(
                   if(len(D2:D),
                      TO_DATE(int(A2:A))+F2:F*7,
                      ""
                     )
                   )
     
This formula calculates the end date for an item submitted through the form. 

## Sort the results onto a sheet

    =FILTER('Form Responses 1'!A:K, 
            ('Form Responses 1'!D:D="School Name")+('Form Responses 1'!D:D="All School Type")+('Form Responses 1'!D:D="All Schools"),  
            'Form Responses 1'!K:K>=today()
           )
   
This filters for the school name and type as well as any content the goes to all schools. It then filters old items out that no longer belong. 
