# Preparation for Acetone
1. Generate the pdb file from ATB website: moleculeID 705057, used the all-atom optimized geo file
2. The procedure to generate the GAFF files:
     
      `babel -ipdb 0F-atb-ori-pdb.pdb -omol2 0f-mol2.mol2`
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
