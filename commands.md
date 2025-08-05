# COMMANDS 

--SUDO--

* su - : su, "switch user" kıslatması olarak gelir. su'dan sonra gelen - ile hedef kullanıcının oturum açma ortamını da yüklemesi emredilir.
* su kullanici_adi formatiında kullnaılıyor olsaydı bu bahsi geçen kullnaıcının oturum açmasına izin veiridi ama bir önceki kullanıcının oturum açma ortmaın saklardı
* su - kullanici_adi formatında yazsaydık yeni kullancının kendi oturum açma ortamı ile gelmsini emrediyor olurduk.
* biz burada kullanırken herhangi bir kullanici adı belirtmediğimiz için direkt root kullanıısına geçiş yapılır. 

apt paket yöneticisini indirmek için;
* apt update -y : sistemin ait olduğu paket yöneticisini günceller. paketi güncellemiyor, olan paketlerin listesini güncelliyor. 
* apt upgrade -y : update ile güncellenen listeye bakıp sistemde bulunan tüm paketleri güneller veya kurar.
* -y parametresi de herhangi bir onay sorusuna yes der. 

bu aşamadan sonra sudo'nun inip inmediğini kontrol için;
* dpkg -l | grep sudo : aslında bu komut sudo özelinde bir komut değildir.
    - dpkg: debian paket yöneticisi anlamına gelir.
    -  l: listeleme işi yapar.bu komut için dpkg'ye kurulu tüm paketleri listeler.
    - Pipe (|): pipe işareti kendinden bir önceki komutun çıktısını alıp kendinden sonraki komuta girdi olarak gönderir.
    - grep sudo: 'grep', kendine gelen  metin içinde belirli bir deseni arar ve eşleşen satırları ekrana yazdırır. Burada dpkg -l ile listelenen paketler arasında sudo kelimesini arıyor.

grup içerisine kullanıcı eklemek için;

* adduser asay sudo: sudo grubuna kullanıcımızı eklemek için;
* getent group sudo: gruba eklenip eklenmediğini kontrol etmek için kullanılıyor.
    - getent: "get entries" kısaltımı olarak kullanılır. genel format şudur: getent <veritabanı_adı> [anahtar_değer], sudo adı verilen bir grubun girdilerini aldığımız anlamına gelir.

sudo grubuna eklediğimiz kullanıcıya sudo yetkileri vermek için "sudo visudo" komutunu giriyoruz. bizim sudoers diye bir dosyamız var. bu dosya sudo'nun nasıl davranacağını belirleyen 
kuralları içerir. visudo ile bu dosya içerisine girip "asay ALL=(ALL:ALL) ALL" komutunu ekliyoruz. asay kullanıcısına tüm yetkiler verilmiş oluyor. sonrasında kaydedip çıkıyoruz. visudo'nun 
özelliği, kaydedip kapatırken sudoers dosyasına özgü syntax kontrolü veya herhangi hatalı bir giriş olup olmadığını kontrol eder. sudo çok geniş yetkiye sahip olduğu için herhangi bir hata
programın çalışmasını engelleyebilir. Kısaca amacı güvenlik.

* kuralları yeniledikten sonra "reboot" komutu giriyoruz.
* sudo -v: komutun açılımı sudo "validate". Bir kullanıcı sudo komutunu ilk kullandığında, sistem kullanıcıdan parolasını girmesini ister. Doğru parola girildikten sonra, sudo yetkisi
varsayılan olarak 5 dakika boyunca geçerli olur. Bu süre içinde tekrar sudo komutunu kullandığınızda, sistem sizden tekrar parola istemez. Bu komut girildiğinde kullancı "validate" edilmiş
(doğrulanmış) olur. Bu komutla sudo yetkiniz 5 dakika daha uzatılmış olur.

------------------------------------------------------------------------------------------------------------------------------------------------------------

--SSH YÜKLEME--
* sudo login asay: bu komut sayesinde root'tan kendi hesabımıza geçiş yapmış oluyoruz.
* sudo apt install openssh-server -y:
    - sudo: sistem genelinde bir kurulumo olduğu için yönetici yetkisi gerekir.
    - apt install openssh-server: apt paet yöneticisine openssh-server kurulmasını söyler. openssh-server, başka bir bilgisayardan sanal makineye bağlanmamızı sağlayan ssh sunucu yazılımıdır.
        - SSH Sunucusu Nedir?
      SSH (Secure Shell), başka bilgisayardan sanal makineye ağ üzerinden güvenli bir şekilde bağlanma için kullanılan bir protokoldür(bir dizi kurallar bütünü). Bağlantının sağlanabilmesi
    için de bilgisayarda openssh-server gibi bir yazılımın olması gerekir.
* sudo systemctl status ssh: ssh  çalışıyor mu çalışmıyor mu durumuna bakmak için kullanılır.
    - systemctl: "system control" kısaltması olarak gelmiştir. sistemleri yönetmek için kullnaılır. sistem başlatma, durdurma, yeniden başlatma gibi işlemler içindir.
    - usdo yetkisiyle ssh protokolünün 'status'unu kontrol eder.
  * ssh servisi kapalı olsaydı : sudo service ssh start, yeniden başlatmak isteseydik: sudo service ssh start komutları da kullanılabilirdi.

------------------------------------------------------------------------------------------------------------------------------------------------------------

--PORT İŞLEMLERİ--
  
subject't bizden 4242 portuna bağlanmamız isteniyor. önce bunu yapıyoruz.

* sudo nano /etc/ssh/sshd_config: sshd_config dosyası, SSH sunucusunun ana yapılandırma dosyasıdır, ssh sunucusunun davrsnışlarını kontrol eden kuralları içerir. Burada bulunan Port 22,
ssh portudur. SSH'ın kendisi, Port 22 yani.
* Port 22 satırını Port 4242 ile değiştiriyoruz._"PermitRootLogin prohibit-password"_ satırını _"PermitRootLogin no"_ ile değiştiriyoruz. Bu ikinci değişikliğin amacı güvenliği sağlamaktır.
  Bu sayede root kullanıcısının SSH üzerinden giriş yapması tamamen engellenir. root gibi önemli bilgiler içeren bi hesabı dışarıdan bağlantıya kapatıyoruz aslında.
* değişiklikleri kaydetmek için sudo service ssh restart dedikten sonra sudo service ssh status diyerek ssh durumunu kontrol ediyoruz. Status çıktısında 4242 portunun dinlneiyor olduğu
yazmalı. Eğer istenen çıktı alınamıyorsa _"sudo reboot"_ ile tekrar başlatıp tekrar denenebilir.
* sudo grep Port /etc/ssh/sshd_config: Bu komut ile dosya içinde port eşleşmesi bulunup ekrana yazdırılacağı için bağlanmada sıkıntı çıkmadıysa Port 4242 eşleşmesi bulunur ve yazdırılır.
* 
------------------------------------------------------------------------------------------------------------------------------------------------------------

--GÜVENLİK DUVARI, UFW İŞLEMLERİ--
* sudo apt-get install ufw -y: UFW(Uncomplicated FireWall) indirdiğimiz komut.
* sudo ufw enable: UFW'nin aktif edilmesi için gerekli komut.
* systemctl komutunun kullanımından bahsetmiştim. yine aynı formatla burda da kontrol yapabiliriz: sudo systemctl status ufw

Güvenlik duvarının kurallarına 4242 Port'unu eklemmemiz gerek. UFW, gelen ve giden ağ trafiğin kontrol eden bir güenlik duvarı olduğu için üzerinden işlem yapılacak portları buray tanımlamam 
gerekir. 

Benim rehber aldığım pdf'te SSH'ın da kurallara tanıtılması gerektiği yazıyor. Ama zaten subject dosyası sadece 4242 portu kalsın dediği için portlar arasında Port 22 olmayacak. O yüzden 
