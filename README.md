# hse22_hw1

Команды в консоли сервера:

```
# создаем ссылки на файлы с сырыми данными
ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq

# случайная выборка чтений
seqtk sample -s515 oil_R1.fastq 5000000 > pairedend1.fastq
seqtk sample -s515 oil_R2.fastq 5000000 > pairedend2.fastq
seqtk sample -s515 oilMP_S4_L001_R1_001.fastq 1500000 > matepairs1.fastq
seqtk sample -s515 oilMP_S4_L001_R2_001.fastq 1500000 > matepairs2.fastq

# создаем папки для результатов работы fastQC и multiQC
mkdir fastqc
mkdir multiqc

# анализируем с помощью fastQC и multiQC
fastqc -o fastqc pairedend* matepairs*
multiqc -o multiqc fastqc
```

![](https://github.com/nik-fedorov/hse22_hw1/blob/main/screenshots/before1.png)
![](https://github.com/nik-fedorov/hse22_hw1/blob/main/screenshots/before2.png)

```
# подрезаем чтения
platanus_trim pairedend*
platanus_internal_trim matepairs*

# удаляем ненужные файлы
rm matepairs*.fastq pairedend*.fastq

# аналогично оценваем качество подрезанных ридов с помощью fastQC и multiQC
mkdir fastqc_trimmed
mkdir multiqc_trimmed
fastqc -o fastqc_trimmed pairedend* matepairs*
multiqc -o multiqc_trimmed fastqc_trimmed
```

![](https://github.com/nik-fedorov/hse22_hw1/blob/main/screenshots/after1.png)
![](https://github.com/nik-fedorov/hse22_hw1/blob/main/screenshots/after2.png)

```
# собираем контиги из подрезанных чтений
platanus assemble -o contigs -f pairedend*.fastq.trimmed

# формируем скаффолды
platanus scaffold -o contigs -c contigs_contig.fa -IP1 pairedend*.fastq.trimmed -OP2 matepairs*.fastq.int_trimmed

# заполняем гэпы
platanus gap_close -o contigs -c contigs_scaffold.fa -IP1 pairedend*.fastq.trimmed -OP2 matepairs*.fastq.int_trimmed

# удаляем ненужные файлы
rm matepairs* pairedend*
```

Ссылка на [ноутбук в Colab](https://drive.google.com/file/d/1MZq8y6x-PWOKyukWdG8DjQSJW3_zXdBo/view?usp=sharing) и тот же [ноутбук в репозитории](https://github.com/nik-fedorov/hse22_hw1/blob/main/src/hse22_hw1.ipynb), где проводились остальные вычисления.
