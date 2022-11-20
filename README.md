# NS9188K
AIRS/LBL Radiances 2003-2021

### Requirements:
Jupyter (Python 3)
Dependencies are in the first cell of each workbook. The hardest one to find install instructions for is `py-hdf` so that one is provided here:
```
conda install py-hdf -c conda-forge
```

### Overview:
There are multiple Jupyter notebooks intended to be used sequentially:
```mermaid
graph TD;
    LBL_model--44 GB-->_AIRS5_shortcutter_;
    _AIRS1_GES_DISC_downloader--80 TB-->_AIRS2_L1b_HDFs_to_daily_CSV;
    _AIRS2_L1b_HDFs_to_daily_CSV--800 GB-->_AIRS3_daily_gzips_to_single_annual_csv;
    _AIRS3_daily_gzips_to_single_annual_csv--14 GB-->_AIRS4_single_annual_csv_to_gridded_annual_avg;
    _AIRS4_single_annual_csv_to_gridded_annual_avg--280 MB-->_AIRS_6;
    _AIRS5_shortcutter_--3.6 GB-->_AIRS_6;
    _AIRS_6_Figs-->Figures

```
 - `_AIRS1_GES_DISC_downloader` accepts a `year` and proceeds to download `.HDF` files from GES DISC. For example, `AIRS.2003.07.04.122.L1B.AIRS_Rad.v5.0.0.0.G07090064822.hdf` is a 55 MB file, and there are 240 per day. One year of AIRS data will take approximately 1 week to download over a 200 MBPS connection and will occupy ~4.5 TB.
 - `_AIRS2_L1b_HDFs_to_daily_CSV` will read in the 4.5 TB/yr of `.HDF` files, extract the clear-sky radiances, and save each day of radiances to a single compressed `.csv.gzp` file (app. 100 MB each).
 - `_AIRS3_daily_gzips_to_single_annual_csv` will read in each of the 100 MB files (each of which has every clear-sky radiance individually) and create a gridded daily average `.csv.gzp` where every day is the average radiance for every grid cell. Each file of annual gridded average is app. 770 MB.
 - `_AIRS4_single_annual_csv_to_gridded_annual_avg` will read in the 770 MB file of gridded daily average radiances for a single year produced by `_AIRS3_` and output monthly averages for each grid cell as a 15 MB `.csv.gzp` file


### How to use:
1. Acquire a username/password for GES DISC Earthdata system `https://www.earthdata.nasa.gov/`
2. In `_AIRS1_.ipynb` in the `def download_files:` function replace `USERNAME` and `PASSWORD` with your Earthdata username/password.
3. Verify the local folder exists for saving data. The program expects to find a subfolder with the year and within that year a two digit subfolder for month. These folders must be created by the user. Example: to download AIRS L1b 2003 data, you must create `C:\data\AIRS\L1b\2003\01\` folder, as well, as `02`, `03`...`12`
4. In `_AIRS2_.ipynb` set the `folder_L1b` to be the location where the `.HDF` files from `_AIRS1_` are located (specify the root folder that has the \year\ folders) then specify which year(s) and month(s) you want to process. Verify the output (save path at the end) is acceptable. Then run all cells to create daily .csv.gzp files with each clear-sky radiance+lat+lon.
5. In `_AIRS3_.ipynb` set the `files` location where the `.csv.gzp` files are located (from `_AIRS2_`). Verify the output (save path at the end) is acceptable. Then run all cells to create a `2003_dailyavgs.csv.gzp` that is a gridded daily average radiance.
6. In `_AIRS4_.ipynb` set the `files` location where the `.csv.gzp` files are located (from `_AIRS3_`). Verify the output (save path at the end) is acceptable. Then run all cells to create a `2003_lati_lonj.csv.gzp` that is 'i' lat grid cells and 'j' lon grid cells of monthly average radiances for an entire year. The default 20x20 lat x lon will produce 9 lat bands x 18 lon bands.
7. In `_AIRS6_.ipynb` set the `folder_AIRS_20x20`, `folder_LBL_20x20`, and `folder_LBL_cfc` to their respective locations. The `AIRS_ir` and `AIRS_vis` files should be placed in the same folder (see download links below). Then, run all cells to generate the figures.

### Download Links for AIRS granule in Fig 1:
[https://airsl1.gesdisc.eosdis.nasa.gov/data/Aqua_AIRS_Level1/AIRIBRAD.005/2021/099/AIRS.2021.04.09.121.L1B.AIRS_Rad.v5.0.25.0.G21100105112.hdf]
[https://airsl1.gesdisc.eosdis.nasa.gov/data/Aqua_AIRS_Level1/AIRVBRAD.005/2021/099/AIRS.2021.04.09.121.L1B.VIS_Rad.v5.0.0.0.G21100104903.hdf]
