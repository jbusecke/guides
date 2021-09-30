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

There is also an offline package for cartopy with this now. Good
