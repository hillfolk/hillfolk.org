+++
title = "Object Pascal String Format"
author = ["Hillfolk"]
date = 2019-01-20
lastmod = 2019-02-08T15:58:48+09:00
tags = ["delphi", "pascal"]
categories = ["posts"]
weight = 100
draft = true
+++

## Number {#number}

```nil
d 	= 정수(Decimal)
e 	= 과학수치(실수)(Exponent,Scientific)
f 	= 부동소수점(Fixed)
g 	= 일반실수(General)
m 	= 통화(Money)
n 	= 숫자(Number,floating)
p 	= 포인터(Pointer)
s 	= 문자열(String)
u 	= 부호없는 정수(Unsigned decimal)
x 	= 16진수(Hexadecimal)
```


## Datetime {#datetime}

formatdatetime command
Example code : Showing all of the date field formatting data types

```nil
var
  myDate : TDateTime;
begin
  // Set up our TDateTime variable with a full date and time :
  // 5th of June 2000 at 01:02:03.004  (.004 milli-seconds)
  myDate := EncodeDateTime(2000, 6, 5, 1, 2, 3, 4);

// Date only - numeric values with no leading zeroes (except year)
  ShowMessage('              d/m/y = '+ formatdatetime('d/m/y', myDate));

// Date only - numeric values with leading zeroes
  ShowMessage('           dd/mm/yy = '+ formatdatetime('dd/mm/yy', myDate));

// Use short names for the day, month, and add freeform text ('of')
  ShowMessage('  ddd d of mmm yyyy = '+ formatdatetime('ddd d of mmm yyyy', myDate));

// Use long names for the day and month
  ShowMessage('dddd d of mmmm yyyy = '+ formatdatetime('dddd d of mmmm yyyy', myDate));

// Use the ShortDateFormat settings only
  ShowMessage('              ddddd = '+ formatdatetime('ddddd', myDate));

// Use the LongDateFormat settings only
  ShowMessage('             dddddd = '+ formatdatetime('dddddd', myDate));

// Use the ShortDateFormat + LongTimeFormat settings
  ShowMessage('                  c = '+ formatdatetime('c', myDate));
end;

```

```nil

Show full unit code
		 d/m/y = 5/6/00
	      dd/mm/yy = 05/06/00
     ddd d of mmm yyyy = Mon 5 of Jun 2000
   dddd d of mmmm yyyy = Monday 5 of June 2000
		 ddddd = 05/06/2000
		dddddd = 05 June 2000
		     c = 05/06/2000 01:02:03

```

Example code : Showing all of the time field formatting data types

```nil
var
  myDate : TDateTime;

begin
  // Set up our TDateTime variable with a full date and time :
  // 5th of June 2000 at 01:02:03.004  (.004 milli-seconds)
  myDate := EncodeDateTime(2000, 6, 5, 1, 2, 3, 4);

// Time only - numeric values with no leading zeroes
  ShowMessage('     h:n:s.z = '+formatdatetime('h:n:s.z', myDate));

// Time only - numeric values with leading zeroes
  ShowMessage('hh:nn:ss.zzz = '+formatdatetime('hh:nn:ss.zzz', myDate));

// Use the ShortTimeFormat settings only
  ShowMessage('           t = '+formatdatetime('t', myDate));

// Use the LongTimeFormat settings only
  ShowMessage('          tt = '+formatdatetime('tt', myDate));

// Use the ShortDateFormat + LongTimeFormat settings
  ShowMessage('           c = '+formatdatetime('c', myDate));
end;


Show full unit code
	h:m:s.z = 1:2:3.4
   hh:mm:ss.zzz = 01:02:03.004
	      t = 01:02
	     tt = 01:02:03
	      c = 05/06/2000 01:02:03
Example code : Showing the effect of local date format settings
var
  myDate : TDateTime;

begin
  // Set up our TDateTime variable with a full date and time :
  // 5th of June 2049 at 01:02:03.004  (.004 milli-seconds)
  //
  // Note that 49 is treated as 2049 as follows :
  //               TwoDigitYearCenturyWindow => 50
  //                            Current year => 2008 (at time of writing)
  //      Subtract TwoDigitYearCenturyWindow => 1958
  //            2 digit year to be converted => 49
  //  Compare with the last 2 digits of 1958 => Less
  //      So the year is in the next century => 2049
  // (58 would be converted to 1958)

myDate := StrToDateTime('05/06/49 01:02:03.004');

// Demonstrate default locale settings

// Use the DateSeparator and TimeSeparator values
  ShowMessage('dd/mm/yy hh:nn:ss = '+
	      formatdatetime('dd/mm/yy hh:nn:ss', myDate));

// Use ShortMonthNames
  ShowMessage('              mmm = '+formatdatetime('mmm', myDate));

// Use LongMonthNames
  ShowMessage('             mmmm = '+formatdatetime('mmmm', myDate));

// Use ShortDayNames
  ShowMessage('              ddd = '+formatdatetime('ddd', myDate));

// Use LongDayNames
  ShowMessage('             dddd = '+formatdatetime('dddd', myDate));

// Use the ShortDateFormat string
  ShowMessage('            ddddd = '+formatdatetime('ddddd', myDate));

// Use the LongDateFormat string
  ShowMessage('           dddddd = '+formatdatetime('dddddd', myDate));

// Use the TimeAmString
  ShowMessage('           hhampm = '+formatdatetime('hhampm', myDate));

// Use the ShortTimeFormat string
  ShowMessage('                t = '+formatdatetime('t', myDate));

// Use the LongTimeFormat string
  ShowMessage('               tt = '+formatdatetime('tt', myDate));

// Use the TwoDigitCenturyWindow
  ShowMessage('       dd/mm/yyyy = '+
	      formatdatetime('dd/mm/yyyy', myDate));

ShowMessage('');

// Now change the defaults
  DateSeparator      := '-';
  TimeSeparator      := '_';
  ShortDateFormat    := 'dd/mmm/yy';
  LongDateFormat     := 'dddd dd of mmmm of yyyy';
  TimeAMString       := 'morning';
  TimePMString       := 'afternoon';
  ShortTimeFormat    := 'hh:nn:ss';
  LongTimeFormat     := 'hh : nn : ss . zzz';
  ShortMonthNames[6] := 'JUN';
  LongMonthNames[6]  := 'JUNE';
  ShortDayNames[1]   := 'SUN';
  LongDayNames[1]    := 'SUNDAY';
  TwoDigitYearCenturyWindow := 75; // This means 49 is treated as 1949

// Set up our TDateTime variable with the same value as before
  // except that we must use the new date and time separators
  // The TwoDigitYearCenturyWindow variable only takes effect here
  myDate := StrToDateTime('09-02-49 01_02_03.004');

// Use the DateSeparator and TimeSeparator values
  ShowMessage('dd/mm/yy hh:nn:ss = '+
	      formatdatetime('dd/mm/yy hh:nn:ss', myDate));

// Use ShortMonthNames
  ShowMessage('              mmm = '+formatdatetime('mmm', myDate));

// Use LongMonthNames
  ShowMessage('             mmmm = '+formatdatetime('mmmm', myDate));

// Use ShortDayNames
  ShowMessage('              ddd = '+formatdatetime('ddd', myDate));

// Use LongDayNames
  ShowMessage('             dddd = '+formatdatetime('dddd', myDate));

// Use the ShortDateFormat string
  ShowMessage('            ddddd = '+formatdatetime('ddddd', myDate));

// Use the LongDateFormat string
  ShowMessage('           dddddd = '+formatdatetime('dddddd', myDate));

// Use the TimeAmString
  ShowMessage('           hhampm = '+formatdatetime('hhampm', myDate));

// Use the ShortTimeFormat string
  ShowMessage('                t = '+formatdatetime('t', myDate));

// Use the LongTimeFormat string
  ShowMessage('               tt = '+formatdatetime('tt', myDate));

// Use the TwoDigitCenturyWindow
  ShowMessage('       dd/mm/yyyy = '+
	      formatdatetime('dd/mm/yyyy', myDate));
end;
Show full unit code
   dd/mm/yy hh:mm:ss = 05/06/49 01:02:03
		 mmm = Jun
		mmmm = June
		 ddd = Sat
		dddd = Saturday
	       ddddd = 05/06/2049
	      dddddd = 05 June 2049
	      hhampm = 01AM
		   t = 01:02
		  tt = 01:02:03
	  dd/mm/yyyy = 05/06/2049

dd/mm/yy hh:nn:ss = 05-06-49 01_02_03
		 mmm = JUN
		mmmm = JUNE
		 ddd = SUN
		dddd = SUNDAY
	       ddddd = 05-JUN-49
	      dddddd = SUNDAY 05 of JUNE of 1949
	      hhampm = 01morning
		   t = 01_02_03
		  tt = 01 _ 02 _ 03 . 004
	  dd/mm/yyyy = 05-06-1949

```


## Float & Double Format {#float-and-double-format}

Float 와 Double 의 경우 자리수 표현을 위해 formatfloat 함수를 사용해야 한다.

```nil

var
  float : extended;

begin
  // Set up our floating point number
  float := 1234.567;

  // Display a sample value using all of the format options

  // Round out the decimal value
  ShowMessage('##### : '+formatfloat('#####', float));
  ShowMessage('00000 : '+formatfloat('00000', float));
  ShowMessage('0     : '+formatfloat('0'    , float));
  ShowMessage('#,##0 : '+formatfloat('#,##0', float));
  ShowMessage(',0    : '+formatfloat(',0'   , float));
  ShowMessage('');

  // Include the decimal value
  ShowMessage('0.#### : '+formatfloat('0.####', float));
  ShowMessage('0.0000 : '+formatfloat('0.0000', float));
  ShowMessage('');

  // Scientific format
  ShowMessage('0.0000000E+00 : '+formatfloat('0.0000000E+00', float));
  ShowMessage('0.0000000E-00 : '+formatfloat('0.0000000E-00', float));
  ShowMessage('#.#######E-## : '+formatfloat('#.#######E-##', float));
  ShowMessage('');

  // Include freeform text
  ShowMessage('"Value = "0.0 : '+formatfloat('"Value = "0.0', float));
  ShowMessage('');

  // Different formatting for negative numbers
  ShowMessage('0.0 : 'formatfloat('0.0'              , -1234.567));
  ShowMessage('0.0 "CR";0.0 "DB" : '+
	      formatfloat('0.0 "CR";0.0 "DB"', -1234.567));
  ShowMessage('0.0 "CR";0.0 "DB" : '+
	      formatfloat('0.0 "CR";0.0 "DB"',  1234.567));
  ShowMessage('');

  // Different format for zero value
  ShowMessage('0.0 : 'formatfloat('0.0' , 0.0));
  ShowMessage('0.0;-0.0;"Nothing" : '+
	      formatfloat('0.0;-0.0;"Nothing"', 0.0));
end;

```


## Reference {#reference}

[Number](http://www.delphibasics.co.uk/RTL.asp?Name=format)
[DateTime](http://www.delphibasics.co.uk/RTL.asp?Name=formatdatetime)
[Float&Double](http://www.delphibasics.co.uk/RTL.asp?Name=formatfloat)