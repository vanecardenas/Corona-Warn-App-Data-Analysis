# Corona-Warn-App-Data-Analysis
This repository contains the resources for the data analysis for my Seminar Paper of Data Security and Data Protection.
During this analysis I trace the exposure notifications of the Corona-Warn-App in my sourrounding.

The study was conducted with a random app in an android phone walking through the city of Berlin.

## Executing the Analysis of the data
### Prerequisites
- Have python 3 installed 
- Install JupyterLab to be able to open the notebook by followinf installation guide
  https://jupyterlab.readthedocs.io/en/stable/getting_started/installation.html
- Install the required python packages from the requirements.txt, e.g. via:
  `pip install -r requirements.txt`
- Install the sqlite3 client by following this installation guide:
  https://www.tutorialspoint.com/sqlite/sqlite_installation.htm 
  
### Extracting the exposure notification data from the sqlite data
The following code was used to extract the data from the sqlite database to a csv file:
Open data with the sqlite client:
```
sqlite3 RaMBLE_playstore_v40.15_20200728_2322.sqlite
```
To change the output mode to CSV:
```
.mode csv
```
To specify the output file:
```
.output cwa-bluetooth.csv
```
To extract the data filtered by SSUUID:
```
SELECT
    datetime(locations.timestamp, 'unixepoch', 'localtime') AS 'Timestamp', 
    devices.service_data AS 'R. Prox. Identifier',
    locations.rssi AS 'RSSI',
    locations.latitude AS 'Latitude',
    locations.longitude AS 'Longitude'
FROM
    devices
INNER JOIN locations ON devices.id = locations.device_id
WHERE
    devices.service_data LIKE 'fd6f:%'
ORDER BY
    locations.timestamp;
```
According to the [specification of the contact tracing API by Google](https://blog.google/documents/58/Contact_Tracing_-_Bluetooth_Specification_v1.1_RYGZbKW.pdf),
the bluetooth IDs are exchanged with the service UUID 0xFD6F, which is why I filter for 'fd6f:%':
The code for the sql filter follows [the example by Torsten Mohrbach](https://gist.github.com/tmohrbach/b549abd6818e2f0153a2489d0e35400e)

If you want to reset the output mode to print on the terminal, run:
```
.output stdout
```

### Running the Notebook
To be able to display the heatmap properly, you will need an Google API Key,
to request an Google API Key, go here: 
https://console.developers.google.com/flows/enableapi?apiid=maps_backend,geocoding_backend,directions_backend,distance_matrix_backend,elevation_backend&keyType=CLIENT_SIDE&pli=1
For the heatmap to run be drawn properly in jupyterlab, please follow the installation guide of the gmaps package:
https://jupyter-gmaps.readthedocs.io/en/latest/install.html
