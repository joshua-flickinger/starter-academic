---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Creating a GIF from GOES Imagery"
summary: "This noteboook creates a GIF from GOES Imagery."
authors:
- admin
tags: [Python, Jupyter Notebook, fire, smoke, weather]
categories: [Jupyter Notebook]
date: 2020-09-10T07:50:04-07:00

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder. 
image:
  caption: ""
  focal_point: ""
---
# Creating a GIF from a day of GOES Imagery

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

day_of_year = datetime.datetime(2020,9,10).timetuple().tm_yday
day_of_year
```




    254



The next step is creating a list of times to append to our year and day of year.  But times are recorded in UTC, so to get a full day in California you want to start a little before 14:30.  The images are produced every 5 minutes, so you need a list of times at 5 minute intervals.  Creating the list of times will be easiest using `datetime`, but we'll eventually need the list in string format. `strftime` converts the times to strings based on the desired format.

> e.g. `date.strftime('%Y%m%d%H%M')`


```python
# Create a start time.  The date doesn't matter, since we already have a day_of_year to use
start = datetime.datetime(2020, 9, 8, 14, 1)

# Add 5 minutes to the start time for the desired range
date_list = [start + datetime.timedelta(minutes=5*x) for x in range(0, 120)]

# Convert the list of date-times to a list of strings
datetext=[x.strftime('%H%M') for x in date_list]

# View the list
datetext
```




    ['1401',
     '1406',
     '1411',
     '1416',
     '1421',
     '1426',
     '1431',
     '1436',
     '1441',
     '1446',
     '1451',
     '1456',
     '1501',
     '1506',
     '1511',
     '1516',
     '1521',
     '1526',
     '1531',
     '1536',
     '1541',
     '1546',
     '1551',
     '1556',
     '1601',
     '1606',
     '1611',
     '1616',
     '1621',
     '1626',
     '1631',
     '1636',
     '1641',
     '1646',
     '1651',
     '1656',
     '1701',
     '1706',
     '1711',
     '1716',
     '1721',
     '1726',
     '1731',
     '1736',
     '1741',
     '1746',
     '1751',
     '1756',
     '1801',
     '1806',
     '1811',
     '1816',
     '1821',
     '1826',
     '1831',
     '1836',
     '1841',
     '1846',
     '1851',
     '1856',
     '1901',
     '1906',
     '1911',
     '1916',
     '1921',
     '1926',
     '1931',
     '1936',
     '1941',
     '1946',
     '1951',
     '1956',
     '2001',
     '2006',
     '2011',
     '2016',
     '2021',
     '2026',
     '2031',
     '2036',
     '2041',
     '2046',
     '2051',
     '2056',
     '2101',
     '2106',
     '2111',
     '2116',
     '2121',
     '2126',
     '2131',
     '2136',
     '2141',
     '2146',
     '2151',
     '2156',
     '2201',
     '2206',
     '2211',
     '2216',
     '2221',
     '2226',
     '2231',
     '2236',
     '2241',
     '2246',
     '2251',
     '2256',
     '2301',
     '2306',
     '2311',
     '2316',
     '2321',
     '2326',
     '2331',
     '2336',
     '2341',
     '2346',
     '2351',
     '2356']



For each time in the list, you can plug the time from the list into the web URL filename pattern, and use `urllib` to actually download the item.  Some of the timestamps are missing images, so it's important to build in an exception for 404 errors.


```python
base_url+str(2020)+str(day_of_year)
```




    'https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/2020254'




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

    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541401_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541406_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541411_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541416_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541421_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541426_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541431_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541436_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541441_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541446_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541451_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541456_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541501_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541506_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541511_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541516_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541521_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541526_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541531_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541536_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541541_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541546_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541551_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541556_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541601_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541606_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541611_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541616_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541621_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541626_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541631_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541636_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541641_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541646_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541651_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541656_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541701_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541706_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541711_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541716_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541721_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541726_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541731_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541736_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541741_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541746_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541751_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541756_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541801_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541806_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541811_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541816_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541821_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541826_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541831_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541836_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541841_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541846_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541851_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541856_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541901_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541906_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541911_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541916_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541921_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541926_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541931_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541936_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541941_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541946_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541951_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202541956_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542001_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542006_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542011_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542016_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542021_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542026_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542031_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542036_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542041_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542046_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542051_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542056_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542101_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542106_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542111_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542116_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542121_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542126_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542131_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542136_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542141_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542146_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542151_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542156_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542201_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542206_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542211_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542216_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542221_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542226_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542231_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542236_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542241_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542246_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542251_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542256_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542301_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542306_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542311_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542316_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542321_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542326_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542331_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542336_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542341_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542346_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542351_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202542356_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    HTTPError: 404
    

Since the pattern for the file names changes at midnight, we have to create a second list for the last times in the day with a new day of year.


```python
start = datetime.datetime(2020, 9, 9, 0, 1)
date_list = [start + datetime.timedelta(minutes=5*x) for x in range(0, 25)]
datetext=[x.strftime('%H%M') for x in date_list]
datetext
```




    ['0001',
     '0006',
     '0011',
     '0016',
     '0021',
     '0026',
     '0031',
     '0036',
     '0041',
     '0046',
     '0051',
     '0056',
     '0101',
     '0106',
     '0111',
     '0116',
     '0121',
     '0126',
     '0131',
     '0136',
     '0141',
     '0146',
     '0151',
     '0156',
     '0201']




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

    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540001_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540006_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540011_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540016_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540021_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540026_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540031_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540036_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540041_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540046_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540051_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540056_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540101_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540106_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540111_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540116_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540121_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540126_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540131_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540136_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540141_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540146_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540151_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540156_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    https://cdn.star.nesdis.noaa.gov/GOES17/ABI/SECTOR/psw/GEOCOLOR/20202540201_GOES17-ABI-psw-GEOCOLOR-2400x2400.jpg
    

# Creating a GIF from the Images

In order to turn the downloaded images into a GIF, the `imageio` package can be used.  This package appends every file from a list to a GIF or video output.  First, the directory where you saved the images above should be specified.

>Note: It's recommended that you move the image files to a new folder, so you aren't accidentally creating a GIF with the unintended images.


```python
pathname = r"C:\Path\To\GOES\Images\Folder"
```

Use the pathname as an input for the `listdir()` method from the `os` package to create a list of filenames.


```python
import os

filenames = os.listdir(path=pathname)
```

Concatenate the pathname with the filename in a new list.  This can be accomplished with a for loop and the `.append()` method.


```python
newnames = []
for filename in filenames:
    fullpath = pathname + "\\" + filename
    newnames.append(fullpath)
newnames
```




    ['C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1401.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1406.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1411.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1416.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1421.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1426.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1431.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1436.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1441.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1446.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1451.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1456.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1501.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1506.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1511.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1516.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1521.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1526.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1531.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1536.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1541.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1546.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1551.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1556.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1606.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1611.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1616.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1621.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1626.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1631.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1636.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1641.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1646.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1651.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1656.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1701.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1706.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1711.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1716.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1721.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1726.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1731.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1736.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1741.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1746.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1751.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1756.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1801.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1806.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1811.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1816.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1821.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1826.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1831.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1836.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1841.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1846.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1851.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1856.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1901.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1906.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1911.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1916.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1921.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1926.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1931.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1936.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1941.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1946.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1951.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_1956.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2001.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2006.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2011.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2016.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2021.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2026.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2031.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2036.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2041.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2046.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2051.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2056.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2101.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2106.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2111.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2116.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2121.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2126.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2131.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2136.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2141.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2146.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2151.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2156.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2201.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2206.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2211.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2216.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2221.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2226.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2231.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2236.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2241.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2246.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2251.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2256.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2301.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2306.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2311.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2316.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2321.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2326.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2331.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2336.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2341.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2346.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2351.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_253_2356.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0001.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0006.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0011.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0016.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0021.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0026.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0031.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0036.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0041.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0046.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0051.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0056.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0101.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0106.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0111.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0116.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0121.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0126.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0131.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0136.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0141.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0146.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0151.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0156.jpg',
     'C:\\Users\\jflickinger\\Pictures\\GOES\\2020-09-09\\2020_254_0201.jpg']



Finally use the `imageio` package to create a gif from your list of filenames.


```python
import imageio

images = []
for filename in newnames:
    images.append(imageio.imread(filename))
imageio.mimsave(pathname + "\\video.gif", images)
```
