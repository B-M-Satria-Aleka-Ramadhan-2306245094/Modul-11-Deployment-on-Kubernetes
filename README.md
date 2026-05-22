# Modul-11-Deployment-on-Kubernetes

## REFLECTION 1
### 1. Compare the application logs before and after you exposed it as a Service.
Sebelum aplikasi diekspos sebagai sebuah Service, log yang tercatat pada Pod cenderung statis dan hanya menampilkan proses startup internal aplikasi, seperti inisialisasi framework (misalnya Spring Boot) serta pemetaan endpoint. Namun, setelah aplikasi diekspos menggunakan Service dan diakses beberapa kali melalui jalur proxy (seperti kubectl port-forward atau minikube service), aktivitas log akan mengalami perubahan yang terlihat jelas. Setiap kali halaman aplikasi dibuka atau dimuat ulang (refresh) di browser, jumlah log di dalam Pod akan langsung meningkat secara berkala. Hal ini terjadi karena setiap interaksi pengguna memicu HTTP request baru (seperti perintah GET untuk mengambil aset gambar, file CSS, maupun data dari endpoint REST) yang diteruskan oleh Service langsung menuju Pod target. Akibatnya, application server di dalam kontainer akan mencatat setiap lalu lintas masuk tersebut sebagai log aktivitas baru secara real-time.

### 2. Notice that there are two versions of `kubectl get` invocation during this tutorial section. 
Dalam jalannya tutorial ini, terdapat dua versi pemanggilan perintah kubectl get yang memiliki fungsi dan tujuan berbeda dalam manajemen objek Kubernetes. Versi pertama adalah pemanggilan standar tanpa argumen tambahan, seperti kubectl get deployments atau kubectl get services, yang digunakan untuk menampilkan ringkasan informasi dasar secara cepat di terminal mengenai status komponen yang sedang aktif. Sementara itu, versi kedua menggunakan parameter tambahan untuk mengubah format keluaran, seperti penggunaan flag -o yaml (contohnya kubectl get svc <nama-service> -o yaml). Perintah versi kedua ini berfungsi untuk melakukan dumping atau mengekspor seluruh konfigurasi spesifik dari objek yang dipilih dalam format YAML lengkap—termasuk metadata otomatis dari cluster seperti nilai uid, resourceVersion, dan status internal jaringan. Perbedaan ini krusial karena versi pertama ditujukan untuk pemantauan status sekilas, sedangkan versi kedua digunakan untuk kebutuhan dokumentasi, analisis mendalam, atau sebagai cetak biru untuk menduplikasi manifest konfigurasi.

## REFLECTION 2
### 1. What is the difference between Rolling Update and Recreate deployment strategy?
Berdasarkan cara Kubernetes mengelola pembaruan aplikasi (rollout), kedua strategi ini memiliki mekanisme operasional dan dampak operasional yang sangat berbeda:

- Rolling Update (Default): Strategi ini memperbarui aplikasi secara bertahap dan perlahan. Di latar belakang, Deployment Controller akan membuat sebuah ReplicaSet baru untuk versi aplikasi yang baru, kemudian menaikkan skala (scale up) jumlah Pod baru secara bertahap bersamaan dengan menurunkan skala (scale down) Pod pada ReplicaSet lama. Proses ini menjamin tidak ada waktu henti (zero downtime), karena pengguna masih bisa diarahkan ke Pod versi lama yang tersisa selagi Pod versi baru sedang disiapkan hingga siap sepenuhnya (Ready).

- Recreate: Strategi ini adalah pendekatan yang bersifat mutlak dan langsung. Ketika pembaruan dipicu, Kubernetes akan langsung mematikan dan menghapus semua Pod yang berjalan pada versi lama secara bersamaan hingga jumlahnya mencapai nol. Setelah semua Pod lama dipastikan mati, barulah Kubernetes membuat ReplicaSet baru dan memicu pembuatan Pod versi baru. Strategi ini menyebabkan terjadinya waktu henti aplikasi (downtime) selama jeda waktu antara penghapusan Pod lama dan kesiapan Pod baru untuk menerima trafik kembali. Namun, keuntungan dari strategi ini adalah tidak adanya dua versi kode aplikasi berbeda yang berjalan secara bersamaan (no version overlap).






