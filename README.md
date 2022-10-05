# hse22_hw1
# Делаем ссылки на файлы
```
ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq
```
# Получаем семплы
```
seqtk sample -s1012 oil_R1.fastq 5000000 > pe1.fastq
seqtk sample -s1012 oil_R2.fastq 5000000 > pe2.fastq
seqtk sample -s1012 oilMP_S4_L001_R1_001.fastq 1500000 > mp1.fastq
seqtk sample -s1012 oilMP_S4_L001_R2_001.fastq 1500000 > mp2.fastq
```
Используем в качестве сида месяц + день рождения
# Анализ с помощью fastQC и multiQC
```
mkdir fastqc
mkdir multiqc
fastqc -o fastqc pe1.fastq 
fastqc -o fastqc pe2.fastq
fastqc -o fastqc mp1.fastq
fastqc -o fastqc mp2.fastq
multiqc -o multiqc fastqc
```

![MultiQC Report](https://user-images.githubusercontent.com/115037034/194146652-58635ed7-4684-4ca1-b25c-810306b67b2f.png)
# Обрезаем с помощью platanus
```
platanus_trim pe1.fastq pe2.fastq
platanus_internal_trim mp1.fastq mp2.fastq
```
# Удаляем ненужные файлы
```
rm pe1.fastq
rm pe2.fastq
rm mp1.fastq
rm mp2.fastq
```
# Теперь делаем анализ для обрезанных файлов
```
mkdir fastqc_trimmed
mkdir multiqc_trimmed
fastqc -o fastqc_trimmed pe1.fastq.trimmed
fastqc -o fastqc_trimmed pe2.fastq.trimmed
fastqc -o fastqc_trimmed mp1.fastq.int_trimmed
fastqc -o fastqc_trimmed mp2.fastq.int_trimmed
multiqc -o multiqc_trimmed fastqc_trimmed
```
![MultiQC Report (1)](https://user-images.githubusercontent.com/115037034/194147616-73aa6501-0b2d-46ec-a306-0e502707262d.png)
# Делаем анализ с помощью platanus
```
time platanus assemble -o Poil -f pe1.fastq.trimmed pe2.fastq.trimmed 2> assemble.log
time platanus scaffold -o Poil -c Poil_contig.fa -IP1 pe1.fastq.trimmed pe2.fastq.trimmed -OP2 mp1.fastq.int_trimmed mp2.fastq.int_trimmed 2> scaffold.log
time platanus gap_close -o Poil -c Poil_scaffold.fa -IP1 pe1.fastq.trimmed pe2.fastq.trimmed -OP2 mp1.fastq.int_trimmed mp2.fastq.int_trimmed 2> gapclose.log
```
# Удаляем все лишние файлы
```
rm pe1.fastq.trimmed
rm pe2.fastq.trimmed
rm mp1.fastq.int_trimmed
rm mp2.fastq.int_trimmed
```
# Результаты анализов контигов
```
Общее количество - 592 
Общая длина -  3922783 
Самый длинный -  179307 
N50 - 54904
```
# Результаты анализов скаффолдов без обрезанных чтений
```
Общее количество - 69 
Общая длина -  3872501 
Самый длинный -  3833133 
N50 - 3833133
Число гепов -  139
Общая длина гепов -  6024
```
# Результат анализов скаффолдов с обрезанными чтениями
```
Общее число - 69 
Общая длина -  3963828 
Самый длинный -  3923615 
N50 -  3923615
Число гепов -  8
Общая длина гепов -  1188
```
