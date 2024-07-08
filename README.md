# ASA3P_Singularity
This repository contains the files required to utilize the ASA3P pipeline as a singularity image without need for root permissions.

The main ASA3P pipeline can be found at: [https://github.com/oschwengers/asap/tree/master].

The updated files are as follows:
  1. A recipe to build the singularity image based on the ASA3P dockerfile. `ASAP_Singularity_recipe`
  2. The scaffold .fasta file for _Listeria monocytogenes_ SRR3330409, produced by the ASA3P pipeline in the scaffolding step. `Listeria_monocytogenes_SRR3330409.fasta`
      * This is required as input during the singularity build step to initialize the CARD database properly for the rgi conda enviornment.
  4. An updated version of the PROKKA perl script to ignore the version check for BARRNAP. It is assumed that the BARRNAP version (here 0.8) will be compatible with PROKKA 1.12. `prokka`
  6. An updated bash script to initialize the ASA3P pipeline using the `singularity run` command. `asap-singularity.sh`
  7. An updated groovy script for the ABR step to utilize the `rgi` conda environment instead of initializing a secondary singularity container. `asap-abr.groovy`
  8. An updated `aro.json` file (version 3.2.9) downloaded from the Comprehensive Antibiotic Resistance Database at [https://card.mcmaster.ca/download]. `aro.json`

In order to recreate the singularity image please follow the steps below:
  1. Choose a location for the ASA3P pipeline, make a dedicated ASA3P directory and enter it.
```
     $ cd /your/location/here
     $ mkdir ./ASAP
     $ cd ./ASAP
```
  2. Download the ASA3P files according to the setup instructions previously provided at the main ASA3P pipeline github page:
```
    $ wget https://zenodo.org/record/3780003/files/asap.tar.gz
    $ tar -xzf asap.tar.gz
    $ rm asap.tar.gz 
```
  3. Download the updated files:
```
    $ wget https://github.com/jp589/ASA3P_Singularity/archive/refs/heads/master.zip
    $ unzip -j master.zip
    $ rm master.zip
```
  4. Move files to their respective locations:
     * `ASAP_Singularity_recipe` and `Listeria_monocytogenes_SRR3330409.fasta` should remain in the current directory.
```
    $ mv prokka ./asap/share/prokka/bin/prokka
    $ mv asap-singularity.sh ./asap/
    $ mv asap-abr.groovy ./asap/scripts/
    $ mv aro.json ./asap/share/card/
```
  5. Build the Singularity image remotely:
     * Note that in order to build remotely, a free Singularity account must be created and an access token generated (For further details: https://docs.sylabs.io/guides/3.1/user-guide/cli/singularity_remote_login.html).
     * While the previous docker image utilized Ubuntu 16.04, this Singularity image needed to be based on Ubuntu 18.04 to have access to Python 3.6 which was required for the rgi conda environment.
     * With the addition of the conda environment, this image is nearly 1.9Gb so the build can be expected to take ~26 minutes.
```
    $ singularity remote login
    $ singularity build --remote asap_latest.sif ASAP_Singularity_recipe
```
  6. Download the example files (optional, but recommended):
```
    $ wget https://zenodo.org/record/3606761/files/example-lmonocytogenes-4.tar.gz
    $ tar -xzf example-lmonocytogenes-4.tar.gz
    $ rm example-lmonocytogenes-4.tar.gz
```
  6. Run the pipeline:
      * All options, parameters, and required input files remain the same as detailed on the main github page [https://github.com/oschwengers/asap/tree/master].
```
    $ ./asap/asap-singularity.sh -p example-lmonocytogenes-4/ -s /tmp
```


