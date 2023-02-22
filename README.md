# Long read de novo assembly exercise  

1. Download Data  
    SRR13577846
2. Quality Assesment using FastQC V0.11.9  
    ```sh
    fastqc --threads 2 Data/SRR13577846.fastq.gz --outdir fastqc_raw/
    ```  
    We have two Summary report statistics that fail: Per base sequence content, which shows expected variation for the first 10 zoomed in basepairs,  and realy large differences in the basepairs from 13000-13499. A reason for this could be that only a couple to one read of this length exists, thus making the distribution not as average. The end contains only Gs, highly repetitive region? The other failed statistic is Per sequence GC content. The main peak we see is twice the size of the expected one at 38%. We see two smaller peaks at around 17% and 44%. These might be worth investigating, as they may stem from contaminets.  
3. DeNovo Assembly with Hifisam V0.18.7  
    ```sh
    hifiasm -o hifisam_result/SRR13577846.asm -t 10 Data/SRR13577846.fastq.gz
    ```