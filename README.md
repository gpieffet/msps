Molecular System Preparation Scripts

This an ensemble of scripts designed to facilitate and speed up the setup of molecular dynamics simulations of small molecules, proteins and protein - ligand complexes. The scripts were written to work with the Gromacs simulation package and as such are probably only useful if gromacs is already installed. The scripts are high level so it doesn't matter which version of gromacs you're running providing it's > 5.x.

When using the scripts, you'll first have to set the variables ROOTDIR, FF_DIR, DOCK_DIR and DOCK_DIR_GLIDE inside the file prep_VAR. The file prep_VAR is parsed by many of the scripts to know where to find the structures and trajectories that need to be processed, among other things:

 - ROOTDIR: the root directory of the project
 - FF_DIR: the location of the force field (FF), only needed if using a FF not included in the default distribution
 - DOCK_DIR: the location of the docking results from vina
 - DOCK_DIR_GLIDE: the location of the docking results from Glide
 

Appart from gromacs, the following programs are also needed by some scripts:

 - babel for file format conversion
 - vina and vina_split for running docking simulations
 - antechamber for generating ligand topology files (including RESP charges from QM calculations)
 - gmxMMPBSA for MM/P(G)BSA calculations
 
 
Note that the scripts expect the result files to be organized and named in a certain way, i.e. the docking results for the protein PROT are expectd to be located in the directory named PROT and the name of the docking results of the ligand LIG with PROT should be PROT_LIG-run.
Clustering the docking results is done using the script *prep_cluster*:
```
$ ./prep_cluster
Syntax: ./prep_cluster protein ligand [nb_cluster]

$ ./prep_cluster PROT LIG 4
```
The script should give back a file PROT_LIG.pdb containing the structures of the center of 4 cluster accross all the docking poses.


A sequence of actions after preparing the ligand (for instance with gaussview) and the protein structure (for instance with maestro) could be:

1. Generate the pdbqt files with adt (if using vina) and running the docking simulations
```
$ vinad PROT.pdbqt LIG.pdbqt param.conf 
```

2. Sort the docking results into clusters and select the center each cluster
```
$ prep_cluster PROT LIG.pdb CUTOFF
```

3. Calculate the (ESP) charges of the ligand (for instance with gaussian) based on one of the docking poses

4. Generate the ligand topology with antechamber from the calculated (ESP) charges
```
$ prep_top_lig LIG.log
```

5. Run the MD simulation of the ligand (min, eq and producion run)
```
$ prep_sim_lig LIG
```

6. Prepare the topology of the protein
```
$ prep_top_prot PROT
```

7. Run the MD simulation of the protein
```
$ prep_sim_prot $PROTEIN GPU_ID
```

8. Prepare structure and topology of the protein-ligand complex using the first cluster center cc0
```
$ prep_top_compl $PROTEIN $LIGAND cc0
```

9. Run the MD simulations of the complex +
```
$ prep_sim_compl $PROTEIN $LIGAND cc0 0
```
