![image](https://github.com/user-attachments/assets/33026fb3-a0ba-426b-afd0-7b59cffb12ee)# BRIN eDNA Masterclass
## Pre-Workshop: HPC Introduction and Setup Environment
Modified from "BRIN-ONT-avian-workshop" by Syam Budi Irianto
Link: https://github.com/andhikaprima/BRIN-ONT-avian-workshop
#

## 1. Mengakses MAHAMERU BRIN HPC dengan SSH
Gunakan Terminal, Command Prompt, atau CLI software favorit Anda untuk menjalankan perintah SSH.
```
ssh [akun SSO BRIN]@login2.hpc.brin.go.id
```
Jika berhasil, Anda masuk di login node bernama `trembesi02` seperti tampilan di bawah ini

![image](https://github.com/user-attachments/assets/bf171033-ce89-4cf7-91f7-2d5f67693965)



## 2. Bekerja dengan mode interaktif di MAHAMERU BRIN HPC
Gunakan perintah `srun` untuk bekerja dengan cara interaktif di MAHAMERU BRIN HPC seperti berikut ini
```
srun --partition=interactive2 --cpus-per-task=4 --pty bash
```
Jika berhasil, maka Anda akan mendapatkan compute node bermana `trembesi91` atau `trembesi92`. 

![image](https://github.com/user-attachments/assets/5293726b-4cbb-4f81-a9b7-1c1f78b31f56)



## 3. Check versi python dan update versi python
Untuk mengetahui versi pythin Anda, ketikan perintah `python –version` atau `python -V`
Pastikan versi yang Anda gunakan adalah Python 3.10.14
Jika bukan versi tersebut, lakukan langkah berikut:
```
wget https://www.python.org/ftp/python/3.10.14/Python-3.10.14.tgz
tar zxfv Python-3.10.14.tgz
rm Python-3.10.14.tgz
find ./Python-3.10.14/Python -type d | xargs chmod 0755
cd Python-3.10.14
./configure --prefix=$PWD/Python-3.10.14/Python
make
make install
export PATH=$CWD:$PATH  # adds python3.10 to PATH
```
Cek kembali versin python Anda 



## 4. Menyiapkan Conda Environment
PERHATIAN!
Jika Anda sudah memiliki Conda Environment, silakan SKIP langkah ini dan lanjut ke langkah 5.

Install miniconda dengan perintah berikut.
```
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-py39_24.5.0-0-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm -rf ~/miniconda3/miniconda.sh
./miniconda3/bin/conda init

```
Baca dan ikuti langkah proses instalasi, ketik 'Y' jika diperlukan.
Setelah proses instalasi selesai, logout terlebih dahulu dengan perintah `exit` sebanyak 2 kali. Exit pertama untuk keluar dari mode interaktif dan exit kedua untuk keluar dari MAHAMERU BRIN HPC.

Login kembali ke MAHAMERU BRIN HPC dengan perintah SSH. Jika instalasi conda berhasil dan environment `base` diaktifkan otomatis, maka prompt di login node muncul `(base)` sebelum tulisan akun Anda, seperti contoh berikut.

![image](https://github.com/user-attachments/assets/85ce8af8-39a3-4f88-8c9b-186877a72ff5)


Anda dapat juga mengecek apakah Conda sudah terinstall atau belum dengan mengetikkan perintah 
```
 conda --version
```
Jika Conda telah berhasil terpasang, maka akan muncul nomor versi Conda yang terpasang, misal `conda 24.5.0`. 
![image](https://github.com/user-attachments/assets/469ea1b9-96d4-4879-b89c-dfe78c22b6a9)



## 5. Buat dan aktifkan environment aplikasi untuk analisis
Agar proses pembuatan environment dan instalasi aplikasi berjalan lancar, lakukan proses tersebut di interactive compute node dengan cara menjalankan perintah `srun` seperti pada langkah 2.

Dalam workshop ini, aplikasi yang akan digunakan adalah `R` untuk melakukan QC, filtering, clustering dan taxonomy assigment. Aplikasi `R base` di Conda channel yang bernama 'bioconda'. `R` adalah lingkungan perangkat lunak gratis untuk komputasi statistik dan grafik. R dikompilasi dan berjalan pada berbagai macam platform UNIX, Windows dan MacOS.

Karena dependencies dari masing-masing aplikasi yang digunakan tidak selalu seragam, terutama versi `python`, maka menggunakan satu environment untuk ketiga aplikasi di atas tidak memungkinkan. Dalam workshop ini, setiap aplikasi akan menggunakan environment sendiri. 



## 6 Install R base
```
conda create -n Rbase -c conda-forge r-base=4.4.1
```
Ketik 'Y' untuk melanjutkan instalasi saat proses meminta konfirmasi. Ada banyak paket yang dibutuhkan R, sehingga semua paket tersebut akan diunduh dan selanjutnya dipasang. Tunggu hingga proses instalasi selesai. Perintah tersebut akan membuat environment di `Conda` dengan nama `Rbase` untuk aplikasi `R` 
![image](https://github.com/user-attachments/assets/b2a62a21-3643-49e4-8610-0df75c6b90c1)
![image](https://github.com/user-attachments/assets/aa9f2971-7d8e-402d-9845-32addab150f3)
![image](https://github.com/user-attachments/assets/0cd4ef3d-e6ee-40a6-b83d-60b7defcea54)
![image](https://github.com/user-attachments/assets/c74a9ce7-10e2-4818-a5bd-e48bc3f3d06a)


Jika proses selesai dan berhasil, periksa versi R yang terinstall dengan menjalankan perintah sebagai berikut.
```
conda activate Rbase
R --version
```
Jika berhasil, maka layar akan menampilkan kode versi NanoPlot. 
![image](https://github.com/user-attachments/assets/64af83ce-c456-46fd-ae26-39f37af6c289)

Pastikan bahwa `R base` yang terinstall adalah version 4.4.1



## 7 Run R
Untuk menjalankan R, cukup ketik “R” (huruf besar) di terminal
```
R
```
![image](https://github.com/user-attachments/assets/b9b2834a-5015-4e87-90a6-87950486587b)

Untuk keluar dari aplikasi R ketikan perintah:
```
q()
```



## 8 Menginstal R packages dependencies (Diperlukan 1-2 jam, terutama permintaan unduhan yang tinggi pada waktu yang sama)
Karena pekerjaan kita berkaitan dengan bidang biologi, makan kita akan menggunakan repositori untuk bidang biologi, yakni `BiocManager`.
```
install.packages("BiocManager")
```
Ketika Anda pertama kali menginstal R, ia akan meminta mirror CRAN Anda (semacam basis data global dari R packages). Untuk kali ini, Anda dapat mengetikkan 44 untuk "44: Indonesia (Banda Aceh) [https]". 
![image](https://github.com/user-attachments/assets/158e7bf9-fd94-4963-b7f3-5f685b732c85)

Install.Packages hanya utnuk mengunduh dan menginstal package. Untuk menggunakan package tersebut, kita perlu menjalankan perintah berikut ini:
```
library(BiocManager)
```



## 9 Menginstall Dada2 
Pipeline yang akan kita gunakan untuk analisis data eDNA metabarcoding adalah Dada2. Dada2 dikembangkan oleh Benjamin J Callahan et.al. (2016). Dada2 adalah perangkat lunak yang memodelkan dan mengoreksi kesalahan amplikon yang diurutkan oleh Illumina. DADA2 menyimpulkan urutan sampel dengan tepat, tanpa pengelompokan kasar ke dalam OTU, dan menyelesaikan perbedaan sesedikit satu nukleotida. Dalam beberapa komunitas tiruan, DADA2 mengidentifikasi lebih banyak varian nyata dan menghasilkan lebih sedikit urutan palsu daripada metode lain. Jadi, mari kita unduh file dada2 miliknya. Kemudian muat paketnya dan lakukan pengecekan versi, seharusnya "1.32.0".
```
BiocManager::install("dada2", version = "3.19")
library(dada2); packageVersion("dada2")
```
![image](https://github.com/user-attachments/assets/4deda9f2-bbe9-43f1-a8fc-6b555e1f666c)

Selamat pipeline Dada2 siap untuk digunakan lebih lanjut saat eDNA Masterclass



## 10 Setup directory and download dataset
Sekarang kita ingin menyiapkan dataset untuk pelatihan. Data yang akan kita gunakan sama dengan data yang digunakan dalam SOP mothur MiSeq (https://mothur.org/wiki/miseq_sop/). Untuk mengikuti langkah selanjutnya, unduh data contoh dan unzip. File fastq ini dihasilkan oleh pengurutan amplikon Illumina Miseq 2x250 dari wilayah V4 gen 16S rRNA dari sampel usus yang dikumpulkan secara longitudinal dari tikus pascasapih. Untuk saat ini, anggap saja file fastq berpasangan yang akan diproses. 
Tautan: https://mothur.s3.us-east-2.amazonaws.com/wiki/miseqsopdata.zip
Pertama, kita perlu membuat direktori dengan mengetik:
```
mkdir workshop
cd workshop
mkdir eDNAmasterclass
cd eDNAmasterclass
```
![image](https://github.com/user-attachments/assets/e9253f4c-7a0b-4ca6-8949-db24cab7851b)

Kemudian, unduh dataset dengan mengetik:
```
wget https://mothur.s3.us-east-2.amazonaws.com/wiki/miseqsopdata.zip
```
![image](https://github.com/user-attachments/assets/c9ae9f02-1c8a-4c5c-a0c1-4195343236b7)

Kemudian, buka zip dataset dengan mengetik:
```
unzip miseqsopdata.zip
ls
```
![image](https://github.com/user-attachments/assets/cd931726-0f06-42de-9120-5f80c3735016)
![image](https://github.com/user-attachments/assets/3a6a2317-6d55-41b2-b6f1-d2f92a653d5d)

Sekarang kita telah memiliki dataset yang nantinya akan digunakan untuk pelatihan di dalam folder “MiSeq_SOP”.



## 11 Mari mencoba Dada2 pipeline
1. Jalankan R

2. Load package Dada2

3. Setting Working Directory and File Paths
```
setwd("~/workshop/eDNAmasterclass")
```

Now, you must set the path to the files you were provided with. For me this is
```
path <- "./MiSeq_SOP" 
```

Note that there is a “./” at the front. This means that the path starts from the present working directory.
you could also do this
```
path <- " /mgpfs/home/andh009/workshop/eDNAmasterclass/MiSeq_SOP"
```

Now, let's check if you have the right path.
```
list.files(path)
```

4. Collating fastq Files 
Now, it starts to look complicated. But really, it’s doing something quite simple.
```
fnFs <- sort(list.files(path, pattern="_R1_001.fastq", full.names = TRUE))
fnRs <- sort(list.files(path, pattern="_R2_001.fastq", full.names = TRUE))
```

Let’s translate that into English.
Make a variable and assign this variable to sort the files listed in “path”. But only sort the files that contain “_R1_001.fastq”. And use “_R1_001.fastq” to sort them. However, in the output, include the whole name.
You can view the output of this by typing “fnFs” and running it. It should look like this 
```
> fnRs
```
`[1] “./bioinfomatics_course/MiSeq_SOP/F3D0_S188_L001_R2_001.fastq”
[2] “./bioinfomatics_course/MiSeq_SOP/F3D1_S189_L001_R2_001.fastq”
[3] “./bioinfomatics_course/MiSeq_SOP/F3D141_S207_L001_R2_001.fastq”
[4] “./bioinfomatics_course/MiSeq_SOP/F3D142_S208_L001_R2_001.fastq”
[5] “./bioinfomatics_course/MiSeq_SOP/F3D143_S209_L001_R2_001.fastq”`

So that's great, but what does that mean? fnFs and fnRs point to our fastq files on our computer. We can also perform functions on those files by using fnFs to point at them.
> ![image](https://github.com/user-attachments/assets/1b3e419c-d520-474a-9ac1-f75e9293958e)





 



























## 5. Menyiapkan data sampel
Sampel berupa berkas dengan format FASTQ telah tersedia di server. Untuk memudahkan, buat symbolic link (symlink) ke direktori kerja Anda. Sebelum melanjutkan, agar memudahkan Anda, aktifkan dahulu environment base dengan perintah berikut.
```
conda activate base
```
### 5.1 Buat direktori kerja
Buatlah sebuah direktori untuk proyek ini, contoh: avian_worskhop.
```
mkdir avian_workshop
```
Masuk ke direktori kerja Anda
```
cd avian_workshop
```
### 5.2 Buat symlink untuk sampel
Buat symlink untuk sampel dengan perintah berikut
```
ln -s /mgpfs/data/workshop/SRR15421342.sampled10K.fastq
```
Lihat hasilnya dengan perintah `ls` seperti berikut
```
ls
```
Jika berhasil, akan ada sebuah berkas bernama `SRR15421342.sampled10K.fastq`.
Berkas FASTQ tersebut merupakan berkas SRR15421342 yang diunduh dari DDBJ dan telah dilakukan resampling sebanyak 10K reads. Berkas asli berukuran 24GB, adapun berkas resampling hanya berukuran 0.5GB.
## 6. Periksa sampel dengan NanoPlot
Aktifkan NanoPlot env
```
conda activate nanoplot
```
Jalankan NanoPlot
```
NanoPlot --fastq SRR15421342.sampled10K.fastq -t 4 -o nanoplot_result
```
Jika berhasil, output NanoPlot ada di direktori 'nanoplot_result'.
```
ls nanoplot_result
```
Tampilan hasilnya seperti gambar berikut.
![Screenshot 2024-07-11 at 06 54 00](https://github.com/siryanto/BRIN-ONT-avian-workshop/assets/30887367/31c225ad-1d9b-4c8f-b768-07806a0f58f2)
Lihat summary statistik dengan perintah `less` seperti berikut
```
less nanoplot_result/NanoStats.txt
```
Hasilnya seperti di bawah ini
```
General summary:
Mean read length:               26,687.2
Mean read quality:                  11.5
Median read length:             19,427.0
Median read quality:                13.0
Number of reads:                10,000.0
Read length N50:                45,270.0
STDEV read length:              26,049.6
Total bases:               266,871,520.0
Number, percentage and megabases of reads above quality cutoffs
>Q10:   8400 (84.0%) 228.8Mb
>Q15:   1588 (15.9%) 44.3Mb
>Q20:   0 (0.0%) 0.0Mb
>Q25:   0 (0.0%) 0.0Mb
>Q30:   0 (0.0%) 0.0Mb
Top 5 highest mean basecall quality scores and their read lengths
1:      19.3 (262)
2:      19.0 (1)
3:      18.5 (349)
4:      18.5 (1076)
5:      18.2 (274)
Top 5 longest reads and their mean basecall quality score
1:      347973 (15.0)
2:      238044 (14.0)
3:      225172 (13.6)
4:      202808 (16.2)
5:      199725 (14.9)
```
Untuk keluar dari perintah `less`, tekan tombol 'q' di keyboard Anda.

Hasil plot berupa citra PNG dan report HTML dapat diunduh dengan SFTP manager seperti: FileZilla, CyberDuck, dll. (Akan dijelaskan di akhir sesi jika masih ada waktu)
## 7. Assembly menggunakan Flye
Aktifkan Flye env
```
conda activate flye
```
Jalankan flye
```
flye --nano-raw SRR15421342.sampled10K.fastq --threads 4 --out-dir flye_result --scaffold
```
Jika berhasil, akan muncul tampilan summary statistic seperti gambar di bawah ini
![Screenshot 2024-07-11 at 10 24 50](https://github.com/siryanto/BRIN-ONT-avian-workshop/assets/30887367/6711f8ba-061d-419c-a3e3-9b00e4480ee5)

Selain itu, terdapat direktori baru bernama `flye_result`. Hasil flye tersimpan di direktori tersebut, lihat dengan perintah `ls`.
```
ls flye_result
```
Berikut contoh hasil luaran Flye.
![Screenshot 2024-07-11 at 10 01 42](https://github.com/siryanto/BRIN-ONT-avian-workshop/assets/30887367/a071a757-5c78-4d5e-9bc9-7401f0bfc344)
Melihat overview hasil assembly Flye dari file `assembly_info.txt`.
```
less flye_result/assembly_info.txt
```
Contoh hasilya seperti berikut ini
```
#seq_name       length  cov.    circ.   repeat  mult.   alt_group       graph_path
contig_3        116765  13      N       Y       3       *       *,3,*
contig_4        102545  5       N       Y       1       *       *,4,*
contig_25       79521   11      N       Y       1       *       *,25,*
contig_5        78712   5       N       Y       1       *       *,5,*
contig_7        68998   3       N       Y       1       *       *,7,*
contig_6        58546   20      N       Y       3       *       *,6,*
contig_19       58348   13      N       Y       2       *       *,19
contig_15       51147   17      N       Y       4       *       *,15,*
contig_16       49743   3       N       Y       1       *       *,16,*
contig_14       46502   3       N       Y       1       *       *,14,*
contig_12       45424   3       N       Y       1       *       *,12,*
contig_18       37337   3       N       Y       1       *       *,18,*
contig_11       31646   7       N       Y       1       *       *,11,*
contig_21       23842   12      N       Y       2       *       21,*
contig_24       20178   11      N       Y       1       *       *,24,*
contig_13       19472   21      N       Y       4       *       *,13,*
contig_17       17260   5       N       Y       1       *       *,17,*
contig_1        14846   20      Y       Y       3       *       1
contig_9        13003   16      N       Y       2       4       *,9,*
contig_2        12104   7       N       Y       2       2       *,2,*
contig_8        7907    3       Y       Y       1       *       8
contig_10       1426    37      Y       Y       6       *       10
```
Ps. Untuk keluar dari `less`, tekan tombol 'q' di keyboard

## 8. Polishing menggunakan Medaka
Aktifkan Medaka env
```
conda activate medaka
```
Jalankan `medaka_consensus` sebagai berikut
```
medaka_consensus -i SRR15421342.sampled10K.fastq -d flye_result/assembly.fasta -o medaka_consensus_result -t 4 -f -m  r1041_e82_400bps_hac_g632
```
Catatan: opsi `-m  r1041_e82_400bps_hac_g632` merupakan model yang digunakan, disesuaikan dengan platform yang digunakan. Untuk kasus ini, karena sampel dilakukan seqeuncing dengan PromethION dan kita asumsikan menggunakan basecalling mode HAC, maka model tersebut yang digunakan. Selain itu, jika model tersebut belum diunduh, maka saat menjalankan `medaka_consensus` komputer/server/node harus dapat mengakses internet untuk mengunduh model yang digunakan.

Berkas yang dihasilkan bisa dilihat dengan perintah `ls`
```
ls medaka_consensus_result
```
Hasilnya sebagai berikut
```
calls_to_draft.bam
calls_to_draft.bam.bai
consensus.fasta
consensus.fasta.gaps_in_draft_coords.bed
consensus_probs.hdf
```

Keterangan

`consensus.fasta` Hasil konsensus setelah polishing

`calls_to_draft.bam` Alignment dari reads yang sudah dikoreksi

`calls_to_draft.bam.bai` Alignment index

`consensus.fasta.gaps_in_draft_coords.bed` Information about gaps in the draft assembly where the consensus sequence could not be aligned or corrected

`consensus_probs.hdf` Provides probabilistic information about the consensus sequence, offering insights into the confidence of each base call
