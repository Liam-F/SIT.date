---
layout: post
title: SIT.date - Wrappers for Common Date Operations
comments: true
---


To install [Systematic Investor Toolbox (SIT)](https://github.com/systematicinvestor/SIT) please visit [About](/about) page.




SIT.date package is the collection of functions to ease extraction of date period ends.

The first function that i want to highlight is the `date.ends` function. 
This is a user friendly wrapper to compute periods ends with optional logic to handle
holidays if holiday calendar is provided. It handles various commonly used periodicity by mapping them
into corresponding end period functions. For example, weeks, week, weekly, and w are all mapped into `date.week.ends'.

It also handles `bi-weekly` syntax by taking every other period end. For more precise handling you can directly 
specify by and skip input parameters. 

**Please note the `date.ends` function works differently depending on the input data type. 
I.e. if xts object is given, assume that xts is sourced from historical data and does not contain holidays
otherwise first filter out non business days and next apply dates periodicity function.**


Let's checkout some examples:





{% highlight r %}
library(SIT.date)

dates = seq(as.Date('1-Oct-2013','%d-%b-%Y'), as.Date('10-Jan-2015','%d-%b-%Y'), 1)
out = function(x) {
	temp = matrix(format( x ,'%d-%b-%Y %a'),nc=1)
		colnames(temp)='dates'
	print(temp)
}

out( dates[date.ends(dates, 'bi-monthly')] )
{% endhighlight %}



|dates           |
|:---------------|
|31-Oct-2013 Thu |
|31-Dec-2013 Tue |
|28-Feb-2014 Fri |
|30-Apr-2014 Wed |
|30-Jun-2014 Mon |
|29-Aug-2014 Fri |
|31-Oct-2014 Fri |
|31-Dec-2014 Wed |
    




{% highlight r %}
out( dates[date.ends(dates, 'bi-monthly', skip=1)] )
{% endhighlight %}



|dates           |
|:---------------|
|29-Nov-2013 Fri |
|31-Jan-2014 Fri |
|31-Mar-2014 Mon |
|30-May-2014 Fri |
|31-Jul-2014 Thu |
|30-Sep-2014 Tue |
|28-Nov-2014 Fri |
|10-Jan-2015 Sat |
    




{% highlight r %}
out( dates[date.ends(dates, 'monthly', skip=1, by=2)] )
{% endhighlight %}



|dates           |
|:---------------|
|29-Nov-2013 Fri |
|31-Jan-2014 Fri |
|31-Mar-2014 Mon |
|30-May-2014 Fri |
|31-Jul-2014 Thu |
|30-Sep-2014 Tue |
|28-Nov-2014 Fri |
|10-Jan-2015 Sat |
    

We can also specify a calendar to only consider business dates:


{% highlight r %}
dates = seq(as.Date('1-Mar-2015','%d-%b-%Y'), as.Date('1-May-2015','%d-%b-%Y'), 1)

out( dates[date.ends(dates, 'weekly', calendar = 'UnitedStates/NYSE')] )
{% endhighlight %}



|dates           |
|:---------------|
|06-Mar-2015 Fri |
|13-Mar-2015 Fri |
|20-Mar-2015 Fri |
|27-Mar-2015 Fri |
|02-Apr-2015 Thu |
|10-Apr-2015 Fri |
|17-Apr-2015 Fri |
|24-Apr-2015 Fri |
|01-May-2015 Fri |
    




{% highlight r %}
out( dates[date.ends(dates, 'weekly', last.date=F, calendar = 'Canada/TSX')] )
{% endhighlight %}



|dates           |
|:---------------|
|06-Mar-2015 Fri |
|13-Mar-2015 Fri |
|20-Mar-2015 Fri |
|27-Mar-2015 Fri |
|02-Apr-2015 Thu |
|10-Apr-2015 Fri |
|17-Apr-2015 Fri |
|24-Apr-2015 Fri |
|01-May-2015 Fri |
    




{% highlight r %}
# Good Friday 	April 3, 2015-04-03 is holiday
out( dates[date.ends(dates, 'weekly')] )
{% endhighlight %}



|dates           |
|:---------------|
|06-Mar-2015 Fri |
|13-Mar-2015 Fri |
|20-Mar-2015 Fri |
|27-Mar-2015 Fri |
|03-Apr-2015 Fri |
|10-Apr-2015 Fri |
|17-Apr-2015 Fri |
|24-Apr-2015 Fri |
|01-May-2015 Fri |
    


You also might find handy the `custom.date.bus` and `custom.date` functions.
These functions handle custom syntax to specify date ends. The expression given to these functions must
be in the following format:

([identifier] [periodicity]) [in/every] ([identifier] [periodicity]) ... [in/every] ([identifier] [periodicity])

For example:

* last day in Apr
* last day in first week in Apr
* 3rd Mon in last M in Q1
* 10th to last day in Apr

The `custom.date.bus` function also takes in the holiday calendar to properly adjust for holidays.

Let's checkout some examples:



{% highlight r %}
library(SIT.date)

dates = seq(as.Date('1-Oct-2010','%d-%b-%Y'), as.Date('1-Jan-2015','%d-%b-%Y'), 1)

out( dates[custom.date('last day in Apr', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|30-Apr-2011 Sat |
|30-Apr-2012 Mon |
|30-Apr-2013 Tue |
|30-Apr-2014 Wed |
    




{% highlight r %}
out( dates[custom.date.bus('last day in Apr', dates, 'UnitedStates/NYSE')] )
{% endhighlight %}



|dates           |
|:---------------|
|29-Apr-2011 Fri |
|30-Apr-2012 Mon |
|30-Apr-2013 Tue |
|30-Apr-2014 Wed |
    




{% highlight r %}
dates = seq(as.Date('1-Oct-2014','%d-%b-%Y'), as.Date('1-Jan-2015','%d-%b-%Y'), 1)

out( dates[custom.date.bus('first day in Jan', dates, 'UnitedStates/NYSE')] )
{% endhighlight %}



|dates |
|:-----|
    




{% highlight r %}
dates = seq(as.Date('1-Jan-2014','%d-%b-%Y'), as.Date('5-Jan-2015','%d-%b-%Y'), 1)
out( dates[custom.date('first day in Jan', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|01-Jan-2014 Wed |
|01-Jan-2015 Thu |
    




{% highlight r %}
out( dates[custom.date.bus('first day in Jan', dates, 'UnitedStates/NYSE')] )
{% endhighlight %}



|dates           |
|:---------------|
|02-Jan-2014 Thu |
|02-Jan-2015 Fri |
    




{% highlight r %}
# with out calendar the, logic assumes that 1 jan 2014 is first business date
out( dates[custom.date.bus('first day in Jan', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|01-Jan-2014 Wed |
|01-Jan-2015 Thu |
    




{% highlight r %}
out( dates[custom.date('last day in Apr', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|30-Apr-2014 Wed |
    




{% highlight r %}
out( dates[custom.date('first day in Apr', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|01-Apr-2014 Tue |
    




{% highlight r %}
out( dates[custom.date('last day in first week in Apr', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|05-Apr-2014 Sat |
    




{% highlight r %}
out( dates[custom.date('last Mon in Apr', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|28-Apr-2014 Mon |
    




{% highlight r %}
out( dates[custom.date('last Fri in Apr', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|25-Apr-2014 Fri |
    




{% highlight r %}
out( dates[custom.date('first day in Apr', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|01-Apr-2014 Tue |
    




{% highlight r %}
out( dates[custom.date('1st day in Apr', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|01-Apr-2014 Tue |
    




{% highlight r %}
out( dates[custom.date('10th day in Apr', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|10-Apr-2014 Thu |
    




{% highlight r %}
out( dates[custom.date('50th day in Apr', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|30-Apr-2014 Wed |
    




{% highlight r %}
out( dates[custom.date('10th to last day in Apr', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|20-Apr-2014 Sun |
    




{% highlight r %}
out( dates[custom.date('3rd Mon in Q', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|20-Jan-2014 Mon |
|21-Apr-2014 Mon |
|21-Jul-2014 Mon |
|20-Oct-2014 Mon |
|05-Jan-2015 Mon |
    




{% highlight r %}
out( dates[custom.date('3rd Mon in 1st Q', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|20-Jan-2014 Mon |
    




{% highlight r %}
out( dates[custom.date('3rd Mon in Q1', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|20-Jan-2014 Mon |
|05-Jan-2015 Mon |
    




{% highlight r %}
out( dates[custom.date('3rd Mon in last M in Q1', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|17-Mar-2014 Mon |
|05-Jan-2015 Mon |
    




{% highlight r %}
# Options Expiration is third Friday of the expiration month
# the expiration months are the first month of each quarter - January, April, July, October. 
out( dates[custom.date('3rd Fri in Q', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|17-Jan-2014 Fri |
|18-Apr-2014 Fri |
|18-Jul-2014 Fri |
|17-Oct-2014 Fri |
|02-Jan-2015 Fri |
    




{% highlight r %}
dates = seq(as.Date('1-Jan-2010','%d-%b-%Y'), as.Date('29-Apr-2015','%d-%b-%Y'), 1)
out( dates[custom.date('last day in Apr', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|30-Apr-2010 Fri |
|30-Apr-2011 Sat |
|30-Apr-2012 Mon |
|30-Apr-2013 Tue |
|30-Apr-2014 Wed |
|29-Apr-2015 Wed |
    




{% highlight r %}
dates = seq(as.Date('1-Jan-2010','%d-%b-%Y'), as.Date('30-Apr-2015','%d-%b-%Y'), 1)
out( dates[custom.date('last day in Apr', dates)] )
{% endhighlight %}



|dates           |
|:---------------|
|30-Apr-2010 Fri |
|30-Apr-2011 Sat |
|30-Apr-2012 Mon |
|30-Apr-2013 Tue |
|30-Apr-2014 Wed |
|30-Apr-2015 Thu |
    




{% highlight r %}
dates = seq(as.Date('1-Jan-2010','%d-%b-%Y'), as.Date('29-Apr-2015','%d-%b-%Y'), 1)
out( dates[custom.date.bus('last day in Apr', dates, 'UnitedStates/NYSE')] )
{% endhighlight %}



|dates           |
|:---------------|
|30-Apr-2010 Fri |
|29-Apr-2011 Fri |
|30-Apr-2012 Mon |
|30-Apr-2013 Tue |
|30-Apr-2014 Wed |
    

Please let me know if stumble upon any bugs.

The [SIT.date](https://github.com/systematicinvestor/SIT.date) is available at 
[github.com/systematicinvestor/SIT.date](https://github.com/systematicinvestor/SIT.date)

Please install with `devtools::install_github('systematicinvestor/SIT.date')`



*(this report was produced on: 2015-11-05)*
