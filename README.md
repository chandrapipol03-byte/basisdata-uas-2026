UAS BASIS DATA

Entity Model yang dibuat:
- Rumah Sakit
- Poliklinik
- Dokter
- Jadwal Praktek
- Pasien
- Kunjungan
- Obat
- Resep
- User

Service Lanjutan Metabase & Minio

Metabase

Metabase User Login
Email : Chandrapipol03@gmail.com
Password : Chandra25

Contoh membuat visualisasi di metabase:

1. Login
Kunjungi halaman http://localhost:3000/ dan login menggunakan akun di atas.

2. Buat dashboard visualisasi
Buka menu our analytics -> lalu klik + New -> lalu pilih dashboard -> bisa memilih collection our analytics / personal (saya memilih personal)
Di dalam dashboard kita bisa menambah / merubah visualisasi kita berdasarkan table yang sudah ada dengan New Question / Membuat query manual sendiri.

Visualisasi di metabase bisa menampilkan Pie Chart, Line Chart, Bar chart dan lain-lain.

Minio

Untuk website minio bisa dikunjungi di http://localhost:9001/ atau http://localhost:9000/
Minio harus perlu di Setup dulu sebelum di gunakan berikut penjelasan singkatnya:

1. Buat bucket baru  

2. Set status bucket menjadi PUBLIC dengan menjalankan command berikut pada service Minio:
mc alias set myminio http://127.0.0.1:9000 minioadmin minioadmin
mc anonymous set download myminio/chabud
mc anonymous set public myminio/chabud

3. Install library league/flysystem-aws-s3-v3 dan melakukan publikasi file livewire dan merubah konfigurasi disk livewire dengan 'local'

4. Menambahkan script berikut pada config/filesystems.php agar minio dapat digunakan:
'minio' => [
    'driver' => 's3',
    'key' => env('MINIO_ACCESS_KEY'),
    'secret' => env('MINIO_SECRET_KEY'),
    'region' => env('MINIO_REGION'),
    'bucket' => env('MINIO_BUCKET'),
    'url' => env('MINIO_URL') . '/' . env('MINIO_BUCKET'),
    'endpoint' => env('MINIO_ENDPOINT'),
    'use_path_style_endpoint' => env('MINIO_USE_PATH_STYLE_ENDPOINT', false),
    'visibility' => 'public',
    'throw' => false,
    'report' => false,
]

5. Merubah konfigurasi environment pada file .env:
FILESYSTEM_DISK=minio
MINIO_ACCESS_KEY=minioadmin
MINIO_SECRET_KEY=minioadmin
MINIO_REGION=us-east-1
MINIO_BUCKET=chabud
MINIO_ENDPOINT=http://minio:9000
MINIO_URL=http://localhost:9000
MINIO_USE_PATH_STYLE_ENDPOINT=true

Saya disini mengambil contoh pada bagian rumah sakit untuk menampilkan dan mengupload gambar.
Pada bagian migration rumah sakit ditambahkan:
$table->string('upload_gambar')->nullable();

Untuk menampilkan gambar pada tabel di Resource Rumah Sakit:
Tables\Columns\ImageColumn::make('upload_gambar')
                    ->label('Gambar')
                    ->disk('minio')
                    ->size(60)
                    ->searchable()

Sedangkan untuk form agar bisa upload gambar menggunakan script:
Forms\Components\FileUpload::make('upload_gambar')
->disk('minio')
->visibility('public')
->image()
->maxSize(2048)

Terimakasih.
Mungkin ini terkait UAS tentang materi lanjutan yaitu Metabase dengan visualisasi dan Minio dengan menampilkan / mengupload gambar.