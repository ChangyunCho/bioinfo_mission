# bioinfo_mission
### First mission 21.05.13 
1) Download dataset "https://hyeshik.qbio.io/binfo/binfo1-datapack1.tar"
2) Run subread (featureCounts -a [.gtf file] -o [output file name] *.bam)
3) processing with subread result (local data : "https://hyeshik.qbio.io/binfo/mouselocalization-20210507.txt")
4) visualize 

### Second mission 21.05.27
1) Preprocess GTF files
   1-1)grep '	start_codon	' gencode.gtf | grep '+' | grep 'transcript_support_level "1"' | sed -e 's/	[^ ]*transcript_id "\([^"]*\)".*$/	\1/g' > gencode-start.gtf
   1-2)greo '	exon	' gencode.gtf | grep '+' | sed -e 's/ [^ ]*transcript_id "\([^"]*\)".*$/ \1/g' > gencode-plus-exon.gtf
   1-3)bedtools intersect -a gencode-start.gtf -b gencode-plusexon.gtf -wa -wb | awk -F'	' -v OFS='	' $9 == $18 { print $10, $13-1, $14, $18, $4-1, $16; }' | sort -k1,1 -k2,3n -k4,4 > gencode-exons-containing-startcodon.bed
   1-4)(samtools view -H RPF-siLuc.bam; samtools view -F20 RPF-siLuc.bam | bioawk -c sam '{ if (length($seq)>= 25) print $0; }') | samtools view -b -o filtered-RPF-siLuc.bam
   1-5)bedtools genomecov -ibam filtered-RPF-siLuc.bam -bg -5 > fivepcounts-RPF-siLuc.bed
   1-6)bedtools intersect -a fivepcounts-RPF-siLuc.bed -b gencode-exons-containing-startcodon.bed -wa -wb -nonamecheck > fivepcounts-filtered-RPF-siLuc.txt
2) Draw plot with fivepcounts-filtered-RPF-siLuc.txt file (python code)

_Changyun Joe_
