# Struktur Compiler

Compiler adalah sebuah program yang dapat menerjemahkan kode-kode yang ditulis dalam bahasa pemrograman menjadi bahasa yang dimengerti oleh mesin (computer) yang biasanya memiliki bentuk dalam kode biner dan kemudian dikenal sebagai *object code*. Mengapa bahasa pemrograman perlu diterjemahkan menjadi bahasa mesin? Jawaban paling umum adalah karena dengan mengubah bahasa pemrograman menjadi bahasa mesin, kita dapat membuat sebuah *executable program*.

Compiler menjembatani program yang ditulis dalam bahasa pemrograman dengan hardware yang mendasarinya. Sebelum membuat *executable program*, compiler akan memverifikasi sintaks dari kode yang dituliskan , kemudian menghasilkan *object code* yang efisien, mengeksekusi *run-time*, dan memformat output sesuai dengan konvensi dari *assembler* dan *linker*. Pada awal-awal compiler dibentuk, pendekatan yang dilakukan pada desain compiler dipengaruhi oleh kompleiksitas pemrosesan, pengalaman sang perancang, dan sumber daya-sumber daya yang ada. Apabila compiler yang akan dibuat digunakan untuk bahasa yang relative sederhana, pembuatan compiler tersebut mungkin merupakan monolitik *software*. Namun apabila compiler tersebut dibuat untuk bahasa pemrograman yang cukup rumit dan dibutuhkan output yang kualitasnya tinggi, perancangannya umumnya dipecah menjadi beberapa bagian kecil yang bersifat independen. Maksud dari independen disini alah setiap bagian kecil tersebut dapat dikerjakan oleh orang yang berbeda. Dengan metode ini, dapat dilakukan *maintenance* dengan mudah dan dapat menambahkan fitur baru dengan mudah apabila diperlukan.

Production Quality Compiler-Compiler Project (PQCC) memperkenalkan istilah *front end*, *middle end*, dan *back end*. Tidak ada batas yang jelas di mana perbatasan dari ketiga istilah ini. Hampir semua compiler (kecuali compiler kecil) memiliki lebih dari dua fase ini.

## Tiga Fase Struktur Compiler
![Images tiga fase struktur compiler](https://upload.wikimedia.org/wikipedia/commons/thumb/c/cc/Compiler_design.svg/550px-Compiler_design.svg.png)

* *Front end*: memverifikasi sintaks dari kode program yang dibuat oleh programmer. Jika ada kesalahan sintaks, maka akan dihasilkan kesalahan dan peringatan. Aspek dari *front end* adalah *lexical analytics*, *syntax analytics*, dan *semantic analytics*.

* *Middle end*: melakukan optimasi terhadap bentuk selain source code dan machine code. Contoh dari pengoptimalan yang dilakukan adalah pembuangan kode-kode yang *useless* dan *unreachable code* yang ada.

* *Back end*: mengambil hasil *output* dari *middle end*. Menghasilkan kode *assembly* yang bergantung pada target dan melakukan alokasi register dalam proses. Dalam fase ini, dilakukan pengoptimalan terhadap penggunaan kode target perangkat keras, misalnya dengan mengetahui bagaimana cara agar program dapat bekerja secara paralel dengan mengisi slot delay. Biasanya output dari *back end* adalah kode mesin untuk prosesor dan sistem operasi tertentu.

### *Front End*
![Images front end](https://upload.wikimedia.org/wikipedia/commons/thumb/5/5b/Xxx_Scanner_and_parser_example_for_C.gif/400px-Xxx_Scanner_and_parser_example_for_C.gif)

*Front end* compilator menganalsis kode sumber untuk membangun representasi internal program yang disebut IR (*Intermediate Representation*). Front end compilator juga mengelola tabel simbol yaitu sebuah struktur data yang memetakan setiap simbol dalam source code ke informasi terkait misalnya lokasi, jenis, dan *scope*.

Pada saat ini, *front end* dibagi menjadi 3 tahap yaitu analisis *lexical analysis* (disebut juga *lexing*) , *syntax analysis* (disebut juga *parsing*), dan *semantic analysis*. *Lexical analysis* dan *parsing* terdiri dari *syntactic analysis* (sintaks kata dan sintaks frase) yang pada khasus simpelnya dapat di-*generate* secara otomatis dari sintaks sebuah bahasa pemrograman, meskipun dalam kasus yang lebih kompleks tettap memerlukan modifikasi manual. Tahap *semantic analysis* biasanya lebih rumit dan lebih sering ditulis tangan, namun dapat juga menggunakan *attribute grammar*.

Dalam beberapa kasus, digunakan fase tambahan, terutama *line reconstruction* dan *preprocessing*, namun kasus ini jarang terjadi. Fase-fase tambahan yang paling sering ditambahkan adalah:

* *Line reconstruction*: bahasa yang memiliki sintaks yang bebas dalam pemrogramannya memerlukan sebuah fase sebelum *parsing* di mana fase tersebut mengubah urutan karakter masukan menjadi *canonical form* yang siap untuk di-*parsing*. Atlas Autocode dn Imp adalah bahasa *stropped* yang compiler-nya mungkin memiliki fase *line reconstruction*.

* *Lexical analysis* memecah *source code* menjadi bagian-bagian kecil yang disebut sebagai token. Setiap token adalah unit ato tunggal dari bahasa tersebut, misalnya kata kunci, *identifier*, atau simbol. Fase ini disebut juga lexing atau *scanning*, dan *software* yang melakukan *lexical analysis* disebut *lexical analyzer* atau *scanner*.

* *Preprocessing*: beberapa bahasa pemrograman, misalnya bahasa C, memerlukan sebuah fase *preprocessing* yang dapat mendukung subtitusi makro dan kompilasi bersyarat. Biasanya fase ini terjadi sebelum *syntactic* atau *semantic analysis*.

* *Syntax analysis* melibatkan *parsing* urutan token untuk mengidentifikasi struktur sintaksis program. Fase ini biasanya membangun *parse tree* yang menggantikan urutan linear token dengan sebuah struktur pohon yang dibangun sesuai dengan urutan tata bahasa formal yang menentukan sintaks bahasanya. *Parse tree* sering dianalasis, ditambah, dan ditransformasikan oleh fase selanjutnya pada compiler.

* *Semantic analysis* adalah fase di mana compiler menambahkan informasi semantik ke *pohon parse* dan membangun tabel simbol. Fase ini melakukan pemeriksaan semantic, misalnya *type checking* (misalnya pengecekan jenis *error*), atau *object binding* (variable asosiasi dan referensi fungsi dengan definisinya), atau *definitie assignment* (mewajibkan semua variable lokal untuk diinisialisasi sebelum digunakan), menolak program yang salah atau memberikan *warning*. Karena fase ini biasanya membutuhkan parse tree yang lengkap, fase ini biasanya ada setelah fase *parsing*.

### *Back End*

Istilah ini kadang membingungkan dan mirip dengan *code generator* karena fungsinya yang *overlapping* dalam menghasilkan kode *assembly*. Fase utama dari tahap back end adalah sebagai berikut:

1. Analisis: tahap pengumpulan informasi dari input. *Data-flow analysis* digunakan untuk membangun *use-define chain* bersama dengan *dependence analysis*, *alias analysis*, *pointer analysis*, *escape analysis*, dan sebagainya. Analisis yang akurat adalah dasar untuk optimasi sebuah compiler. *Call graph* dan *control flow graph* juga biasanya terbentuk selama tahap analisis.

2.	Optimasi: optimasi yang popular adalah *inline expansion*, penghapusan *dead code*, *constant propagation*, *loop transformation register allocation*, dan *automatic parallelization*.

3.	Code generation: bahasa perantara yang ditransformasikan diterjemahkan ke dalam bahasa keluaran, biasanya bahasa mesin dari sistem. Fase ini melibatkan *resource and storage decision*, seperti menentukan variable mana yang sesuai dengan register dan memori  dan pemilihan dan scheduling instruksi mesin yang sesuai.

Analisis compiler adalah prasyarat untuk optimasi compiler dan mereka saling membutuhkan dan bekerja sama. Misalnya, *dependence analysis* sangat penting untuk *loop transformation*.

Selain itu, lingkup dari analisis compiler dan optimasi sangat bercariasi, mulai dari sekecil *basic block* hingga prosedur/fungsi, bahkan keseluruhan program (*interprocedural optimization*). Analisis dan pengomptimalan interprosedural umum terjadi pada compiler kmersial modern dari HP, IBM, SGI, Intel, Microsoft, dan Sun Microsystems. Compiler *open source* seperti GCC dikritik karena waktu kompilasi yang lama akibat kurangnya optimasi interprosedural.



*sumber: wikipedia.org*
