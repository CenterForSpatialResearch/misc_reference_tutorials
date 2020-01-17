## Satellite Grids

This is a brief guide to using the Google Static Maps API. In it we provide an example script that allows you to download satellite/aerial imagery through the [Google Static Maps API](https://developers.google.com/maps/documentation/maps-static/dev-guide).

The (very simple) script was initially developed as part of the Center for Spatial Research's work on *[In Plain Sight](https://c4sr.columbia.edu/projects/plain-sight)*, an installation for the 16th International Architecture Exhibition of La Biennale di Venezia in 2018.

Premise: from a CSV of latitude and longitude coordinates, download and save files of Google aerial or satellite imagery at a specified zoom level.

From this script it is possible to build images / projects like that of artist [Josh Begley](https://joshbegley.com/) or like this:

![grid of satellite images]


###  Instructions

#### Prerequisites
1. follow instructions for how to create a Google API key [here](https://developers.google.com/maps/documentation/maps-static/get-api-key)
2. you must have python 3 installed on your computer, [anaconda's distribution](https://www.anaconda.com/distribution/) is a great option
3. the script relies on three modules `requests` `csv` and `io`, install these using your package manager if you do not already have them installed


#### Step 1: formatting a csv file with latitude and longitude coordinates
1. the script below expects a CSV file in a specific format, with three columns, no headers/column names, column 1 contains a unique id for each point, column 2 contains latitude coordinates, column 3 contains longitude coordinates. A sample of three points are shown below

```
101,39.203425,8.403248
102,27.884442,-97.259635
103,-16.432387,136.098233
```
2. save the snippet above as `test_points.csv`, and/or change the latitude and longitude coordinates to locations of your choosing

#### Step 2: defining the function to download images

1.  review the script below, it defines a function to download satellite images based on input parameters.

```python
import requests
import csv
from io import open as iopen

# defining a function, it takes four parameters:
# the path to a csv file containing lat/lon coordinates
# your API key
# the zoom level you would your images to be downloaded as
# the size, in pixels of your images, the maximum is 640

def satellite_squares(csvforimport,APIkey,zoom,size):
    API_KEY = APIkey
    base = "https://maps.googleapis.com/maps/api/staticmap?scale=2&size="
    locations = []
    # this section reads the csv file
    with open(csvforimport, 'rt') as f:
        reader = csv.reader(f)
        for row in reader:
            ind, lat, lng = str(row[0]),float(row[1]),float(row[2])
            location = (ind, lat, lng)
            locations.append(location)
    # here we format a url to pass to the API for each row in the csv file
    for location in locations:
        ind, lat, lng = location
        latlng = "center={},{}".format(lat, lng)
        view = "zoom={}&maptype=satellite".format(zoom)
        keys = "key={}".format(API_KEY)
        url = "{}{}x{}&{}&{}&{}".format(base,size,size, latlng, view, keys)
        # defining the name for the image files that will be downloaded
        filename = "{}_{}.png".format(ind,zoom)
        res = requests.get(url)
        if res.status_code == requests.codes.ok:  
            with iopen(filename, 'wb') as file:
                file.write(res.content)
            print(filename)
        #here we tell the function to show us an error message if it is unable to download an image
        else:
            print(res.status_code)
            print(url)
            return False    
```

2. run the script above in your preferred python environment, jupyter notebooks is a good option, you can also run it in a terminal

3. once you have defined the function above call the function to download images.
```python
satellite_squares("path-to-your-csv-file/test_points.csv","yourAPIkey",13,640)
```
Note on file paths: depending on where / how you are running this python script you may need to use either an absolute or a relative path to your csv files

4. images for the latitude and longitude coordinates specified in your csv file will be downloaded into whatever location on your hard drive you are running the script from  


_________________________________________________________________________________
Guide written by Dare Brawley as a resource for the spring 2020 Conflict Urbanism seminar, based on tools used in producing *[In Plain Sight](https://c4sr.columbia.edu/projects/plain-sight)*, an installation for the 16th International Architecture Exhibition of La Biennale di Venezia in 2018.


[grid of satellite images]:img/satellite_grids_01.png
