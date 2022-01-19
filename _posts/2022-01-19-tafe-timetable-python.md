---
title: "Transferring my TAFE QLD timetable into Google Calendar with Python"
last_modified_at: 2022-01-19T16:20:02-05:00
categories:
  - blog
tags:
  - linux
  - python
  - ics
  - calendar
  - tafe
---

TAFE Queensland, where I am studying a Diploma of IT, has a website to access the [class timetables](https://timetables.tafeqld.edu.au/group). However, it only gives output in the form of text. But we want to put it into [Google Calendar](https://calendar.google.com). Data entry is bad, so let's automate it using `python`!

This project sort of grew larger than I thought it would. So I made a [github repository](https://github.com/simon-mcmahon/tafeqld-timetable-ics-parsing) with all the files and effort.
{: .notice--info}

The website text output looks like this:

![website screenshot](/assets/images/tafe-timetable-python/timetable.png)

And when we copy the text from the page, we get output (into the `xed` text editor - the default on `Linux Mint`) like this:

![copied text](/assets/images/tafe-timetable-python/copied-text-timetable.png)

So the format is:

```text
DATE1
\n
CLASS1
    TIMES
    'Room'
    ROOM LOCATION
    'Unit(s)'
    UNIT TEXT 1
    UNIT TEXT 2
\n
CLASS2
...

DATE2
...

```
## Parsing the text

So we can separate out the key variables with lines as something like:

* Date: Does it match the `WEEKDAY, MONTH MONTHDAY, YEAR` format?
* TIMES: Contains `"AM"` or `"PM"`.
* Class N: `TIMES` line to a new line (`\n`).
* ROOM LOCATION: Line following `"Room"`.
* UNITS: Line text after `"Units(s)"` until new line (`\n`).

So firstly, we split text into an array of strings for each DATE. Then split each date string into a CLASS string. 
Then we build up each `CLASS` object with date, time, room and units.

## Making it an ics file

Then we need some calendar parsing libraries. From this [tutorial](https://www.tutorialsbuddy.com/create-ics-calendar-file-in-python), the [icalendar](https://pypi.org/project/icalendar/) seems well support with the features we need.

Make sure to install this with `pip install icalendar`.
If you are running vanilla `Linux Mint 20.3` as I was, run:
```shell
sudo apt install python3-pip
```


## Writing the code

We have explained enough of the logic and the libraries. Show me the code.

```python
#!/usr/bin/python3

from icalendar import Calendar, Event
import pytz
from datetime import datetime
from dateutil.parser import parse

# Set the timezone
tz_string = "Australia/Brisbane"
timezone = pytz.timezone(tz_string)

# Get the lines as a list
with open('timetable-text-4week.txt','r') as f:
    lines = f.readlines()
    lines = [line.strip('\n') for line in lines] #Strip the '\n'

def is_date(date_string):
    try:
        parse(date_string)
        return True

    except ValueError:
        return False

# Test the isdate function
assert(is_date("Friday, January 21, 2022")==True)
assert(is_date("Thursday, February 3, 2022\n")==True)
assert(is_date("this is not a date\n")==False)

def close_lowest(value, num_list):
    # Finds the number in num_list which is less than or equal to its value
    lowdiff = max(num_list)
    for x in range(0, len(num_list)):
        diff = value - num_list[x]
        if (diff < lowdiff) and (diff >= 0):
            lowdiff = diff
            close_low = num_list[x]
    try:  
        return close_low
    except NameError:
        return 'Argument 1 lower than all entries in list'

# Test the close_lowest function
assert(close_lowest(5, [1,3,5,7,9]) == 5)
assert(close_lowest(4, [1,3,5,7,9]) == 3)
assert(close_lowest(-10, [1,3,5,7,9]) == 'Argument 1 lower than all entries in list')

dates = []
times = []
for count, string in enumerate(lines):

    # Each date uniquely defines a day
    if is_date(string):
        dates += [count]

    # Each time uniquely defines an event
    if (("AM" in string) or ("PM" in string)) and ("-" in string):
        times += [count]


events = [] # list of dictionaries with key data 

# Assign each event to a date (all dates are above times)
for x in range(0, len(times)):

    date_equiv = close_lowest(times[x], dates)
    # Assign event lines their own list
    if x < len(times)-1:
        event_lines = lines[times[x] : times[x+1]]
    else:
        event_lines = lines[times[x] : -1]

    room_num = event_lines.index('Room')
    room_txt = event_lines[room_num+1] #Assign line below "Room" to be Location

    try:
        unit_num = event_lines.index('Unit(s)')
        summary = str(event_lines[unit_num + 1]) # Make title the subject name
    except ValueError:
        unit_txt = ''
        summary = 'TAFE QLD CLASS' #default title 

    # Extract times
    date_string = lines[date_equiv]
    time_string = lines[times[x]]

    start = time_string.split('-')[0].strip(' ')
    end = time_string.split('-')[1].strip(' ')

    dt_start = parse(date_string + ' ' + start)
    dt_end = parse(date_string + ' ' + end)
    # Add in the timezones
    dt_start = timezone.localize(dt_start)
    dt_end = timezone.localize(dt_end)
    
    # Make description the entire event string with the date for error checking
    raw_event_string = date_string + '\n'
    for s in event_lines:
        raw_event_string += s + '\n'

    # Make and append the dictionary
    event_dict = {'summary': summary, 'description': raw_event_string, 'start': dt_start, 'end': dt_end, 'location': room_txt}
    events.append(event_dict)

# Make the events in the calendar
# Thanks to https://www.tutorialsbuddy.com/create-ics-calendar-file-in-python for code I have adapted here

cal = Calendar()
cal.add('version', '2.0')

for e in events:
    event = Event()
    event.add('summary', e['summary'])
    event.add('description', e['description'])
    event.add('location', e['location'])
    event.add('dtstart', e['start'])
    event.add('dtend', e['end'])
    event.add('dtstamp', datetime.now(pytz.timezone(tz_string)) ) # Time event was created.
 
    cal.add_component(event) # Adding events to calendar
# Write out the ical file to disk
filename = 'tafe-qld-timetable.ics'
with open(filename, 'wb') as ics_file:
    ics_file.write(cal.to_ical())

print('{} Events found. Processed and .ics file exported called {}'.format(len(events), filename))
```

## Importing to Google Calendar

From google support [here](https://support.google.com/calendar/answer/37118?hl=en&co=GENIE.Platform%3DDesktop#zippy=):

![import google calendar](/assets/images/tafe-timetable-python/import-gcal.png)

Here are the results of the output if you decide to replicate this for yourself.

The terminal output:

![terminal output](/assets/images/tafe-timetable-python/terminal-output.png)

The Google Calendar view:
![calendar view](/assets/images/tafe-timetable-python/calendar-view.png)

Another Google Calendar event view (you can see the correct parsing of the dates into the event):
![date-parsing-verify](/assets/images/tafe-timetable-python/date-parsing-verify.png)

