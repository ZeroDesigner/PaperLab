# Computationally designed peptide macrocycle inhibitors of New Delhi metallo-β-lactamase 1

[TOC]

## Purpose

We present structure-guided computational methods for designing peptide macrocycles built from mixtures of l- and d-amino acids that are able to bind to and inhibit targets of therapeutic interest.

## Key words

> structure-guided , peptide macrocycles, mixtures of l- and d-amino acids, bind  and inhibit

## Software

+ Rosetta Software Suite:https://www.rosettacommons.org/
+ PyMol:https://sourceforge.net/projects/pymol/

## Detail

1. Target : New Delhi metallo-β-lactamase 1 (NDM-1),  a bacterial enzyme that degrades β-lactam antibiotics.

3. Result:

   1. determine X-ray crystal structures of three of the designed inhibitors in complex with NDM-1

      1. all three the conformation of the peptide is very close to the computationally designed model
      2.  two of the three structures, the binding mode with NDM-1 is also very similar to the design model
      3. the third, we observed an alternative binding mode likely arising from internal symmetry in the shape of the design combined with flexibility of the target. 

      ![An external file that holds a picture, illustration, etc. Object name is pnas.2012800118fig01.jpg](https://gitee.com/zerodesigner/markdown-png/raw/master/uPic/pnas.2012800118fig01.jpg)

      > (*A*) Structure of NDM-1 (PDB ID 4EXS), chain B. The active site binds catalytic zinc atoms and is flanked by an ordered FL and a flexible HL. Hydrophobic amino acid residues on the inner face of the HL, and metal-coordinating residues, are labeled. (*B*) Comparison of a subset of NDM-1 crystal structures. PDB IDs 3RKJ, 3S0Z, 3ZR9, and 4HL1 are shown in gray. In lavender and green are PDB ID 4EXS, chains A and B, respectively. Where most of the structure, including the FL, is rigid, the HL shows extensive conformational flexibility, putting inner-face hydrophobic side chains (labeled) in diverse positions. (*C*) Crystal structure of NDM-1 active site (green) with ʟ-captopril (purple) bound (PDB ID 4EXS, chain B). Active-site zinc atoms are shown beneath the surface as spheres. (*D*) In silico conversion of ʟ-captopril to a ᴅ-proline, ʟ-cysteine dipeptide (purple) flanked by polyglycine sequences (pink). (*E*) Rapid in silico sampling of closed conformations of a peptide macrocycle containing the ᴅ-cysteine, ʟ-proline stub (purple), and flanking sequences (pink) in the context of the NDM-1 active site, using the generalized kinematic closure approach. For each closed conformation, Rosetta design heuristics were used to find side-chain identities and conformations (represented here by spheres).

## Workflow

NDM-1 pdb structure :4EXS

Keys:

+ L-captopril occludes the NDM-1 active site cleft

+  Adjacent to this cleft are an ordered front loop (FL) consisting of amino acids 210 through 228

+ A flexible hinge loop (HL) consisting of amino acids 64 through 73. 

+ The HL shows considerable conformational heterogeneity from one crystal structure to another or even in copies of the molecule in the asymmetric unit of a single crystal structure ([Fig. 1*B*](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8000195/figure/fig01/)). 

  ![image-20210425115440362](https://gitee.com/zerodesigner/markdown-png/raw/master/uPic/image-20210425115440362.png)

To do:
+ **Supposed** that the observed HL conformations in available crystal structures represent relatively low-energy conformations of this loop. 
+ *Since the conformation in chain B of PDB structure 4EXS presents Phe70 in a position likely to permit favorable hydrophobic interactions with an inhibitor, we chose this conformation for our in silico design work.*

Step:

##### 1. Make Input Structure

   1. Get protein structure

      ![image-20210425121112532](https://gitee.com/zerodesigner/markdown-png/raw/master/uPic/image-20210425121112532.png)

   2. Converted the  methyl group  to an amine to yield a d-cysteine-l-proline dipeptide “stub” bound in the NDM-1 active site.

      See `Data/Converted  l-captopril methyl group to an amine.mov`

      Out peptide：`Data/d-cysteine-l-proline dipeptide.pdb`

      ![Screen Shot 2021-04-25 at 12.40.01 PM](https://gitee.com/zerodesigner/markdown-png/raw/master/uPic/Screen%20Shot%202021-04-25%20at%2012.40.01%20PM.png)

   3.  Extended this stub

      The rosetta script will finish this.
      
      1. prepending a three-residue polyglycine chain by an amide bond to the d-cysteine
      
      2. appending a three-residue polyglycine chain to the C terminus of the l-proline to yield an eight-residue peptide
      

##### 2. Sampled conformations of this chain

Using the **Rosetta generalized kinematic closure** method , we sampled conformations of this chain

+ compatible with an amide bond linking the two termini and with **favorable intramolecular backbone hydrogen bonding**
+ **keeping the d-cysteine-l-proline starting stub fixed**.

Keys:

1. For each conformation sampled,we designed sequences to maximize favorable interactions with the target while **favoring the designed conformation using Rosetta side-chain packing methods**, sampling l- and d-amino acids at positions able to accommodate each respectively .

2. Followed by a Monte Carlo-based refinement procedure in which we sampled small perturbations of the peptide conformation using **generalized kinematic closure, reoptimizing side-chain identities and rotamers** for each conformation sampled. 

3. Filtered this initial pool of several hundred designs based on the number of **internal hydrogen bonds, shape complementarity to the target, and atomic clashes**. 

4. To assess diversity of backbone conformations in the filtered population, we assigned each amino acid residue to one of four conformational bins, designated **A, B, X, and Y**, and representing **left-handed α-helical, left-handed β-strand, right-handed α-helical, and right-handed β-strand conformations**.

5. We selected peptides with diverse backbone bin strings, and since we hypothesized that **rigidity would be a key determinant of success**, these were subjected to in silico conformational landscape analysis using the **Rosetta simple_cycpep_predict protocol**  to  predict the binding-competent conformation in the absence of the target.

6. We used **the $P_{Near}$ metric** (approximates the fractional occupancy (Boltzmann weight) of the designed conformation amid large sets of alternative conformations generated by extensive conformational sampling).  

   > $P_{Near}$ values close to 0 indicate little predicted propensity to favor the binding-competent conformation, while values close to 1 indicate high predicted propensity for the binding-competent conformation.

7. NDM1i-3 designs were designed using **a variant of the protocol used to produce NDM1i-1 designs**. Starting with the X-ray crystal structure of NDM1i-1G bound to NDM-1, the macrocycle was subjected to small perturbations and redesigned using a much-expanded palette of amino acid building blocks. The step of performing extensive macrocycle backbone conformational sampling via a Monte Carlo search was omitted. To be as conservative as possible, we constrained **the number of exotic noncanonical amino acids** to one to two per design using the aa_compostion design-centric guidance scoring term . We also made use of newly developed **design-centric terms to encourage desired properties** during design. 

8. Based on the alternative conformation observed in the X-ray crystal structure of NDM1i-3D bound to NDM-1, we redesigned the macrocycle using **a more broadly expanded palette of amino acid building blocks** to produce the NDM1i-4 designs. We also altered the protocol used for NDM1i-3 designs by **adding sampling of small perturbation of the HL** in addition to the macrocycle backbone, relaxing the restrictions on the number of exotic side chains that could be incorporated and including crystallographic water molecules during design as hydrogen bond donors and acceptors. 


Step:

1. Produce NDM1i-1A through NDM1i-1G designs

   It will last a long time.

   ```shell
   cd 'Step.2.Produce NDM1i-1A through NDM1i-1G designs'
   rosetta_scripts.static.linuxgccrelease @rosetta.flags
   ```

2. Produce NDM1i-3A through NDM1i-3D designs

   ```shell
   cd 'Step.2.Produce NDM1i-1A through NDM1i-1G designs'
   rosetta_scripts.static.linuxgccrelease @rosetta.flags
   ```

3. Analysis and Plotting Python Scripts

   1. NDM1i_delta_G_binding_estimation_scripts

      ```shell
      cd 'Support_Script/NDM1i_delta_G_binding_estimation_scripts'
      rosetta_scripts.static.linuxgccrelease @rosetta.flags
      ```

   2. peptide_structure_prediction_example

      ```
      
      ```

