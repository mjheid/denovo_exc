# Long read de novo assembly exercise, M Heidrich  

1. Download Data  
    * SRR13577846
2. Quality Assesment using FastQC V0.11.9  
    ```sh
    fastqc --threads 2 Data/SRR13577846.fastq.gz --outdir fastqc_raw/
    ```  
    * We have two Summary report statistics that fail: Per base sequence content, which shows expected variation for the first 10 zoomed in basepairs,  and realy large differences in the basepairs from 13000-13499. A reason for this could be that only a couple to one read of this length exists, thus making the distribution not as average. The end contains only Gs, highly repetitive region? The other failed statistic is Per sequence GC content. The main peak we see is twice the size of the expected one at 38%. We see two smaller peaks at around 17% and 44%. These might be worth investigating, as they may stem from contaminets.  
3. DeNovo Assembly with Hifiasm V0.18.7  
    ```sh
    hifiasm -o hifisam_result/SRR13577846.asm -t 10 Data/SRR13577846.fastq.gz
    ```
    * Convert resulting .gfa files to fasta: ```sh
    awk '/^S/{print ">"$2;print $3}' SRR13577846.asm.bp.hap1.p_ctg.gfa > SRR13577846.asm.bp.hap1.p_ctg.fa
    awk '/^S/{print ">"$2;print $3}' SRR13577846.asm.bp.hap2.p_ctg.gfa > SRR13577846.asm.bp.hap2.p_ctg.fa
    ```  
4. QC with Quast V5.2.0  
    * Search for Reference Genome on SGD `http://sgd-archive.yeastgenome.org/sequence/S288C_reference/` by downloading latest genome releases, selecting the latest (RefSeq assembly GCF_000146045.2), and Downloading it.  
    ```sh
    quast hifisam_result/SRR13577846.asm.bp.hap1.p_ctg.fa -o quast/hap1/ -r Data/GCF_000146045.2_R64_genomic.fna.gz
    quast hifisam_result/SRR13577846.asm.bp.hap2.p_ctg.fa -o quast/hap2/ -r Data/GCF_000146045.2_R64_genomic.fna.gz
    ```  
    * This results in all of the repotrs found in the quast dir. Most natably, for the first/second haploid: 51/26 contigs, largest contig of 1506376/1410597, total length 12737440/11724748, N50 809047/809047, NG50 809047/809047, misassemblies 118/96  
5. QC with BUSCO V5.4.4  
    * Select linage database saccharomycetes_odb10, for concrete comparison.  
    ```sh
    busco -i hifisam_result/SRR13577846.asm.bp.hap1.p_ctg.fa -l saccharomycetes_odb10 -m genome -o busco_result/hap1 -f
    busco -i hifisam_result/SRR13577846.asm.bp.hap2.p_ctg.fa -l saccharomycetes_odb10 -m genome -o busco_result/hap2 -f
    ```
    * Main results for hap1 are: Complete 2129(99.7%), fragmented 2 (0.1%), missing 6 (0.2%) and total BUSCOs 2137. For hap2: Complete 2039, fragmented 3, missing 98 and total BUSCOs 2137
    