# PUMA #
## PANDA Using MicroRNA Associations ##
PUMA, or **P**ANDA **U**sing **M**icroRNA **A**ssociations, is a regulatory network reconstruction algorithm that uses message passing to model gene regulatory interactions. PUMA can reconstruct gene regulatory networks using both transcription factors and microRNAs as regulators of mRNA expression levels. This Github repository contains both a MATLAB and a C++ version of PUMA.

## MATLAB code ##
### Condition-specific PUMA networks ###
The MATLAB code of PUMA and example files to run PUMA in MATLAB can be found in folder `PUMAm`. The main script to run PUMA is `RunPUMA.m`. This script is set-up to run PUMA on example data from folder `ToyData`. The script must be run with a regulatory prior (variable `motif_file` in the RunPUMA script) and expression data (variable `exp_file`). Protein-protein interaction data (variable `ppi_file`) and a list of microRNAs (variable `mir_file`) are optional parameters. If no `mir_file` is listed, the script will run the message passing algorithm described in PANDA to estimate regulatory edges, assuming that all regulators are transcription factors. If a `mir_file` is listed, the script will run PUMA to estimate regulatory edges. In particular, regulators listed in that file will be treated as regulators that cannot form complexes with other regulators, such as microRNAs, while regulators not listed will be treated as regulators that can form complexes, such as transcription factors.

To run PUMA on your own data, change the paths and filenames under "Set Program Parameters". MATLAB functions `NormalizeNetwork.m`, `PANDA.m`, `PUMA.m`, `Tfunction.m`, and `UpdateDiagonal.m` will be called from the main script.

Some important notes on this tool:
- The target genes in the regulatory prior (`motif_file`) should match the target genes in the expression data (`exp_file`).
- The regulators in `mir_list` should match symbols in the regulatory prior (`motif_file`).
- Self-interactions in the protein-protein interaction data will be set to 1. The algorithm converges around these edges. Protein-protein interaction "prior" edges therefore should have weights between 0-1.
- In the protein-protein interaction prior, edges between microRNAs from the `mir_file` (see `RunPUMA.m` script) and other regulators can have values, but these will automatically be set to 0, as the algorithm assumes that any regulator listed in `mir_file` will not be able to form edges with other regulators.

### Resampling PUMA networks ###
`RunPUMAresample.m` runs PUMA as described above, but resamples the data multiple times (variable `nrboots` under "Set Program Parameters") by removing a certain percentage (variable `perc` under "Set Program Parameters") of samples to obtain a collection of networks.

### Single-sample PUMA networks ###
LIONESS, or **L**inear **I**nterpolation to **O**btain **N**etwork **E**stimates for **S**ingle **S**amples, can be used to estimate single-sample networks using aggregate networks made with any network reconstruction algorithm (Kuijjer et al. 2019. iScience, https://doi.org/10.1016/j.isci.2019.03.021).

The main script to run PUMA+LIONESS is `RunPUMALIONESS.m`. For instructions to run this script, see instructions under "Condition-specific PUMA networks".

### Other scripts ###
Some useful scripts can be found in the folder `OtherScripts`:
- bash scripts `RunPUMA.sh` and `RunPUMALIONESS.sh` can be used to remotely run `RunPUMA.m` and `RunPUMALIONESS.m`, respectively.
- `getCompleteEdgelist.R` is an R script that can convert an unweighted regulatory prior in a complete edgelist. This can be useful when preparing the regulatory prior and expression data.

## C++ code ##
The C++ code of PUMA and example files to run PUMA can be found in folder `PUMAc`. The code can be compiled using:
```
g++ PUMA.c -O3 -o PUMA
```

To run PUMA with the assumption that all regulators can form complexes (estimate *responsibility* for all regulators, *eg* a gene regulatory prior with transcription factors only):
```
./PUMA -e ToyExpressionData.txt -m ToyMotifData.txt -o ToyOutput
```

To tell PUMA to discriminate between regulators that can, and regulators that cannot cannot form complexes (for example a list of microRNAs in `miRlist.txt`), run:
```
./PUMA -e ToyExpressionData.txt -m ToyMotifData.txt -u miRlist.txt -o ToyOutput
```
More flags are highlighted in the source code. The PUMA C++ code was based on the PANDA C++ version 2 sourceforge project: https://sourceforge.net/projects/panda-net/.
