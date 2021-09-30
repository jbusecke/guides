# Installing natural earth datasets for cartopy on a server without internet connection

I recently encountered this with the basin masking functionality, which needs the NE marine poly dataset

> This is e.g. helpful on `tigercpu`. On the tigercpu nodes I have no internet access, so I need to manually download the shapefiles...
```
 # to find the appropriate directory
from cartopy import config
config['data_dir']
```

then move to the `shapefiles/natural_earth/physical` folder and do 
`wget http://naciscdn.org/naturalearth/50m/physical/ne_50m_geography_marine_polys.zip`
and
unzip ...
DONE

! Somehow the above link did not work, but I was able to make it work the following way:
1. Install cmip6_pp on a local machine, execute the tests (which should download the files into a local folder (find it just like above). 
2. Manually copying all files with `*marine_poly*` in it over.
3. Not pretty but worked for now.
