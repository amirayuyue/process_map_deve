# Preparation for Acetone
1. Generate the pdb file from ATB website: moleculeID 705057, used the all-atom optimized geo file
2. The procedure to generate the GAFF files:
     
      `babel -ipdb 0F-atb-ori-pdb.pdb -omol2 0f-mol2.mol2`
   - Actiave conda environment on our Debye cluster: 
      `conda activate AmberTools21`

   1. Generate gaussian input file for RESP
      `antechamber -fi mol2 -fo gcrt -i 0f-mol2.mol2 -o 0f_gau.gau`
   2. Change name of gaussian output from “0f_gau.gau.log” to
      “0f_gau.out”
   3. Now start generating prepi residue topology file
      `antechamber -fi gout -fo prepi -i 0f_gau.out -o 0f_resp_gaff.prepi -c resp -j 4 -at gaff -rn 0FGA`
   4. Generate ac file from gaussian output
      `antechamber -fi gout -fo ac -i 0f_gau.out -o 0f_resp.ac -c resp`
   5. extract RESP charges
      `antechamber -fi ac -i 0f_resp.ac -c wc -cf 0f.crg`
   6. Read in charges file .crg and mol2 to gnerate new ac file
      `antechamber -fi mol2 -i 0f-mol2.mol2 -c rc -cf 0f.crg -fo ac -o 0f_with_resp.ac`
   7. Judge atom types to GAFF
      `atomtype -i 0F_with_resp.ac -o 0F_with_resp_gaff.ac -p gaff`

   8. Generate topology using prepgen
      `prepgen -i 0f_with_resp_gaff.ac -o 0f_with_resp_gaff.prepc -f car -rn 0FGA`
   9.  Use parmchk to check the missing force field parameters
      `parmchk2 -i 0F_with_resp_gaff.prepc -o 0F.frcmod -f prepc`
   10. `tleap -s -f leap.in`
    ** make sure your leap.in file name matches, e.g. saveamberparm EFZ EFZ.prmtop EFZ.prmcrd
    here the EFZ matched the prepc file
   11. finally use  amb2gro_top_gro.py
      `amb2gro_top_gro.py -p prmtop -c prmcrd -t gmx.top -g gmx.gro -b gmx.pdb`

## Solvent options
- [x]  Hexane
- [ ] Toluene
- [x] Acetonitrile
- [x] Chloroform
- [ ] DMSO
- [ ] Water

## Finding the IR, use the IR for hexane?
1. Acetone in hexane peak and fwhm are  1721.58113730929 17.420249653259816 
2. The fitting result using the GAFF two fitting with spc
    
   pama =[700.20042614, 1350.1860584]#two fitting spc

## Debug for data analysis when generating electrostatic result
1. Make sure you selected the corrected core atoms, carbonyl carbon and oxgen, it won't matter that much for the other carbon selection
2. But needs to make sure that the index number in the `core.c` file matches whatever you selected for the other carbon, the index number will change. 
3. Make sure to change the box dim file `box_dim.txt`
4. Make sure change the `user_input.txt` file, to match the parameter file
5. Run the program `python 01_calc_freq.py -p 4` select 4 processors to generate the eleF.

## Add ions
1. add 1M 2M 3M 4M different concentration of ions
   procedure: Use `GMX` to generate the correct number of ions
   a. `gmx editconf -f act_opt.gro -c -box 4.5 4.5 4.5 -o act_edit.gro`
   b. `gmx solvate -cp act_edit.gro -cs spc216 -o act_solvate.gro -p sys.top`
   c. `gmx grompp -f ions.mdp -c act_solvate -p sys.top -o ions.tpr`
   d. `gmx genions -s ions.tpr -pname MG -nname CL -pq 2 -neutral -conc 2 -o act_2M.gro -p sys.top`
   c. Gather the information from GMX on how many ions or SOL will be added or left to the sysytem
   d. Use packmol Debye: `/home/yyu49/new_proj/acetone/GROMOS_atb/ions/2M/packmol` to insert ions when concentration larger than 1M
 |     | Exp         | FWHM        | Calc        | FWHM        |
| :-- | :---------- | :---------- | :---------- | :---------- |
| D2O | 1697.059639 | 17.30929265 | 1688.169616 | 17.80198024 |
| 1M  | 1693        |             | 1688.223128 | 20.02722777 |
| 2M  | 1692        |             | 1687.977883 | 22.2524753  |
| 3M  | 1691        |             | 1687.701954 | 25.5903466  |
| 4M  | 1688        |             | 1685.792846 | 26.70297036 |
