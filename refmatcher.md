# refmatcher

Simple linux/Ubuntu reference matcher using NIST tools

## Installing and Running with Python
Requires Python version 3.5 or higher with pip3.

If Python 3.5 is not installed, execute the script:

```
sh install_python_3.5_ubuntu_14.04.sh
```

To install dependencies:

```
sudo pip3 install -r requirements.txt
```

## Running compiled Linux version

Extract both gzip files
```
tar zxvf fencode.tar.gz
tar zxvf fmatcher.tar.gz
```

To run the executable, you have to change directory to fencode or fmatcher first. For example:
```
cd /path/to/fencode
./fencode --threads 2 --noImages --ext eft --fte_file fte2.csv /path/to/gallery_dir
./fencode --threads 2 --noImages --ext eft --fte_file fte2.csv /path/to/probes_dir
```
And for fmatcher:
```
cd /path/to/fmatcher
./fmatcher fmatcher.py --ext eft --threads 2 --out /path/to/result.csv /path/to/gallery_dir /path/to/probes_dir
```
Both **fencode** and **fmatcher** arguments explanation are provided in the 2 sections below.

## Encoding NIST and Enrollment

NIST files can be in different file extension, i.e. .nist, .nst, .eft, etc.

Usage:

```python3.5 fencode.py --threads 2 --noImages --ext eft --fte_file fte2.csv /path/to/gallery_dir```

Usage details:
```
usage: fencode.py [-h] [--threads THREADS] [--ext EXT] [--noImages]
                  [--fte_file FTE_FILE] [--nist_fields NIST_FIELDS]
                  nist_dir

Reference Fingerprint Enroller using the NIST pack algorithms. Will enroll all files in the given directory. Creates a subdirectory 'templates' which contains the enrollment information. Can also create images of all the fingerprint data.
        
The default NIST fields types are 4,13,14,15, default extensions are '.nst,.nist'

positional arguments:
  nist_dir              the name of the nist dir

optional arguments:
  -h, --help            show this help message and exit
  --threads THREADS     the number of threads
  --ext EXT             the file extension
  --noImages            do not create the images for each NIST file
  --fte_file FTE_FILE   file to output enrollment failures (default fte.csv)
  --nist_fields NIST_FIELDS
                        the comma separated list of NIST field types to encode
```
Examples: 

Enroll the probes subdirectory. Do not create images (faster) and output any failures to fte2.csv

```fencode.py --threads 2 --noImages --fte_file fte2.csv probes```

## Matcher

**NOTE**: Before running the matcher, encoding script must be executed on BOTH gallery and probes directories.

Example usage:

```python3.5 fmatcher.py --ext eft --threads 2 --out /path/to/result.csv /path/to/gallery_dir /path/to/probes_dir```

Usage details:
```
usage: fmatcher.py [-h] [--record_type FIELD_NUM] [--threshold THRESHOLD]
                   [--below] [--exclude_self_match] [--out OUTPUT_FILE]
                   [--threads THREADS] [--max_check_rank MAX_CHECK_RANK]
                   [--idc IDC] [--max_rank MAX_RANK] [--ext EXT]
                   [--mates MATES_FILE]
                   gal_dir probe_dir

Reference Fingerprint Matcher using the NIST pack algorithms. Note probe and gallery directories need to be enrolled first

positional arguments:
  gal_dir               the name of the gallery dir
  probe_dir             the name of the probe dir

optional arguments:
  -h, --help            show this help message and exit
  --record_type FIELD_NUM
                        the NIST record type to match i.e. 4
  --threshold THRESHOLD
                        the threshold
  --below               Return only matches below threshold rather than above
  --exclude_self_match  Exclude any matches where the probe and gallery id are the same
  --out OUTPUT_FILE     the outputfile
  --threads THREADS     the number of threads
  --max_check_rank MAX_CHECK_RANK
                        the maxium number of top results to aggrigate
  --idc IDC             the idc's to include in the match
  --max_rank MAX_RANK   the maxium rank
  --ext EXT             the file extension
  --mates MATES_FILE    a file contains matching pairs

Examples: 
1) Using a mates file "m.csv" which contains the comma separated pairs nist filenames (no path or extension) to be matched. Note the probe and gallery directories are still required 
matcher.py --ext eft --threads 2 --mates m.csv gallery probes

2) Matching all templates in the probes directory against those in the gallery directory. Use 2 threads and output results to the res1.csv 
fmatcher.py --threads 2 --out res1.csv gallery probes
```
## refmatcher.py

This script executes the 2 scripts above, combining all the provided arguments. Example usage:

```
python3.5 refmatcher.py --noImages --ext eft --gallery_dir /path/to/gallery_dir --probe_dir /path/to/probes_dir --out results_test.csv
