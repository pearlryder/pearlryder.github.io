---
title: Documentation for the RNA localization pipeline
layout: page
---

# Software and package installation

*We assume that you will need to install software packages necessary for using the Allen Institute Cell Segmenter and present an abridged version of their installation process. See [their website](https://github.com/AllenInstitute/aics-segmentation) for up-to-date installation instructions.*

## Step 1: Install conda and the Allen Institute Cell Segmenter
#### 1.1 Anaconda installation
We use conda to manage the installation of python. You can download and install the latest version of Anaconda at [the Anaconda website](https://www.anaconda.com/). You'll note two versions are available - Anaconda is a full installation pre-loaded with many commonly used packages for data science, whereas miniconda is a stripped down version that saves on disk space. If you have the space, we recommend installing Anaconda to reduce the number of packages you need to load manually. Once you've installed conda, you can test if conda is active by typing the following command into your terminal window.

```bash
conda info
```

In general, grey-blocked text indicates commands for the terminal. It's also a good idea to update your conda once you've installed it using `conda update --all`.

#### 1.2 Test your pip installer
[pip](https://pip.pypa.io/en/stable/) is a package installer for Python. You can think of packages as discrete blocks of Python code that do specific tasks. Make sure you have pip installed by running

```bash
pip show pip
```

If any warnings appear, follow the screen instructions to update your pip installer.

#### 1.3 Create a conda environment for segmentation
Create a new empty conda environment. This environment will contain all the Python packages necessary to segment images using the Allen Cell Segmenter. By using separate environments to run different code, we can avoid conflicts between packages (for example, if our segmentation code requires a version of a package that is in conflict with code needed for visualization, we can run the respective code from different environments).

```bash
conda create -n segmentation python=3.7
```

#### 1.4 Install git
Git is a system for tracking changes to versions of code. You can use git to track the changes to  your own code (recommended) and to manage accessing the code for this pipeline and the Allen Institute Cell Segmenter. Check your own git installation:

```bash
git --version
```

If you're missing git, you can follow [this tutorial](https://www.atlassian.com/git/tutorials/install-git) for installation.

#### 1.5 Install packages into the segmentation environment
First, you'll want to activate your conda environment:

```bash
conda activate segmentation
```

Next, run the following commands to install the Allen Cell Segmenter and other necessary packages.

```bash
pip install numpy
pip install itkwidgets==0.14.0
conda install -c anaconda psycopg2
conda install -c anaconda sqlalchemy
pip install aicssegmentation==0.01.17
conda install seaborn
```

#### 1.6 Download the Allen  Cell Segmenter to a local folder
I've found that installing the cell segmenter using pip as described above is the most error-free way of installation for using the batch processor. However, it can be useful to download the cell segmenter from github in order to access their Jupyter notebooks and modify batch processing scripts locally. The following code creates a folder in your home directory named Projects and then downloads the Allen Cell Segmenter into that folder.

```bash
mkdir ~/Projects
cd ~/Projects
git clone https://github.com/AllenInstitute/aics-segmentation.git
cd ~/Projects/aics-segmentation
```

#### 1.7 Test Allen Institute Cell Segmenter installation
Finally, you can test to see if your installation has worked by starting Jupyter notebook and opening the test_viewer.ipynb notebook.

```bash
conda activate segmentation
cd ~/Projects/aics-segmentation/lookup_table_demo
jupyter notebook
```
Use the "Run" button to advance through the cells. You can use the Allen Cell Segmenter to segment your objects of interest. We refer to their [excellent tutorials and documentation](https://github.com/AllenInstitute/aics-ml-segmentation/blob/master/docs/demo_1.md) for how to proceed. Your goal will be to have a folder of single-channel tif files containing the original images for your structure of interest and a corresponding folder of segmentations for those same images.

Note that we also provide an optimized Jupyter notebook and batch processing code for segmenting single molecule FISH data that works well for our images of *Drosophila* embryos.

#### 1.8 Download RNA localization code
Finally, you want to download the code associated with the RNA localization pipeline from GitHub. The following terminal commands assume that you made a ~/Projects directory in step 1.6. You may choose to change this filepath.

```bash
cd ~/Projects
git clone https://github.com/pearlryder/rna-localization-pipeline.git
cd ~/Projects/rna-localization-pipeline
```

#### 1.9 Download test dataset
We'll need to have a link to download the test dataset.

#### 1.10 Download Fiji and Atom text editor
We recommend using [Fiji](https://fiji.sc/) to visualize your images and segmentations. If you don't already have it, you'll want to download and install it.

If you don't have a preferred text editor, you may want to download and install the [Atom editor](https://atom.io/). We find that it's user friendly and makes editing code more straightforward than using the pre-installed text editor.

## Step 2: Organize data and segment images
For this pipeline, we recommend the following organization for your data. One directory contains a folder for each subcellular structure of interest. We recommend naming your single-molecule FISH data "rna." In general, avoid naming structures with upper-case letters, numbers, or special characters - these don't play well with the database that you'll use later. Each structure folder should contain two folders, one for the raw data and one for the segmentations.

For example, we provide a data folder with sub-folders named rna and centrosomes. Each folder contains two folders, one named raw-data and and one named segmentations. Each of these folders contain single-channel .tif files for the structures of interest. An ImageJ macro can be helpful for converting from multi-channel proprietary formats into single-channel tifs and saving them to the raw-data folder for you automatically. The segmentations folders are currently empty (you will generate these segmentations using the Allen Cell Segmenter).

You likely want to use our pipeline to compare two (or more) biological conditions. We recommend creating a key for yourself to keep track of the associated experimental conditions for each image (we provide an example, raw-data-metadata.csv). This approach allows you to combine images from multiple replicates and conditions into the same folder, so you can be confident that you're applying the same process to each biological condition. We recommend including a column named 'rna_type' that contains a short text description of the rna you're investigating in all lowercase.

As you'll see in their tutorials, the Allen Cell Segmenter approach provides Jupyter notebooks called "playgrounds" to interactively visualize your segmentation workflow and optimize the parameters for your workflow. We provide two playgrounds for you to use with our test data: one optimized for segmentation of RNA (smFISH signals, in particular) and one optimized for segmentation of centrosomes. If your smFISH signals are similar to ours, you may find starting with our workflow helpful.

#### 2.1 Optimize parameters using Jupyter notebook playgrounds
For the purpose of this documentation, we'll walk through the process of segmenting smFISH signals. The process is the same for segmenting the centrosome data in our test dataset, you'll just need to use the corresponding Jupyter notebooks and batch processing scripts.

Start by opening the playground_rna.ipynb Jupyter notebook.

```bash
cd ~/Projects/rna-localization-pipeline/optimized-segmentation-notebooks
jupyter notebook
```
Step through each cell of the playground_rna Jupyter notebook. These notebooks are interactive and allow you to visualize how each step of the workflow contributes to the final segmentation. We encourage you to adjust parameters and see how the output changes. For example, if the gaussian smoothing sigma starts at 1, try 0.1 and 5 to see how these changes affect the smoothing process. You can save final segmentations using the final cell. These will automatically save into a sub-folder named test-segmentations, that is already created for you. You may also want to save intermediate segmentations, such as the bw_extra segmentation from the 3D spot filter. In order to do this, you would replace the variable 'seg' in the final cell with 'bw_extra' and re-name the output name. For example, from:
```bash
seg = seg >0
out=seg.astype(np.uint8)
out[out>0]=255
writer = omeTifWriter.OmeTifWriter('test-segmentations/' + FILE_NAME + '_test_rna_segmentation.tiff')
writer.save(out)
```
to
```bash
bw_extra = bw_extra >0
out=bw_extra.astype(np.uint8)
out[out>0]=255
writer = omeTifWriter.OmeTifWriter('test-segmentations/' + FILE_NAME + '_test_rna_bw_extra.tiff')
writer.save(out)
```
Downloading images is useful, because I've found that I can better assess the quality of segmentation using Fiji. I like to open the original image and a segmentation file in Fiji and then use the Sync Windows function to directly compare the images. For smFISH signals, you want to assess the following questions:
* Are single molecules of RNA captured?
* Is the background appropriately excluded?
* If you have larger granules, as we do in one of our biological conditions, are those granules segmented appropriately?

Note that no segmentation process will ever be perfect. We refer you to this [excellent article](https://blog.cellprofiler.org/2019/10/21/when-to-say-good-enough/) by Beth Cimini, a senior computational biologist in the Imaging Platform led by Anne Carpenter at the Broad Institute, for a philosophical discussion of when to call a workflow "good enough." Remember, you want to robustly quantify the difference between two biological states as accurately as feasible, while recognizing that discovering the "universal truth" of your biological process is impossible.

Continue this process until you're satisfied with the segmentation process. We recommend walking through at least two images from each biological condition to test your workflow before moving to the next step -- batch processing.

#### 2.2 Batch process image segmentation
Once we're satisfied with my workflow, we use the Allen Cell Segmenter's batch processing mode to segment our entire dataset. We then review the results of that segmentation for each image in Fiji (or a representative subset of your images if your dataset is really big).

If you changed the parameters in the Jupyter notebook for RNA segmentation, then you'll first want to update the batch processing file for RNA named "seg_rna.py" and located in the "batch-processing-scripts" folder. Open the file using a text editor. Update the code using copy-paste so that the pre-processing, processing, and post-processing steps reflect the workflow you optimized in step 2.1. [This tutorial](https://www.youtube.com/watch?v=Ynl_Yt9N8p4) may be helpful.

Once you've updated your seg_rna.py code, you'll need to copy it into the folder that contains the Allen Institute Cell Segmenter in your conda environment for segmentation. The easiest way we've found to identify where my anaconda environment is stored on my computer is to search for a batch processing workflow using:
```bash
 mdfind seg_cetn2.py
```
You'll see something like:
```bash
/Users/pearlryder/Projects/aics-segmentation/aicssegmentation/structure_wrapper/seg_cetn2.py
/Users/pearlryder/opt/anaconda3/envs/segmentation/lib/python3.7/site-packages/aicssegmentation/structure_wrapper/seg_cetn2.py
/Users/pearlryder/opt/anaconda3/envs/segmentation/lib/python3.7/site-packages/aicssegmentation-0.1.17.dist-info/RECORD
/Users/pearlryder/Projects/aics-segmentation/aicssegmentation.egg-info/SOURCES.txt
```
This output signifies that I have two locations for the Allen Institute Cell Segmenter batch processing workflows: one located at  ```/Users/pearlryder/Projects/aics-segmentation/aicssegmentation/structure_wrapper/seg_cetn2.py``` and one at ```/Users/pearlryder/opt/anaconda3/envs/segmentation/lib/python3.7/site-packages/aicssegmentation/structure_wrapper/seg_cetn2.py```. As you can see, the anaconda3 path is the one we're looking for. Now, you can copy your seg_rna.py code into the structure_wrapper folder in the anaconda3 environment:

```bash
cp ~/Projects/rna-localization-pipeline/batch-processing-scripts/seg_rna.py ~/opt/anaconda3/envs/segmentation/lib/python3.7/site-packages/aicssegmentation/structure_wrapper/
```

Once you've copied your batch processing code into the anaconda folder, you're ready to batch process your images. The following code should work, although you may need to change the input and output_dir paths, depending on where your raw data is stored.

```bash
batch_processing --workflow_name rna --struct_ch 0 --output_dir ~/data/rna/segmentations per_dir --input_dir ~/data/rna/raw-data --data_type .tif
```

If all goes well, you should see printed updates on the image normalization process and the output directory will start to fill up with segmented files. These usually take ~ 10 minutes to run, depending on the number of images you're segmenting and your computer. You can start comparing the segmentation output to your original files during this time - or take a coffee break.

We often find that once we batch process the images, we need to tweak our parameters. You can do this by directly modifying your seg_rna.py file and repeating the batch processing or by returning to the Jupyter notebook. If you edit the seg_rna.py file that's located in your rna-localization-pipeline folder, you'll need to repeat the copy command above to update it in the anaconda3 folder. Note that the segmenter will not overwrite files, so you'll either want to provide a new directory for the output or delete previous segmentations if you're repeating the batch processing.

Once you're satisfied with the segmentations for your smFISH signal, you'll want to repeat the same process for your second structure of interest. If you're using our test-data to learn the workflow, you can repeat the steps above using the playground_centrosomes.ipynb Jupyter notebook and the seg_cent.py workflow. Here's an example of the command to copy the seg_cent.py batch processing workflow into your anaconda folder:

```bash
cp ~/Projects/rna-localization-pipeline/batch-processing-scripts/seg_cent.py ~/opt/anaconda3/envs/segmentation/lib/python3.7/site-packages/aicssegmentation/structure_wrapper/
```

 Note that when you run the batch processing command, you'll need to update the workflow name and the input and output directories, e.g.:

```bash
batch_processing --workflow_name cent --struct_ch 0 --output_dir ~/data/segmentations/centrosomes per_dir --input_dir ~/data/raw-data/centrosomes --data_type .tif
```

## Step 3: Set up database server
#### 3.1 Install postgres
First, we need to set up a postgres server and database for this experiment. We use one database to contain all of the data for a given experiment, including multiple replicates and different biological conditions that are being compared.

To install postgres, you want to activate your segmentation environment and then run the postgres installation command:

```bash
conda activate segmentation
conda install -c anaconda postgresql=='11.2'
```

#### 3.2 Initialize a postgres server
The next step is to create folders for your database server information and a place to hold your collection of databases. You'll be prompted to enter your system password. Be sure to update your username in the commands below. If you're not certain of your username, you can use the whoami command to find out:

```bash
sudo mkdir -p ~/usr/local/pgsql/data
sudo chown username ~/usr/local/pgsql/data
sudo chmod -R 700 ~/usr/local/pgsql/data
pg_ctl -D ~/usr/local/pgsql/data initdb
```

If everything works, you'll see a printout to your terminal window indicating the successful initiation of your database cluster. Now you can start your server using the following command. Note that you may need to change the path to your anaconda installation of postgres - the terminal window should have this command printed out. Note this process creates a logfile in your Projects/rna-localization directory, which you can use to debug any errors:

``` bash
~/opt/anaconda3/envs/segmentation/bin/pg_ctl -D ~/usr/local/pgsql/data -l ~/Projects/rna-localization-pipeline/logfile start
```

#### 3.3 Create a database for your data
Now we'll create an initial database. This database will be named for your username and is the starting point for creating other databases. We'll first create a database for your username with the createdb command, then create a database called "demo" for the tutorial data.

``` bash
createdb
createdb demo
```

#### 3.4 Interact with your data using the postgres command line utility
Finally, you can interact with your databases using SQL commands from a terminal window by activating the postgres command line utility. You may want to open a new terminal window that you use for this purpose exclusively. If you do, you'll need to be sure to activate your conda environment before running the following commands. The first command launches the postgres command line utility, while the second command is an SQL command that connects you to the demo database:

``` bash
psql
\c demo
```

When you're done interacting with your databases using SQL, you can stop postgres command line utility by typing ```quit```

#### 3.5 Stop your server when you finish
It's good practice to stop the server when you're done:

``` bash
pg_ctl stop -D ~/usr/local/pgsql/data
```
You're now ready to extract information from your segmented images! The standard way that we'll execute this code is from Jupyter notebooks, unless you prefer to run the code on a virtual cluster.

## Step 4: Run the RNA localization pipeline
#### 4.1 Open the pipeline.ipynb jupyter notebook
Navigate in the terminal to your rna-localization folder and launch Jupyter notebook. Note the command below is specialized to allow for the processing needed for distance measurements.

```bash
cd ~/Projects/rna-localization-pipeline
jupyter notebook --NotebookApp.iopub_data_rate_limit=100000000
```

Launching this script should open your browser window. Navigate to 'pipeline-notebooks' and open the 'pipeline.ipynb' notebook.

#### 4.2 Update parameters in cell 1
The first step for this pipeline is to update your parameters. You named your database in step 3.3 above. Your database username is probably your system username, unless you specified something different in the step above. Your password will probably be an empty string and the host name should be "localhost". You will want to be sure that you've started your postgres server, so that Jupyter notebook can communicate with your database.

Next, update the path to find the raw data and segmented images. You'll also need to update the directory names for your raw data and segmentations.

The default output for the Allen Institute Cell Segmenter adds a suffix to the end of the file names. The localization pipeline can accommodate if your segmentation files have a different suffix than your raw data files. Update the segmentation_file_suffix variable accordingly. If your segmentation files have the same name as the raw data, then change this variable to an empty string ('').

Finally, update the scale parameters for your pipeline. The pipeline uses these scales to convert from pixels to microns for the distance measurements. Note that the "z_scale" is the your step size in the z dimension.

#### 4.3 Extract basic object data
The next step is to extract intensity data for each object and store it in the postgres database. The first cell in this section loads the packages and functions necessary for object extraction.

Next, tables are created in your database to hold the data for each structure of interest. The tables will be named based on the names of your structures that you defined in Step 4.2. Remember that those names should correspond to the name of the folder containing the raw-data and segmentations for that structure.

The third cell navigates through your data and extracts intensity information for each subcellular structure of interest. That data is then inserted into the structure table in the your database.

After running the third cell, you should see a printout indicating the image name and its status.

Note that data are not reprocessed - if the database already contains object data for a structure for a given image, the image is skipped. If you need to delete object data for a structure in a particular image, use the `delete_data_db(image_name, structure, conn)` function

In a new cell, you would run:
```bash
from pipeline import delete_data_db
conn = psycopg2.connect(database=database_name, user=db_user, password=db_password, host=db_host)
image_name = ''
structure = ''
delete_data_db(image_name, structure, conn)
conn.close()
```
Update the image_name and structure variables with the respective image names and structures.

You can check to see if your database was updated using the command line utility in terminal.

```bash
psql
\c demo
\dt
```

The `\dt` command will show you the tables contained in your database. You should see tables for each of your subcellular structures. From there, you can select some data from each structure table to verify that it was properly inserted:

```bash
SELECT * from structure_table_name limit 20;
```


#### 4.3 Measure distances between two subcellular structures
Our approach is to first extract intensity data about all subcellular structures (Step 4.2 above) prior to measuring distances between select pairs of subcellular structures. For the distance measurements process, you'll need to update the variables for the parameters.

The structure_1 variable sets your "objects of interest." For each of these objects, the distance to the nearest structure_2 object and the id of that object will be measured and updated in the database. In general, RNA objects will be your "objects of interest" and the structure_1 variable should be set to 'rna'. The structure 2 variable is set to the target structure, such as the centrosome or the nucleus.

The next two variables determine how the processing proceeds. As a default, the pipeline will check the database to see if distance measurements have already been made for an image. If not, distance measurements proceed and the database is updated. If the database already contains distance_to_structure_2 data for all of the structure_1 objects, then the image is not processed. If you want to overwrite data, then change the value of the overwrite_data_bool variable to False. If your computer has multiple cores, then you may want to take advantage of parallel processing to decrease the total time to process your images. To do so, set the parallel_processing_bool variable to True.

After package import, the following cell creates columns in your structure one table to hold your distance_to_structure_2 and structure_2_id data. These columns will be named "distance_to_" + "your_structure_2_name" and "your_structure_2_name" + "\_id". For example, if you are using our demo dataset, then your rna table will contain two new columns named "distance_to_centrosomes" and "centrosomes_id" after running this cell. These columns are only created if they do not already exist (data is not overwritten, as discussed above).

To check that your columns were created properly, return to the terminal and connect to your database. The run the SQL command below to select data from your rna table:

```bash
psql
\c demo
SELECT * from rna limit 5;
```

You should see two new empty columns ready to accept your distance and structure_2_id data.

Once you've verified that the database is ready to accept distance measurements data, run the next cell to measure distances between the two subcellular structures. Our test dataset of 20 images takes about 9 hours to run on a MacBook Pro with a 12-core processor using parallel processing. If the processing time is too slow on your computer, then processing on a virtual machine using Amazon Web Services may be an option you want to explore (in development).

#### 4.4 Create an images table
Your raw-data images folder likely contains images of a control RNA or control biological condition together with images from your experimental condition. You may also have multiple biological replicates or images from different biological conditions, such as developmental stages. You can use postgres to help track where each image comes from -- e.g. is it an image of control RNA or an experimental RNA?

We recommend storing these data in a .csv file. We provide an example .csv file `raw-data-metadata.csv` with our sample dataset. You can create this file for your own experiment using Excel and then "Save As" .csv (you specifically want the "Comma Separated Values" file type and not the "CSV UTF-8" file type). The first row of your .csv file should contain labels that will become column names when you upload this data to postgres.

We recommend including a column named 'rna_type' in your csv file that contains a short text description of the rna you're investigating in all lowercase. You may have multiple RNA types within a given experiment. For example, we often have an experimental RNA and the control RNA gapdh (a metabolic enzyme that is dispersed in *Drosophila* embryos).

In the first cell of this section, you'll want to update the filepath to the directory that contains your .csv file. If you are using our sample dataset, then this directory is the same directory that contains your images. Also update the name of your csv file in this cell.

The third cell opens your csv file and prints out the column names for you. This printout is intended to help you update the SQL query in the following cell. That SQL query creates a new table in your database named 'images'. The text contained in parentheses

``` bash
(name TEXT NOT NULL,
rna_type TEXT,
cycle TEXT,
stage TEXT,
replicate INT)
```

describes the columns that will be contained in this new table. You will need to update this command to have the columns match the columns in your csv file. The uppercase word that follows the column name indicates the data type for the column. For example the command `name TEXT NOT NULL` creates a column in the table named 'name' that contains text data and cannot be null (a good idea for the name column, since this column is how we will match objects in the structure tables with metadata about each images). To learn more about SQL data types, you can visit [this website](https://www.postgresqltutorial.com/postgresql-data-types/). Useful datatypes include TEXT (variable-length character string), INT (integer), and REAL (non-integer numbers). After you edit the query and run this cell, the updated query will be printed for you to verify that you've edited it properly. Note that this command will not overwrite a table if it already exists ("CREATE TABLE IF NOT EXISTS").

The last cell in this section copies data from your csv file into your new images table. An additional command (add_id_column) adds an id column of integers that autopopulates incrementally. Note that this cell can only be run once because after you've created an id column, then the columns in your images table will no longer match the columns in your csv file. This approach is useful to prevent overwriting of your images table.

If you need to overwrite the data in your images table, then it's probably best to connect to your postgres database using the command line utility to then drop the images table and start again. For example:

``` bash
psql
\c demo
DROP TABLE images;
```

As long as you have your csv file, it should be easy to recreate the images table. You can check your images table for data upload in the terminal using:

```bash
psql
\c demo
SELECT * from images;
```

#### 4.5 Normalize RNA to estimate number of single molecules per object

This section estimates the number of RNA molecules per object for each of the RNA types in your experiment (as described by your rna_type column in the images table). The logic of this method is that it estimates the integrated intensity (also called the total intensity in the database) of small objects for a given RNA. These small objects are identified using an area threshold. For our data, we opened several representative images of smFISH data imaged at 100x on our spinning disk confocal microscope in Fiji and measured the size of individual molecules of RNA in pixels. Fiji's oval tool is helpful to measure the diameter of single molecules in multiple z slices. These measurements can help you estimate the number of pixels that represent one molecule of RNA and establish the upper and lower area parameters for your own data.

The first cell requires you to define parameters for the name of the table that contains your RNA data. This cell is where you define the upper and lower thresholds for your small objects and the names of your RNA types. After importing packages, the next cell adds columns to your RNA table to hold the normalized intensity data.

The following cell contains an SQL query that calculates the average integrated intensity of RNA objects within the area parameters that you previously defined (lower_threshold and upper_threshold) for each RNA type in your experiment. For all RNA objects of that RNA type, the integrated intensity is then divided by this small object average and saved in your normalized_intensity column.

Once you run this code, you can connect to your database using the command line utility to check if you now have a column named "normalized_intensity" that has been updated. As you see below, we check this for both of the RNA types in our test dataset.

```bash
psql
\c demo
SELECT * from rna WHERE rna_type = 'cen' limit 10;
SELECT * from rna WHERE rna_type = 'gapdh' limit 10;
```

#### 4.6 Calculate the cumulative percent distribution of RNA relative to distance from subcellular objects of interest
We find that cumulative distribution profiles are very powerful to visualize differences in the spatial distribution of RNAs relative to subcellular objects of interest. This method calculates the percentage of total RNA at different distances from the subcellular object of interest.  

The first step is to update the parameters. The structure_1 and structure_2 parameters are the same parameters that were used in the distance measurements section. In that section, you created a column in the structure_1 table called "distance_to_structure_2" containing a distance in microns for each structure_1 object. The "image_name_column" parameter is the name of the column in your images table that holds the image names data. If you're uncertain of what you named this column, you can either check section 4.4 (where you created this table) or you can run the 4th cell in this section, which will print the names of the columns in your images table.

You'll also need to decide if you want to set a threshold for how far an RNA object can be from a structure_2 object and still be considered a "true" RNA object. For example, our data from early embryos often contains RNA objects that are outside of cellular structures and represent background data. The precise and consistent geometry of the early embryo allows us to set an upper limit depending on developmental stage for how far away from a centrosome an RNA object can be. Setting this threshold helps our plots to appear consistent between images, but we've noted that it doesn't affect our conclusions. If you do not want to set such a threshold, you can define ```distance_threshold = None``` and the pipeline will calculate the cumulative percent of total RNA up to the maximum distance_to_structure_2 for each image.

Next, you'll need to define the step_size parameter. This parameter sets the interval size for calculating the percentage of RNA. For example, if you set step_size = 0.05, then the pipeline will calculate the percentage of total RNA and the percentage of total RNA in granules that localizes within 0, 0.05, 0.10, 0.15 microns up to your distance threshold.

Finally, you will need to define the directory where you want to save .csv files of the calculated distribution data. By default, we choose to save all output to a folder within your data directory named "output". Within the output folder, the pipeline creates a "data" folder to hold these files. This directory is printed out to help you find the .csv files. The next cell creates the output and data folders if they don't already exist.

The following cell accesses the images table, automatically pulls all of the metadata for each image, and processes it into a useful format. In doing so, the distribution data will be connected to each image's metadata, which will allow you to compare the RNA distribution for the biological conditions that you describe using the images table. This cell also prints out the names of the columns in your images table, to help you remember the name of the column containing the names of your images.

The following cell calculates the cumulative distribution of RNA and the cumulative distribution of RNA in granules and returns the data in a dataframe (similar to a spreadsheet). Note that it will likely take a few minutes to run this cell. A status update will be printed as it calculates the distributions for each image.

Once the distributions are calculated, the first ten rows of the RNA distribution data will be displayed using the following cell. You can then save this data as a .csv file in the following cell. By default, data will not be overwritten. You can change the name of your output file using the "distribution_data_filename" variable, if desired.

#### 4.7 Plot cumulative distribution data
In this section, we provide examples of how to plot the RNA distribution data using the Seaborn library. Our method allows you to create line graphs plotting the cumulative distribution of RNA relative to the distance from your subcellular structure of interest. Note that there are many options for subsetting your data according to different variables from your images table, many of which we can't anticipate. We refer you to the excellent [Seaborn tutorials](https://seaborn.pydata.org/tutorial.html), which can help you customize your plots.

As usual, the first cell of this section imports the necessary packages. The next cell is where you adapt the parameters for your data. The `distribution_data_filename` variable is the name of the .csv file that contains the data for cumulative RNA distributions, which was calculated and saved in section 4.6. `data_output_dir` is the path to the directory where the distribution data file is stored. This directory will also be used to save csv files containing subsets of your RNA distribution data, as discussed below. Finally, you can specify a directory where you would like to save the plots that you generate in this section. By default, these plots are saved in the output folder, under a sub-directory named plots. The next cell creates this plots directory, if it does not already exist.

In the fourth cell of this section, your RNA distribution file is loaded into a pandas dataframe. In essence, dataframes function much like spreadsheets. You can learn more at [the pandas website](https://pandas.pydata.org/). The first 10 rows of your dataframe are shown in the output window. Verify that you see the expected columns and that the distances increase in the predicted increments.

Next, we generate a [Seaborn lineplot](https://seaborn.pydata.org/tutorial/relational.html#emphasizing-continuity-with-line-plots) that shows the percentage of total RNA relative to distance from the centrosome. The mean percentage of RNA is shown as a dark line with shading to indicate the standard deviation at each distance measurement. These calculations were made on a per image basis in the previous section. As you can see, we use the `rna_type` variable to separate the data according to the RNA type, allowing us to compare the distribution of centrocortin RNA relative to gapdh RNA in our demo dataset. We also use the `cycle` variable to separate the data according to cell cycle in two different columns.

We encourage you to explore the power of Seaborn plotting. What happens if you change `col = 'cycle'` to `row = 'cycle'`? Do you have an additional variable that you'd like use for plotting? For example, one can imaging having both an `rna_type` variable and a `genotype` variable that are of interest. Perhaps you are investigating the distribution of an RNA type that you predict to mislocalize in a mutant genotype relative to a RNA type that you predict will not be affected by the mutant genotype. In that case, you could continue to use `hue = 'rna_type'` and `col = 'cycle'`, while adding in a `row = 'genotype'` parameter. The plotting methods are very flexible, but do require an investment to learn.

Now that you've created a plot, we provide a function for you to save your plots. By defining a function, you can save all your plots using the same parameters. We use matplotlib's [savefig function](https://matplotlib.org/3.1.1/api/_as_gen/matplotlib.pyplot.savefig.html), which has several optional parameters that you can adjust as appropriate for your work. By default, we set `dpi = 600` and `format = 'pdf'`, which we've found work well for building figures.


#### 4.8 Save .csv files of your raw object data
As a precaution, we recommend saving the raw data from your database into .csv files. We know it can be reassuring to have a backup of your data that can be viewed using standard text editors rather than a specialized program like postgres. The last step in the RNA localization pipeline includes a utility to automatically save all the the

## Step 5: Save save save your data
Finally, we'll tackle saving backup files of your database and how to restore the data into a new postgres database. These approaches are useful to have confidence that your data is secure and may help you run this code on virtual machines, such as using Amazon Web Services.

```bash
pg_dump database_name | gzip > filename.gz

```

recreate the database
```bash
createdb new_database
gunzip < filename.gz | psql new_database   
```
