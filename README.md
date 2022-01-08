#### Process_radtags
The command line used for this process was:
 ```
 /programs/stacks-2.59/process_radtags -p ./ -b ../Barcodes/anthonyi-barcodes-lib6_pool1.txt -o ../PROCESSED_RADTAGS/ -c -q -r --inline_null --renz_1 nlaIII --renz_2 ecoRI

 ```
 The script is available in the scripts folder.

 #### Denovo_map.pl

 We ran 18 different de novo assemblies, these permutations were variations of Stacks parameters that included their default values, those could affect the results of allele numbers, allele, and SNP error rates (Mastretta-Yanes et al., 2015; Paris, Stevens and Catchen, 2017). The Stacks parameters to vary were (-m) a minimum number of identical reads to create a stack, (-M) number of mismatches allowed between loci in a single individual and (-n) number of mismatches allowed between loci when building catalog. 

| Permutation    | (-m)    | (-M)     | (-n)     | Sample |
| :------------- | :------------- |:------------- | :------------- |:------------- |
|a	|3	|2	|3	|166
|b	|3	|3	|2	|166
|c	|3	|3	|3	|166
|d	|3	|5	|4	|166
|e	|3	|5	|5	|166
|f	|3	|7	|6	|166
|g	|3	|7	|7	|166
|h	|4	|3	|2	|163
|i	|4	|3	|3	|162
|j  |4	|5	|4	|163
|k	|4	|5	|5	|162
|l	|4	|7	|6	|163
|m	|4	|7	|7	|163
|n	|5	|3	|2	|158
|o	|5	|3	|3	|158
|p	|5	|5	|4	|158
|q	|5	|5	|5	|158
|r	|5	|7	|6	|158
|s	|5	|7	|7	|158

