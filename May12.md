
```bash
#Current pwd ~/projects/comparison
#qc first time
for i in `ls 00.rawdata/ | egrep -v 'zip|html'`;
do echo $i;
fastqc -o 00.rawdata/ 00.rawdata/$i ;
done


mkdir 01.preprocess
mkdir 02.mapping
mkdir log


#####
for i in `ls 00.rawdata/ | egrep -v 'zip|html'`; do j=${i%.*}; echo $j; cutadapt -u -100 -a AGATCGGAAGAGCACACGTCTGAACTCCAGTCAC -m 16 --trim-n --too-short-output=01.preprocess/$j.tooShort.fastq -o 01.preprocess/$j.cutadapt.fastq 00.rawdata/$j.fastq >> log/$j.cutadapt.log 2>> log/$j.cutadapt.err ; fastqc -o 01.preprocess/ 01.preprocess/$j.cutadapt.fastq ; done


###

for i in `ls 00.rawdata | egrep -v 'zip|html'`;
> do j=${i%.*};
> echo $j;
> mkdir 02.mapping/$j 02.mapping/$j/no_rRNA;
> bowtie2 -p 4 --sensitive-local --no-unal --un 02.mapping/$j/no_rRNA.fastq -x src/rRNA_exon 01.preprocess/$j.cutadapt.fastq -S 02.mapping/$j/no_rRNA/$j.rRNA_exon.sam > log/$j.rm_rRNA.log 2> log/$j.rm_rRNA.err ; 
> samtools view -b -f 16 02.mapping/$j/no_rRNA/$j.rRNA_exon.sam > 02.mapping/$j/no_rRNA/$j.rRNA_exon.reverseMap.bam;
> samtools fastq 02.mapping/$j/no_rRNA/$j.rRNA_exon.reverseMap.bam > 02.mapping/$j/no_rRNA/$j.rRNA_exon.reverseMap.fastq;
> cat 02.mapping/$j/no_rRNA/$j.no_rRNA.fastq 02.mapping/$j/no_rRNA/$j.rRNA_exon.reverseMap.fastq > 02.mapping/$j/no_rRNA/$j.rRNA_exon.unmapped.fastq;
> done
###make mid mapping
for j in AfterSurgery_1 AfterSurgery_2 AfterSurgery_3 BeforeSurgery_1 BeforeSurgery_2 BeforeSurgery_3 NC_1 NC_2 NC_3; do echo $j; samtools view -b -F 16 02.mappingnew/$j/midSizeRNA.sam > 02.mappingnew/$j/midSizeRNA/$j.midSizeRNA.clean.bam; done
for j in AfterSurgery_1 AfterSurgery_2 AfterSurgery_3 BeforeSurgery_1 BeforeSurgery_2 BeforeSurgery_3 NC_1 NC_2 NC_3; do echo $j;  rsem-tbam2gbam src/midSizeRNA 02.mappingnew/$j/midSizeRNA/$j.midSizeRNA.clean.bam  02.mappingnew/$j/midSizeRNA/$j.midSizeRNA.rsem.clean.bam ; echo "complete" $i; done

```
