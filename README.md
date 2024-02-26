For running these lines, headers must contain the sample identifier BEFORE  an **-** (hyphen), (i.e. BC1-Honduras19-F001, where BC1 is the sample identifier).
These lines run only for one fasta file.

Use vsearch to cluster sequences to 98.5% similarity and keeep singletons (for Sanger):

```
vsearch --cluster_fast SEQ.fas --id 0.985 \
--centroids SEQ_otu.fas --relabel OTU \
--sizeout --minseqlength 1
```

Map reads to count how many OTUS there are per sample:

```
vsearch --usearch_global SEQ.fas --db SEQ_otu.fas \
--id 0.985 --otutabout SEQ_otutab.txt \
--relabel_keep --uc SEQ.uc

```

To assign taxonomy, I used the blast algorithm against [UNITE](https://unite.ut.ee/repository.php) database (General FASTA release):

Format the database:

```
makeblastdb -in sh_general_release_dynamic_18.07.2023.fasta \
-dbtype nucl -out reference_db
```

Then, run blast:

```
blastn -query SEQ_otu.fas -db reference_db -max_target_seqs 1 \
 -out SEQ_tax_raw.txt -outfmt 6
```
