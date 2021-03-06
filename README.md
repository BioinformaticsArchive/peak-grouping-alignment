# peak-grouping-alignment

This page contains the supporting files for the paper *Incorporating peak grouping information for alignment of multiple liquid chromatography-mass spectrometry datasets* by Wandy et al. (2014).

- **MW** contains the main peak alignment (matching) code for the proposed methods (MW/MWG/MWM) method. The implementation is done in Python, and the main executable script is MW.py.
- **input_data** contains the dataset used in experiments to measure performance in the paper. Further explanation can be found inside that directory. 
- **experiments** contains scripts to run the evaluation pipeline and do parameter scans. More details can be found inside that directory.

## Checking out the whole project

Use the following command to clone the whole repository. This will give you all the executables, input_data and evaluation scripts used in the paper.

    git clone https://github.com/joewandy/peak-grouping-alignment.git

## Required libraries

The program depends on the following Python library:

- Numpy
- Scipy (0.14)
- NetworkX (optional)
- BLAS/OpenBLAS/Atlas for matrix multiplication

In Ubuntu Linux, you can install them easily with the following commands:

    sudo apt-get install python-numpy python-scipy python-networkx liblapack3

On Fedora Linux, install the following:

    sudo yum install scipy python-networkx lapack-devel blas-devel python-pip

You might need to update scipy to the latest version (0.14) too. Do that from pip:

    sudo pip install --upgrade scipy

## Example usage

To get help:

    MW.py -h

To perform a simple matching using the simple (fast) approximate maximum-weighted matching (MW):

    MW.py -i /path/to/input_dir -o /path/to/output_file -dmz 0.025 -drt 100 

To perform a full matching using the (slow) Hungarian maximum-weighted matching (not evaluated in the paper):

    MW.py -i /path/to/input_dir -o /path/to/output_file -dmz 0.025 -drt 100 -exact_match

To group related peaks greedily (with 2 seconds grouping tolerance) and perform an approximate maximum-weighted matching incorporating the greedy grouping information for peaks being in the same group (MWG):

    MW.py -i /path/to/input_dir -o /path/to/output_file -dmz 0.025 -drt 100 -g -gm greedy -grt 2

To group related peaks using mixture model clustering and perform an approximate maximum-weighted matching incorporating the posterior probabilities of peaks being in the same group (MWM):

    MW.py -i /path/to/input_dir -o /path/to/output_file -dmz 0.025 -drt 100 -g -gm posterior -grt 2

## Data format

Both the input files and output file follow the format established in [SIMA](http://bioinformatics.oxfordjournals.org/content/early/2011/02/03/bioinformatics.btr051). The input files contain peak features, which are tab-separated files with the following columns:

    mass    charge  intensity   rt

E.g.

    64.0288984965439	1	254903.00386791077	1438.5521436180882
    64.52996790118753	1	14769.037754071698	1446.5771484817171
    72.54182358620366	1	218099.4595541399	1442.8666507643893
    73.04060164990388	1	2801.21866543637	1456.5747291123264

Only files within with the extension .csv, .txt within *input_dir* are considered for alignment. The output file establish the correspondence of peaks in the input file. It has the format:

    correspondence_id   originating_filename    mass    rt

E.g.

    0	synthdata_0.csv	28	93.05494095359997	1552.7644476078922
    0	synthdata_1.csv	22	93.05459668295673	1558.3487511435783
    1	synthdata_0.csv	74	158.0938159725959	904.2641474422279
    1	synthdata_1.csv	70	158.09302110253617	878.1399446798514

In this example, the two features with correspondence_id = 0 are matched together. Similarly, the last two peaks with correspondence_id = 1 are also matched together.

## Experiments

The Python scripts are wrapped inside a pipeline that loads the feature data, calls various alignment methods and calculate performance measures. More details, including ready-to-run scripts, can be found inside the [experiments](https://github.com/joewandy/peak-grouping-alignment/tree/master/experiments) folder. 
