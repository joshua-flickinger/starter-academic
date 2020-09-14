---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Creating a GIF from GOES Imagery"
subtitle: ""
summary: "This noteboook creates a GIF from GOES Imagery."
authors: [Joshua Flickinger]
tags: [Python, Jupyter Notebook, fire, smoke, weather]
categories: [python]
date: 2020-09-10T21:55:50-07:00
lastmod: 2020-09-10T21:55:50-07:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

GOES is a geostationary satellite operated by NOAA to monitor atmospheric conditions.  It produces a new set of imagery every 5 minutes for the entire United States!  NOAA hosts the static images for several days, so we can use these files to create little videos of the daily weather.

# Downloading the Images

The base URL for downloading the images is below.  If you follow this URL, you'll see a long list of files of different sizes for each date and time.

> https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/
> Note that the psw in the URL represents a specific area of the United States - California, Oregon, and Nevada.  Other prefixes apply for other areas of the country.

If you look closely, you'll notice all files follow the same pattern.  This pattern allows us to automate the download process.  The pattern is essentially:

1. Four digits representing the year (e.g. 2020)
2. Three digits representing the day of year (e.g. 253)
3. Four digits representing the time of day, in hours and minutes UTC (e.g. 2101)
4. The end of the pattern 'GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg' remains constant for every file

First, you want to set the base URL.


```python
base_url = r"https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/"
```

The year component will be straightforward, so next we need to calculate the day of the year for our date of interest.  Remember that the URL above only hosts images from the last several days, constraining the date of interest.  The `datetime` package provides an easy way for making the calculation.


```python
import datetime

day_of_year = datetime.datetime(2020,9,13).timetuple().tm_yday
day_of_year
```




    257



The next step is creating a list of times to append to our year and day of year.  But times are recorded in UTC, so to get a full day in California you want to start a little before 14:30.  The images are produced every 5 minutes, so you need a list of times at 5 minute intervals.  Creating the list of times will be easiest using `datetime`, but we'll eventually need the list in string format. `strftime` converts the times to strings based on the desired format.

> e.g. `date.strftime('%Y%m%d%H%M')`


```python
# Create a start time.  The date doesn't matter, since we already have a day_of_year to use
start = datetime.datetime(2020, 9, 8, 14, 1)

# Add 5 minutes to the start time for the desired range
date_list = [start + datetime.timedelta(minutes=5*x) for x in range(0, 120)]

# Convert the list of date-times to a list of strings
datetext=[x.strftime('%H%M') for x in date_list]
```

For each time in the list, you can plug the time from the list into the web URL filename pattern, and use `urllib` to actually download the item.  Some of the timestamps are missing images, so it's important to build in an exception for 404 errors.


```python
import urllib.request, urllib.error

for num in datetext:
    item = base_url + str(2020) + str(day_of_year) + str(num) + "_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg"
    print(item)
    try: 
        urllib.request.urlretrieve(item, "2020_"+str(day_of_year)+"_{}.jpg".format(num))
    except urllib.error.HTTPError as e:
        # Return code error (e.g. 404, 501, ...)
        print('HTTPError: {}'.format(e.code))
```

Since the pattern for the file names changes at midnight, we have to create a second list for the last times in the day with a new day of year.


```python
start = datetime.datetime(2020, 9, 9, 0, 1)
date_list = [start + datetime.timedelta(minutes=5*x) for x in range(0, 25)]
datetext=[x.strftime('%H%M') for x in date_list]
```


```python
day_of_year2 = day_of_year + 1
for num in datetext:
    item = base_url + str(2020) + str(day_of_year2) + str(num) + "_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg"
    print(item)
    try: 
        urllib.request.urlretrieve(item, "2020_" + str(day_of_year2) + "_{}.jpg".format(num))
    except urllib.error.HTTPError as e:
        # Return code error (e.g. 404, 501, ...)
        print('HTTPError: {}'.format(e.code))
```


# Creating a GIF from the Images

In order to turn the downloaded images into a GIF, the `imageio` package can be used.  This package appends every file from a list to a GIF or video output.

In order to make a list of image files, we can use the `listdir()` method from the `os` package to create a list of filenames.  Without specifying a path name, the `listdir()` method will look in the same folder where the jupyter notebook is saved (and where all the images have downloaded).  However, since the extension of the downloaded images are all jpg, we can use a modifier to only search for these files.


```python
import os

filenames = []
for file in os.listdir():
    if file.endswith(".jpg"):
        filenames.append(file)
```

Finally use the `imageio` package to create a gif from the list of filenames.


```python
import imageio

images = []
for filename in filenames:
    images.append(imageio.imread(filename))
imageio.mimsave("video.gif", images)
```
