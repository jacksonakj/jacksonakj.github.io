---
layout: post
title: "Is your stored procedure slower than a normal query?"
date: "2013-04-11"
---
On occasion when a query is wrapped in a store procedure the execution time is horribly slow.  This is due to a common issue known as “parameter sniffing” in SQL Server.  A couple of solutions are commonly recommended;

1. Create local variables inside the stored procedure and assign the store procedure variables to the local ones.
1. Add WITH RECOMPILE to the stored procedure right before AS.

However, with one of my stored procedures neither of these options fixed the performance problem.  I discovered another method using the query hint OPTIMIZE FOR UNKNOWN.   This hint causes the query optimizer to use the statistical data instead of the procedures initial values when then query is compiled.

Add the OPTIMIZE FOR UNKNOWN query hint using the OPTION keyword after the WHERE clause.

{% highlight SQL %}
SELECT  FundNumber ,
        DepartmentNumber ,
        ActivityCodeNumber
FROM    OrderedAccounts
WHERE   ( @Limit IS NULL
          OR RowNumber <= @Limit
        )
OPTION  ( OPTIMIZE FOR UNKNOWN )
{% endhighlight %}
