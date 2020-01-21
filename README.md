# thailand-geojson-topojson
This topic focus on creating geojson/topojson data to use with mapping lib such as D3.js</br>
based on [D3 - Thailand Map](http://bl.ocks.org/adkdev/fe15a54ad3748c72e059475e3f43d462)</br>
I am looking for Thailand map whit district, so I do below steps.

## Prepare json data
I download shape file from https://data.humdata.org/dataset/thailand-administrative-boundaries

Then use ogr2ogr tool from GDAL lib (python) to converse .shp to .geojson as below:

#### Province scope
`ogr2ogr -select "ADM1_EN, ADM1_TH" -f "GeoJSON" -lco COORDINATE_PRECISION=6 thailand_province.geojson tha_adm_rtsd_itos_20190221_shp_part_1\tha_admbnda_adm1_rtsd_20190221.shp`

#### District scope (Amphoe)
`ogr2ogr -select "ADM1_EN, ADM1_TH, ADM2_EN, ADM2_TH" -f "GeoJSON" -lco COORDINATE_PRECISION=6 thailand_district.geojson tha_adm_rtsd_itos_20190221_shp_part_1\tha_admbnda_adm2_rtsd_20190221.shp`

<b>Note: ADM1 = Province name, ADM2 = District name</b>

## Resize json (simplify number of point)
After get geojson file from the ogr2ogr tool, size of json file is too big (56MB of province file, 166MB of district file)

I use topojson lib to simplify big json files as below step
#### 1. Install topojson tool via npm
`npm install -g topojson-server` </br>
`npm install -g topojson-client` </br>
`npm install -g topojson-simplify`

#### 2. Converse geojson file to topojson
`geo2topo -o thailand_province.topojson thailand_province.geojson`</br>
`geo2topo -o thailand_district.topojson thailand_district.geojson`

#### 3. Simplify json file
`toposimplify -f -o thailand_province_simplified001.topojson -P 0.01 thailand_province.topojson`</br>
`toposimplify -f -o thailand_district_simplified0001.topojson -P 0.001 thailand_district.topojson`

#### 4. Check object name
`topo2geo -l < thailand_province_simplified001.topojson`</br>
`topo2geo -l < thailand_district_simplified0001.topojson`

#### 5. Converse topojson file to geojson with object name
`topo2geo thailand_province=thailand_province_simplified001.geojson < thailand_province_simplified001.topojson`</br>
`topo2geo thailand_district=thailand_district_simplified0001.geojson < thailand_district_simplified0001.topojson`

Now we can use simplified json with new size around 1 MB for better performance, enjoy :)

topojson ref:
</br>https://github.com/topojson/topojson-simplify
</br>https://github.com/topojson/topojson-server/blob/master/README.md
</br>https://github.com/topojson/topojson-client/blob/master/README.md
