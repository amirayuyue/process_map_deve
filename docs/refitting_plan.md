## Plan for the refitting the ketone map

1. The refitting is going to include acetone
   1. Solvent choices, Acetonitrile, D2O, hexane
   2. Which covers the aprotic, protic (all polar), and non-polar solvents
2. Plan B
   1. Follow Steven Boxer idea and recruit the protein system as the application, then do another map that fit the GAFF force field.
3. For the fitting algorithm, add the more parameters, even include the cylindrical coordinates, which will take care the undefined y-direction, see the notes that Liang sent on 5/31/2022. 
   1. Take acetone as an example, C1 and C2 can each have a r-direction in cyclindrical system, and that electric field applied to both directions on the two atoms will do a sum-up then mulitiplied by another paramters
   2. Since we have 18 fitting samples, but only 2 two paramters, then the fitting might be underfit, we need to engage more fitting parameters, so we will include C1 and C2 x directions, and r-directions.   