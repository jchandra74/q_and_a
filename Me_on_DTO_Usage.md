# Question: *Should I use DTO or just keep using my Entity objects as ViewModel?* #

Sebelumnya saya mengembangkan aplikasi desktop dan web (ASP.NET MVC)
menggunakan Entity Framework sebagai data access dan AutoMapper untuk mapping Entity ke DTO.

Menurut Pak Jimmy perlu gak menggunakan DTO untuk mengirim data ke View dan
mengirimkan balik data ke Database?

Saya ada case seperti ini, misal ada Tabel Item dengan 50 field.

Ketika kita menarik data item dalam list untuk ditampilkan ke DataGridView / HTML Table,
sebenarnya saya hanya butuh 5 field saja untuk ditampilkan. Sebelum saya menggunakan DTO dan
AutoMapper saya menggunakan Entity Item untuk dikirim ke View. Begitu saya lihat SQL Log-nya,
semua field dikirim ke View walau saya hanya menampilkan 5 saja.

Oleh karena itu saya bikin class DTO (ItemListDTO) yang berisi 5 field saja
sehingga di Repository saat akan dikirim saya projection ke ItemListDTO terlebih dahulu.
Kemudian saya lihat SQL Log-nya, betul hanya 5 field saja yang di select.

Sampai disini saya lihat oke sih, kemudian lanjut ke halaman Add dan Edit. Untuk kasus ini,
saya agak bingung ketika di klik Save/Submit, yang dikirimkan ke BL (Business Layer)
apakah dalam bentuk DTO atau Entity?

Kalo dalam bentuk DTO berarti saya bikin class DTO baru (ItemDTO) dengan 45 field saja
karena field seperti IsDeleted, CreatedBy, CreatedDate, ModifiedBy, dan ModifiedDate, tidak
diinput user dan di BL saya mapping ke Item kemudian di insert ke database. Jadi disini,
saya melakukan 2x mapping saat di PL (Presentation Layer), mapping user input ke DTO dan
di BL, mapping DTO ke Entity.

Kalo dalam bentuk Entity, menurut saya lebih cepat kodingnya, karena tidak perlu
mapping lagi ke Entity. Jadi di PL, user input di mapping ke Entity dan di BL hanya
di insert saja. Ini kalo case-nya Add, tapi kalo Edit jadinya 2x mapping juga.
Jadi di PL, user input di mapping ke Entity dan di BL setelah di select item by ID
baru dimapping ke Entity yang dari database.

Maaf Pak Jimmy, kalo ceritanya kepanjangan :)

# Answer #
Saya sih lebih condong ke penggunaan DTO.  Apalagi kalau applikasinya sudah mulai kompleks.

Kenapa?

1. Ada kemungkinan dimana apabila kita menggunakan DB Entity object sebagai ViewModel, akan ada hal-hal yang cuma berhubungan dengan View yang tidak terdapat di dalam Entity tersebut.  Misalnya, status apakah sebuah panel harus dihide atau ditampilkan, dsb.
2. Ada pula kemungkinan yang mungkin tidak kita sadari bahwa ada field field tertentu di dalam form yang apabila form data tersebut di model bind ke Entity object akan mengakibatkan hal-hal yang tidak kita inginkan terupdate di database... Misalnya di Entity ada field Active.  Dan di HTML form yang dibuat oleh front-end developer mempunyai field Active (yang gunanya mungkin tidak sama dengan field Active di Entity object).  Tetapi karena digunakan sebagai ViewModel dan diModelBind di MVC Action, maka Entity.Active secara tidak sengaja diupdate dengan field Active yang ada di form.  Not good.

Ada juga yang mengusulkan untuk malah memisahkan antara ViewModel yang diperuntukkan sebagai input di View dan ViewModel yang dipakai dalam Posting untuk mengisolasikan incident2 seperti diatas.

Jaman sekarang dimana sudah ada AutoMapper dan code generation / scaffolding, sudah akan lebih gampang lah untuk memisahkan Entity dengan ViewModel(s).  So, complaining about having to create DTO and mapping it to Entity is no longer a good reason to be lazy.  Create the tool so you don't have to do it every single time.  Be a smart lazy programmer.  Not just a lazy programer :).  If you have to do anything more than once, automate.  There is T4 template that you can leverage or create your own CLI that can take an Entity model, inspect it and barf out your POCO DTO.

Balik lagi, perlu pertimbangan dan lihat situasi.  Kalau cuma spiking, hindari kompleksitas.  Think of it as a spectrum, dial down and dial up the separation based on your project need.

## Programmer Evolution ##

- Junior -> Just use Entity
- Journeyman -> Create a ViewModel / DTO and map Entity to and from it
- Master -> Create different ViewModel / DTO for input and output and map as needed to Entity
- God -> Automate everything that can be generated.


