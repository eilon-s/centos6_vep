Bootstrap: docker
From: willmclaren/ensembl-vep

# sudo singularity build ensembl-vep Singularity

%help
	This is a singularity file for VEP docker.
	run example: singularity run --bind $PWD:$PWD ensembl-vep -i ./inputfilename.vcf -o ./outputfilename --cache --assembly GRCh37 --dir_cache /opt/vep/.vep --dir_plugins /opt/vep/.plugins --offline --format vcf --vcf --symbol --plugin Downstream --plugin Wildtype --terms SO

%environment
    LANGUAGE=en_US
    LANG="en_US.UTF-8"
    LC_ALL=C
    export LANGUAGE LANG LC_ALL

%post
    # copying vep installation 
    mkdir -p /opt;
    cp -r /home/* /opt;
    
    mkdir -p /opt/vep/.vep;
    # install data for yeast BY
    #perl /opt/vep/src/ensembl-vep/INSTALL.pl -a acf -s Saccharomyces_cerevisiae -y R64-1-1 -c /opt/vep/.vep;
    # install data for human hg19
    #perl /opt/vep/src/ensembl-vep/INSTALL.pl -a acf -s homo_sapiens -y GRCh37 -c /opt/vep/.vep
    
    # get plugins
    #rm -fr /opt/vep/.plugins;
    mkdir -p /opt/vep/.plugins;
    
    rm -f -r /VEP_plugins;
    git clone https://github.com/Ensembl/VEP_plugins.git;
    cp -r -f /VEP_plugins/* /opt/vep/.plugins;
    rm -f -r /VEP_plugins;
    
    # get pVACseq plugin
    rm -f -r /pVAC-Seq;
    git clone https://github.com/griffithlab/pVAC-Seq.git;
    cp -f /pVAC-Seq/pvacseq/VEP_plugins/Wildtype.pm /opt/vep/.plugins;
    rm -f -r /pVAC-Seq;
    
%runscript
    exec /opt/vep/src/ensembl-vep/vep "$@"

%apprun vep
	exec /opt/vep/src/ensembl-vep/vep "$@"

%apprun install_vep
	exec perl /opt/vep/src/ensembl-vep/INSTALL.pl "$@"
