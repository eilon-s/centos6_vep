# centos6_vep



This git will build a Singularity container with VEP (https://uswest.ensembl.org/info/docs/tools/vep/script/vep_options.html).
It also installs a plugin that is required by pVACseq.


## Instructions for building (alternatively you can pull the built version from shub - see below)

If you are working with macOS you will follow the following steps (if you are using linux/unix run steps 2-3,7-...):

1. Install a virtual machine (VM): https://www.vagrantup.com/
2. Create a working directory and cd to it. 
3. Clone this git (install git if you need to): git clone https://github.com/eilon-s/centos6_vep.git. You should see a centos6_vep directory with a Singularity file (and other files) in it. 
4. Initialize the VM machine:  
```
vagrant init singularityware/singularity-2.4
```
5. Activate the VM
```
vagrant up
```
6. Now you will need to ssh to the VM in order to build the singularity image file in it
```
vagrant ssh
```
7. Now you are working in the virtual machine. The current directory in the host is mapped to /vagrant. So lets cd to the directory in which you have the Singularity file of this git:
```
cd /vagrant/centos6_vep
```
8. Finally, we are ready to build the Singularity container. This may take few hours. To build the container:
```
sudo singularity build ensembl-vep Singularity
```
9. scp the ensembl-vep to some working directory on your cluster


## Pull from shub
```
singularity pull --name ensembl-vep shub://eilon-s/sherlock_vep
```
Now you should have an ensembl-vep file in your current directory. 


## install a genome

Lets use it to install data in some location (here I am using Sherlock's $SCRATCH but you can replace by any directory). We are binding it to a directory that exists in the container for it to work.
```
mkdir -p $SCRATCH/.vep;
singularity run --app install_vep --bind $SCRATCH/.vep:/home/vep/.vep ensembl-vep -a cf -s Saccharomyces_cerevisiae -y R64-1-1 -c /home/vep/.vep;
```
and maybe also human data:
```
mkdir -p $SCRATCH/.vep;
singularity run --app install_vep --bind $SCRATCH/.vep:/home/vep/.vep ensembl-vep -a cf -s homo_sapiens -y GRCh37 -c /home/vep/.vep;
```
After that you should have the data on the cluster at the directory that you have provided (here: $SCRATCH/.vep).
Since this data is not part of the container you will have to bind the data directory when you run vep by adding:
```
--bind $SCRATCH/.vep:/home/vep/.vep
```


## running on Stanford's Sherlock cluster:

1. In order to use Singularity on Sherlock you need to load these modules:
```
module load system;
module load singularity/2.4
```
2. Now you can make sure it is running using:
```
singularity run ensembl-vep --help
```
3. In order to run actual VCF files you need to bind your current directory (though in sherlock this is done by default, non sherlock users should add ```--bind $PWD:$PWD```). Assuming that you have inputfilename.vcf in your current directory, here is a possible command line:
```
singularity run --bind $SCRATCH/.vep:/home/vep/.vep ensembl-vep -i ./inputfilename.vcf -o ./outputfilename --cache --assembly GRCh37 --dir_cache /home/vep/.vep --dir_plugins /home/vep/.plugins --offline --format vcf --vcf --symbol --plugin Downstream --plugin Wildtype --terms SO
```

## VEP command line help:

For vep full help see - https://uswest.ensembl.org/info/docs/tools/vep/script/vep_options.html.
This is the short help output:

```
./ensembl-vep 
#----------------------------------#
# ENSEMBL VARIANT EFFECT PREDICTOR #
#----------------------------------#

Versions:
  ensembl              : 91.18ee742
  ensembl-funcgen      : 91.4681d69
  ensembl-io           : 91.923d668
  ensembl-variation    : 91.c78d8b4
  ensembl-vep          : 91.3

Help: dev@ensembl.org , helpdesk@ensembl.org
Twitter: @ensembl , @EnsemblWill

http://www.ensembl.org/info/docs/tools/vep/script/index.html

Usage:
./vep [--cache|--offline|--database] [arguments]

Basic options
=============

--help                 Display this message and quit

-i | --input_file      Input file
-o | --output_file     Output file
--force_overwrite      Force overwriting of output file
--species [species]    Species to use [default: "human"]
                       
--everything           Shortcut switch to turn on commonly used options. See web
                       documentation for details [default: off]                       
--fork [num_forks]     Use forking to improve script runtime

For full option documentation see:
http://www.ensembl.org/info/docs/tools/vep/script/vep_options.html

```

If you have questions, please open an [issue](https://github.com/eilon-s/sherlock_vep/issues).
