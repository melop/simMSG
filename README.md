# simMSG
simMSG user manual: Predict the accuracy of MSG for your hybrid cross

I.	Download:
Once you have downloaded MSGsimulator.tar.gz, to unzip and untar, type:

	tar xvfz MSGsimulator.tar.gz

This will create a folder called MSGsimulator.

Inside this folder you will find the simMSG program folder, the installation folder with the installation shell script, the perl script which executes the program, an example configuration file, and example input and results from a run of simMSG:

	simMSG
	install
	simulateMSG.pl
	example_simmsg_config.cfg
	example_results_simulation

II.	Installation:
The installation tool has been tested on Ubuntu 12.04. Redhat types of Linux distributions (including CentOS) have a known issue with installing python2.7, a prerequisite for this installation tool. On these systems, please install python2.7 manually before executing the installation script. 

The following instructions assume Ubuntu 12.04 Desktop version. 

To install, first cd into the MSGsimulator directory:

	cd MSGsimulator/install 
	
If you have sudo privileges this will facilitate installation. Type:

	sudo bash install.sh

The installation tool will first check if the required versions of python, perl, R and php are installed. If not, the apt-get command will be executed to install them from the Ubuntu repository. Then, the tool installs all required packages for python, perl, and R within the simMSG folder so as not to interfere with the user’s existing package settings. If successful, a file named “runsimmsg.sh” will be created in the same folder as install.sh. This shell script contains “export” commands pointing to the package directories. 

Check the bwainstall.log and samtoolsinstall.log files to ensure that there are no errors.

When the installation is finished and you have checked the error logs, return to the main MSGsimulator directory by typing:

	cd ..


If you do not have sudo privileges, enter:

	bash install.sh

In case automated installation fails, all dependency packages can be found in simMSG/msg/dependencies for manual installation. 
After installation of these packages, you may need to update paths for python, R and perl in the runsimmsg.sh shell script.
Installation on a server environment may be further complicated by security settings and permissions, in which case you should check with the system administrator.

III.	Setting up the configuration file for simMSG:
simMSG has many parameters that go into the simulation (see Table at the end of the user manual) but many of these parameters have default values that can reasonably be used in most cases. 

Users set parameters by editing the configuration file (.cfg file). We recommend copying the example configuration file (example_simmsg_config.cfg) to a new file name and editing this file. The parameters that must be specified by the user are:

  Description  (parameter name in configuration file)
1)	name of parent 1 genome provided in fasta format (par1)
2)	name of parent 2 genome provided in fasta format (par2)
3)	genome size in Mb (genome_size)
4)	recombination rate in cM per kb (rate)
5)	chromosome to analyze (chrom)
6)	generations of recombination between the parental genomes (gen)
7)	proportion of the genome derived from parent1 (prop_par1)
8)	proportion of the genome derived from parent 2 (prop_par2)
9)	proportion of polymorphic sites in parent1 (poly_par1)
10)	 proportion of polymorphic sites in parent2 (poly_par2)

All other parameters have either suggested ranges or default values, but can be changed as described in Table 1 (at the end of this document).

If you are running simMSG on a cluster, there are additional parameters that need to be specified in the configuration file (see last row Table 1). By default, simMSG is configured for a SLURM resource management system, but users can change the job submission format by changing templates found in:
 ./simMSG/msg/template/

IV.	Running the pipeline: 
Link or copy the two parental reference fasta files into the MSGsimulator directory (containing the simMSG program folder and simulateMSG.pl script). Make sure the configuration file for the simulation is also in this directory.
To run the simulator, simply type from this directory: 

	bash runsimmsg.sh configuration_file.cfg

To store the information printed to the screen during the run (which can contain important information for troubleshooting an unsuccessful run, see below) type:
	
	bash runsimmsg.sh configuration_file.cfg > simMSG_out_log

If you are running a large number of individuals, you may need to allow for more processes per user for the hybrid genome generation step. To do this, before running simMSG enter:
	
	ulimit –u 126561

V.	Output files:

A)	If you have set save_files=0 (default)
Three major summary files are output by simMSG: 
  1)	Simulation_results_summary.txt
This file contains an overall summary of the simulation results, including number of markers sampled, overall accuracy,     accuracy by parent, and ancestry tract length. 
  2)	accuracy_distribution.pdf
This file is a histogram of individual accuracy for the simulation. 
  3)	ancestry_distribution.pdf
This file is a histogram of individual hybrid index for the simulation, generated from the output file individual_priors.

Other simulation files retained under this option are: 
  1) ancestry-probs-par1.tsv, ancestry-probs-par1par2.tsv, ancestry-probs-par2.tsv
	Files containing posterior probability of each genotype (homozygous parent 1, heterozygous, and homozygous parent 2 respectively) output from MSG. 

Two log files are also retained when using this parameter:
  1)	hybrid_genomes_log
Contains log of output and error messages generated during the hybrid genome simulation step of the pipeline.
  2)	msg_error_log
Contains log of output and error messages generated during the MSG steps of the pipeline. 

B)	If you have set save_files=1
In addition to the above files, the program will retain all intermediate files generated by the program and MSG.
The hybrid_genomes folder contains sequences generated for each hybrid individual (*genome_hap1.txt, *genome_hap2.txt), information on ancestry for each hybrid individual (*ancestry_hap1.txt, *ancestry_hap2.txt) and information on polymorphism for each individual (*_chr1_poly.txt, *_chr2_poly.txt).

The reads.fq_parsed folder contains reads generated for each hybrid individual. 

The following files contain information and calculations on expected polymorphisms:
poly_stats
SFS
polymorphism_distribution_par1
polymorphism_distribution_par2
expected_shared_polymorphism

The following files and folders are generated by MSG (details can be found on them at https://github.com/JaneliaSciComp/msg):
reads.fq_sam_files
hmm_data
hmm_fit
parent1_ref*
parent2_ref*
ancestry-probs*rda
msg.chrLengths
msgError*
msgOut*

VI.	Common errors:
In our experience the most common errors include:
1)	incorrect scaffold/chromosome name specified in the cfg file
2)	different scaffold/chromosome name in the two input parental genomes
3)	different scaffold/chromosome length in the two input parental genomes
4)	Input polymorphism parameters calculations generate a larger number of expected polymorphisms than available sites in the scaffold/chromosome or a non-positive number of sites. This is almost always caused by errors in the input polymorphism parameters or specifying too few individuals to simulate.
5)	Number of processes permitted per user is less than the number required (gives a “fork error” while generating the hybrid genomes. Try raising the number (for e.g.: ulimit –u 126561).

















