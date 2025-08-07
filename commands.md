# COMMANDS 

# SUDO

* su - : su, "switch user" kısaltması olarak gelir. su'dan sonra gelen - ile hedef kullanıcının oturum açma ortamını da yüklemesi emredilir.
* su kullanici_adi formatiında kullanılıyor olsaydı bu bahsi geçen kullanıcının oturum açmasına izin veriridi ama bir önceki kullanıcının oturum açma ortamını saklardı.
* su - kullanici_adi formatında yazsaydık yeni kullancının kendi oturum açma ortamı ile gelmesini emrediyor olurduk.
* biz burada kullanırken herhangi bir kullanici adı belirtmediğimiz için direkt root kullanıısına geçiş yapılıyor. 

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

sudo grubuna eklediğimiz kullanıcıya sudo yetkileri vermek için "sudo visudo" komutunu giriyoruz. bizim sudoers diye bir dosyamız var. bu dosya sudo'nun nasıl davranacağını belirleyen kuralları içerir. visudo ile bu dosya içerisine girip "asay ALL=(ALL:ALL) ALL" komutunu ekliyoruz. asay kullanıcısına tüm yetkiler verilmiş oluyor. sonrasında kaydedip çıkıyoruz. visudo'nun özelliği, kaydedip kapatırken sudoers dosyasına özgü syntax kontrolü veya herhangi hatalı bir giriş olup olmadığını kontrol eder. sudo çok geniş yetkiye sahip olduğu için herhangi bir hata programın çalışmasını engelleyebilir. Kısaca amacı güvenlik.

- izinleri eklediğimiz dosya /etc/sudoers dosyası. istenilirse bu dosyaya nano /etc/sudoers ile de değişiklik yapılabilir. Ama güvenlik amaçlı visudo daha iyi.

   _peki bu ALL=(ALL:ALL) ALL tam olarak kime neyin "ALL" iznini veriyor?_

_genel format şu şekildedir: kullanıcı_veya_grup host=(çalıştırılacak_kullanıcı:çalıştırılacak_grup) komutlar_

_* ilk 'ALL': belirtilen kullanıcının hangi host'larda bu yetkilere sahip olacağını belirtir._
_* ikinci ve üçüncü, parantez içi 'ALL' ifadeleri : (çalıştırılacakkullanici:calistirilacakgrup) formatında kullanılır. ilk ALL ifadesi komutun hangi kullanıcı adına, parantez içerisindeki 2.ALL ifadesi komutun hangi grup adına çalıştırılacağını belirtir._
_* son ALL ifadesi: belirtilen kullanıcının hangi komutları çalıştırabileceğini belirtir. örneğin sadece apt update komutu kullanmasına izin vermek için /usr/bin/apt update ile değiştirilebilir._

* kuralları yeniledikten sonra "reboot" komutu giriyoruz.
* sudo -v: komutun açılımı sudo "validate". Bir kullanıcı sudo komutunu ilk kullandığında, sistem kullanıcıdan parolasını girmesini ister. Doğru parola girildikten sonra, sudo yetkisi
varsayılan olarak 5 dakika boyunca geçerli olur. Bu süre içinde tekrar sudo komutunu kullandığınızda, sistem sizden tekrar parola istemez. Bu komut girildiğinde kullancı "validate" edilmiş
(doğrulanmış) olur. Bu komutla sudo yetkiniz 5 dakika daha uzatılmış olur.

------------------------------------------------------------------------------------------------------------------------------------------------------------

# SSH Yükleme
* sudo login asay: bu komut sayesinde root'tan kendi hesabımıza geçiş yapmış oluyoruz.
* sudo apt install openssh-server -y:
    - sudo: sistem genelinde bir kurulumu olduğu için yönetici yetkisi gerekir.
    - apt install openssh-server: apt paket yöneticisine openssh-server kurulmasını söyler. openssh-server, başka bir bilgisayardan sanal makineye bağlanmamızı sağlayan ssh sunucu yazılımıdır.
        - SSH Sunucusu Nedir?
      SSH (Secure Shell), başka bilgisayardan sanal makineye ağ üzerinden güvenli bir şekilde bağlanma için kullanılan bir protokoldür(bir dizi kurallar bütünü). Bağlantının sağlanabilmesi sdsduiçin de bilgisayarda openssh-server gibi bir yazılımın olması gerekir.
* sudo systemctl status ssh: ssh  çalışıyor mu çalışmıyor mu durumuna bakmak için kullanılır.
    - systemctl: "system control" kısaltması olarak gelmiştir. sistemleri yönetmek için kullnaılır. sistem başlatma, durdurma, yeniden başlatma gibi işlemler içindir.
    - sudo yetkisiyle ssh protokolünün 'status'unu kontrol eder.
  * ssh servisi kapalı olsaydı açmak için: sudo service ssh start, yeniden başlatmak isteseydik: sudo service ssh start komutları da kullanılabilirdi.

------------------------------------------------------------------------------------------------------------------------------------------------------------

# Port İşlemleri
subject't bizden 4242 portuna bağlanmamız isteniyor. önce bunu yapıyoruz.

* sudo nano /etc/ssh/sshd_config: sshd_config dosyası, SSH sunucusunun ana yapılandırma dosyasıdır, ssh sunucusunun davrsnışlarını kontrol eden kuralları içerir. Burada bulunan Port 22,
ssh portudur. SSH'ın kendisi, Port 22 yani.
* Port 22 satırını Port 4242 ile değiştiriyoruz._"PermitRootLogin prohibit-password"_ satırını _"PermitRootLogin no"_ ile değiştiriyoruz. Bu ikinci değişikliğin amacı güvenliği sağlamaktır.
  Bu sayede root kullanıcısının SSH üzerinden giriş yapması tamamen engellenir. root gibi önemli bilgiler içeren bi hesabı dışarıdan bağlantıya kapatıyoruz aslında.
* değişiklikleri kaydetmek için sudo service ssh restart dedikten sonra sudo service ssh status diyerek ssh durumunu kontrol ediyoruz. Status çıktısında 4242 portunun dinlneiyor olduğu
yazmalı. Eğer istenen çıktı alınamıyorsa _"sudo reboot"_ ile tekrar başlatıp tekrar denenebilir.
* sudo grep Port /etc/ssh/sshd_config: Bu komut ile dosya içinde port eşleşmesi bulunup ekrana yazdırılacağı için bağlanmada sıkıntı çıkmadıysa Port 4242 eşleşmesi bulunur ve yazdırılır.

------------------------------------------------------------------------------------------------------------------------------------------------------------
# Güvenlik Duvarı, UFW İşlemleri
* sudo apt-get install ufw -y: UFW(Uncomplicated FireWall) indirdiğimiz komut.
* sudo ufw enable: UFW'nin aktif edilmesi için gerekli komut.
* systemctl komutunun kullanımından bahsetmiştim. yine aynı formatla burda da kontrol yapabiliriz: sudo systemctl status ufw

Güvenlik duvarının kurallarına 4242 Port'unu eklemmemiz gerek. UFW, gelen ve giden ağ trafiğin kontrol eden bir güenlik duvarı olduğu için üzerinden işlem yapılacak portları buray tanımlamam 
gerekir. 

Benim rehber aldığım pdf'te SSH'ın ufw kurallarına tanıtılması gerektiği yazıyor. Ama zaten subject dosyası sadece 4242 portu kalsın dediği için portlar arasında Port 22 olmayacak. O yüzden ben onu bu adımda kurallara eklemiyorum.

* **sudo ufw allow <port_number>**: bu komut ile ilgili port'u ufw kurallarına tanıtmış oluyorum. Bu sayede bu port'tan gelen bağlantılara izin veriliyor.
* herhangi bir port'u silmek istersem **sudo ufw delete <numara>** ile silebilirim. Burada numara olarak bahsedilen şey, baştan kaçımcı kuralın silineceği.

  <img width="463" height="177" alt="image" src="https://github.com/user-attachments/assets/ea247e2b-19b1-4acf-8a9b-8eefe144f1d6" />
  
    örneğin bu kurallar arasında 22 portunu kaldırmak için sudo ufw delete 1 ve sudo ufw delete 3 komutlarını uygulamalıyım.
------------------------------------------------------------------------------------------------------------------------------------------------------------

# Sanal Makineyi Terminale Bağlama
<img width="650" height="384" alt="image" src="https://github.com/user-attachments/assets/b8e45c0f-e129-4bf7-982a-b1f2e1c93d3a" />

ekteki görsel üzerinden host port ve guest port ayarlayarak sanal makineyi bilgisayar terminaline bağlayacağız.

* host port: ev sahibinin kullandığı porttur. Kendi bilgisyarınıza dışardan bağlanmak istediğinizde bu portu kullanırsınız. dışarıdan gelen bağlantılar bu porta bağlanır ve sonra sanal makineye yönlendirilir. Kısaca burası dışarıya açılan kapıdır.

* guest port: sanal makinenin içinde çalışan uygulama ya da hizmetlerin kullandığı porttur. Dışarıdan gelen bağlantı host port'a ulaştığında host port tarafından ilgili guest port'a yönlendirilir.

bir somutlaştırma yaparsak, host port'u bir apartman sitesinin girişlerini kontrol edip, ilgili apartmana yönlendiren güvenlik; guest port'u ise ilgili apartman olarak düşünebiliriz. ziyaretçi (dış bağlantı), güvenliğe (host port) gelir. güvenlik ise onu ilgili daireye (guest port) yönlendirir.

terminale **ssh -p host_port_numarası kullanici_adi@127.0.0.1** komutu girilidiğinde;
- -p host_port_numarası: SSH bağlantısı için kullanılacak port numarasını belirtir.
- kullanici_adi: sanal makineye giriş yapacak kullanıcı adıdır.
- @127.0.0.1: bağlanılmak istenilen IP adresi. localhosy olarak da bilinen fiziksel bilgisayarın kendisine ait IP adresidir. Biz de o bilgisyardaymış gibi eriştiğimiz için bu IP'yi kullanıyoruz. 127.0.0.1 (kendilerine localhost da deniyormuş), her bilgisayarda bulunan default bir IP adresidir. bilgisyarın ev adresi gibi bişi.
    
    * Terminale bağlanırken belli bir kulanıcı adı belirterek bağlanıyoruz. Peki bağlandığımız kullanıcıdan farklı bir 
    kullanıcıyla işlem yapmak istersek ne olur?
        sudo ve su - komutlarını kullanarak kullanıcı değişiklikleri yapılabilir. sudo, tek bir komutu başka bir kullanıcının 
        (genellikle root’un) yetkisiyle çalıştırmanızı sağlar ve kendi şifrenizi ister; sudo -u kullanıcı ile farklı kullanıcı 
        adına da kullanılabilir. su ise terminal oturumunu tamamen başka bir kullanıcıya geçirir, geçiş yapılacak 
        kullanıcının şifresi gerekir ve yeni oturumdan exit ile çıkılır.
        
    
------------------------------------------------------------------------------------------------------------------------------------------------------------

# Kullanıcı Adı Değiştirme
* sudo hostnamectl set-hostname yeni_host_adi: yeni host name ayarlamak için kullanılır. "hostname" konmutu sistemin hostname'ini ayarlmak için kullanılan bir araçtır.
ilk ayarlandıktan sonra sudo hostname komutu ile hostname'i kontrol etmek istediğimizde sudo kurallarını bu host için ayarlamadığımızdan bir uyarı verecek. bunu düzeltmek için;
- sudo nano /etc/hosts komutunu girdikten sonra açılan dosyada eski kullanıcı ismimizi yenisiyle değiştiriyoruz. bu değişiklik bilgisyar yerel IP'sindeki hostname ile bizim ayarladığımız hostname'in eşleşmesini sağşlamak içindir.
- bu dosyada yeni kullanıcı adının karşısında 127.0.1.1 gibi bir IP adresi var. Bu kullanıcımıza atanmış, bilgisyara ait statik bir IP adresi.
- ekte etc/hosts dosyasının içeriği bulunuyor:
  <img width="466" height="167" alt="image" src="https://github.com/user-attachments/assets/45ad7fb1-170d-4075-b157-9e6898743ca3" />
- IP adreslerini hostnamee ile eşleştirdiğimiz kısmın altında IPv6 hakkında bazı bilgiler var, evoda karşılaşılırsa diye çok ufak araştırdım. IPv6, en güncel internet protokülüdür. Daha geniş adres alanına ve geliştirilmiş bazı özelliklere sahiptir.

------------------------------------------------------------------------------------------------------------------------------------------------------------
# Sudo Yapılandırma
* sudo visudo komutuyla sudoers dosyasına giriş yapıyoruz.
* dosyaya aşağıdaki komutları ekliyoruz:
    - Defaults    log_input,log_output: kullanıcının sudo ile çalıştırdığı komutların girdi ve çıktılarını kaydetmesini sağlar.
    - Defaults    logfile=”/var/log/sudo/sudo.log”: ilgili tüm logların hangi yola kaydedileceğini belirtir.
    - Defaults    requiretty: TTY modunu aktif etmek için. Sudo komutunun sadece geçek bir terminal üzerinden bağlanması içindir. Aksi takdirde kötü niyetli bir yazılım terminal bağlantısı olmadan sudo dosyasına giriş yapabilir. bu tarz bir durumu engelliyoruz. 
          * TTY Modu Nedir: "Teletypewriter"dan gelir. Kullanıcının girdisi karşılığında bir çıktı veren oturumdur. Kısaca terminal ya da konsol arayüzü.
       
    - Defaults    secure_path=” /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin”: bu satırı ekleyerek sudoya girilecek komutların hangi yollarda bulunduğunu belirtiyoruz. bu da aslında güvenlik amaçlı bir aşama. dışarıdan kötü niyetli bir komut gönderildiğinde bu komut bu yolda olmadığı için terminal bu komutu çalıştırmayacak.
          * belirtilen path'ler sudonun çalışması için Linux default dizinleridir. örneğin /sbin, "system binary" kısaltımıdır ve sadece root'un                         kullanabileceği, sistem açma, kapama gibi komutları tutar. /usr/bin, kullanıcının günlük hayatında kullandığı standart program,  dosya vs. içerir.
      
    - Defaults    passwd_tries=3: sudo şifresinin doğru girilmesi için 3 deneme hakkı verir.
    - Defaults    badpass_message=”<belirttiğiniz hata mesajı>”: yanlış sudo şifresi girilidiğinde yayınlanacak hata mesajı.
 
------------------------------------------------------------------------------------------------------------------------------------------------------------
# Şifre Politikası Ayarlama
* sudo nano /etc/login.defs komutunu çalıştırıyoruz. /etc/login.defs dosyasına giriş yapıyoruz. Bu dosya Linux'ta kullanıcılar ve  şifre poltiikaları için varsayılan ayarları tutan bir dosyadır.
* dosyaya subject'in söylediği şifre politikasına ait kuralları ekliyoruz :
PASS_MAX_DAYS   30: kullanıcı parolasının max. 30 gün boyunca geçerli kalacağını söyler. 30 gün sonunda şifre değiştirme gerekir.
PASS_MIN_DAYS   2: kullanıcı şifresini değiştirdikten sonra tekrar değiştirmedn en az 2 gün kullanmalıdır. 
PASS_WARN_AGE   7: parolasının süresi dolmasına 7 gün kala kullanıcıya bir uyarı mesajı göndermek içindir. Bu sayede kullanıcı 7 gün sonra şifre değişikliğine gitmesi gerektiğini bilir.
Bu değişiklikleri root kullanıcısı ve kendi kullanıcımız için uygulamamız gerek. Aşağıdaki komutları yazıyoruz:

- sudo chage –-mindays 2 <user>
- sudo chage –-maxday 30 <user>
- sudo chage –-warndays 7 <user>

* değişiklikleri kaydettikten sonra **sudo chage -l kullanici_adi** komutu ile kontrol edilebilir.
      - sudo chage -l kullanici_adi: belirtilen kullanıcının parola yaşı, son parola değiştirme tarihi gibi bilgileri listeler.
      - chage: "change" ve "age" kavramlarından gelir. parola politikalarını uygulayarak listelenmesini sağlar.
Aynı işlemleri root kullanıcısı için de tekrar ediyoruz. Subject bizden root kullanıcısının da şifre politikalarına tabii olması gerektiğini söylemiş.

 ------------------------------------------------------------------------------------------------------------------------------------------------------------
 # etc/pam.d/common-password Dosya Değişiklikleri

 * sudo apt install libpam-pwquality -y: libpam-pwqualty adlı yazılım paketini yüklüyoruz.
       - PAM sistemi oturum açma, parola değiştirme gibi işlemleri yöneten bir sistemdir.
       - pwquality ise "password quality"de gelir.
       - kısaca libpam-quality, parla uzunluğu, karakter çeşitliliği, kullanıcı adı ile şifre benzerliği, ardışık karakterler gibi durumları handle'lar.
       - koyduğu kurallara /etc/securit/pwquality.conf path'i ile ulaşabilirsiniz.
* paketi kurduktan sonra /etc/pam.d/common-password dosyasında bazı değişiklikler yapmamız gerek;
  
    ucredit=-1 : En az 1 adet büyük karakter olması için, -1 değeri almasının sebebi pam_pwquality sisteminden kaynaklanır.

pam_pwquality sisteminde -1, zorunluluk belirtir. 1 olması, sistemde "puan" mantığındadır. örneğin burayı 1 yapsaydık şifredeki her bir büyük karakter için sistemin 1 credit eklenirdi. PAM sisteminde bu mantıkla şifre için credit'ler kullanılarak şifreye not verilir, ve iyi bir şifre olup olmadığına karar verilir. Örneğin parolada 3 rakam bulunuyorsa şifre bunun için 3 puan kazanır, ancak en az bir tane sayı yoksa -1 puan yer. (dcredit=-1 verilmiş çünkü) aşağıdaki credit atamaları da bu mantıkla çaılışır. 

- lcredit=-1 : En az 1 adet küçük karakter olması için
- dcredit=-1 : En az 1 adet sayı olması için
- maxrepeat=3 : En fazla 3 adet karakterin ardışık olması için
- usercheck=1 : Şifre kullanıcı adını içeriyorsa şifrenin geçersiz olması için
- difok=7 : Yeni oluşturulacak şifrenin, eski şifrenin içermediği en az 7 karakteri içermesi için
- enforce_for_root : Tüm bu değişikliklerin “root” kullanıcısına da uygulamak için
- minlen=10 : Şifrenin en az 10 karakter uzunluğunda olması için

- obscure : Parola kontrol aracıdır. Parolanın basit olup olmadığını kontrol eder. Zaten libpam-pswquality bu işi fazlasıyla yaptığı için çok fazla tercih edilmeyebilir. 
- sha512 : SHA-512 algoritması, şifreyi veritabanına kaydetmeden önce güçlü bir şifreleme için kullanılan hash'ler. Çoğunlukla sistemlerde saklanan şifrelerimizi 3. kişilerden korumak için kullanılır. SHA-512 algoritması şifreyi alıp farklı karakter ve sembollerle hash'ler (şifreler). Bir veriden hash değeri üretmek kolayken, bu değişmiş hash değerinden orijinal şifreyi bulmak imkansızdır. bu özellikler SHA-512'yi güvenilen bir hash algoritması yapar.
 <img width="600" height="284" alt="image" src="https://github.com/user-attachments/assets/9236a4ea-4fe1-4016-9a49-aa69c197bd29" />

 ------------------------------------------------------------------------------------------------------------------------------------------------------------
 # Yeni Kullanıcı 
* sudo adduser yeni_kullanici_adi : yeni kullanıcı oluşturmak için komut
      -> Bu yeni kullanıcı için istenen şifreyi biraz önce koyduğumuz şife politikalarına uygun ayarlamamız gerekiyor.
* sudo getent passwd <username>: ilgili kullanıcı adı hakkında sistemdeki passwd veritabanından bilgi alan bir komut.
      -> örnek bir çıktıyı ekte bulabilirsiniz:
<img width="707" height="268" alt="image" src="https://github.com/user-attachments/assets/99a556ce-1e74-42cc-a977-bde54442f7b3" />
- bu çıktıda en baştaki x, yetersiz yetki sebebiyle görüntülenmeyen kullanıcı şifresidir.
- 1000 sayısı kullanıcı kimliğidir. Sistemin kullanıcıyı tanımak için kullandığı benzersiz bir numaradır. 1000 genellikle sistemdeki ilk normal kullanıcının aldığı kimlik numarasıdır.
- diğer 1000 ise kullanıcının birincil olarak ait olduğu grubun kimlik numarasıdır. Çoğu zaman kullanıcı adı ve grup adları aynıdır ve aynı ID'yi paylaşırlar.
- /home/aleyna kısmı, aleyna kullanıcısının ana dizinidir.
- /bin/bash kısmı ise kullanıcının oturum açtığında çalışacak olan komut satırıdır.

* id <user> : bu komut ilgili kullaıcının kullanıcı ve grup üyelikleri bilgisini görüntülemek içindir. kullanıcı hangi gruplar üye vs. buradan görüyoruz. eğer user değişkeni olmadan çalıştırırsak o an oturumu açık olan kullaıcı için bilgiler getirilir.
* içinde bulunduğumuz kullanıcı hesabının hangi gruplarda olduğunu görmek için: **groups** komutu kullanılır. groups <user> ile de ilgili kullanıcı için gruplar bulunur.
* sudo deluser <user> : Linux sisteminden bir kullanıcının hesabını silmek içindir.
* sudo deluser –remove-home <user> : gibi bir komutla silseydik kullanıcıyı, dosyalarının da olduğu ana diziniyle birlikte silerdi.
* sudo deluser --remove-all-files <user> : bu komut ise kullanıcıya ait ne varsa her şeyi siler.
* compgen -u : sistemdeki tüm kullanıcıların bir listesini verir.
      -> compgen : "completion generator”den gelir. Bash kabuğunda komut ve isim tamamlama (completion) için kullanılabilecek bir araçtır.
      -> -u : -u komutu ile 'compgen'in listeleyeceği şeyn 'user'lar olduğunu belirtiyoruz.
* sudo passwd : bu komut ile root hesabımızın şifresini değiştirebiliriz. sudo passwd <user> formatı ile de ilgili kullanıcının şifresi değiştirilebilir. sadece 'passwd' komutu ile de içinde bulunduğumuz kullanıcı şifresi değiştirilir.

------------------------------------------------------------------------------------------------------------------------------------------------------------
  # Yeni Grup
  * sudo addgroup <groupname> : grup eklemek için.
  * sudo adduser <user> <groupname : ilgili kullancııyı ilgili gruba eklemek için.
  * groups <user> : bu komut ile de ilgili kullanıcının içinde bulunduğu gruplar listelenir.
  * sudo delgroup <groupname> : Bir grubu silmek için
  * sudo deluser <user> <groupname> : ilgili kullanıcıyı ilgili gruptan siler.

------------------------------------------------------------------------------------------------------------------------------------------------------------
# monitoring.sh Dosyası
* monitoring.sh, bir Linux sisteminin veya çalışan servislerinin durumunu izlemek için oluşturulmuş bir shell scriptidir. CPU, RAM ve disk kullanımlarını kontrol eder. Hata durumlarını kontol etmek için dosya ve uygulama loglarını tarar. 
* subject dosyasında her 10 dakikada bir monitoring.sh içeriğini yazdırmamız isteniyor. Bunun zaman ayarını yapabilmek için **"crontab"** dosyası ayarlamamız gerekiyor.
* **"Netstat"** komutu, bilgisayarın ağ bağlantılarını ve hangi portların dinlendiğini gösterir. Örneğin, web veya SSH servislerinin çalışıp çalışmadığını bu komutla görebilirsiniz. Biraz eski bir araç olmasından dolayı daha yeni ve daha geniş kapsamlı araçlar da tercih edilebilir. Ancak tüm Linux'lara uyum sağlayabilecek uluslararası eski bir araç. Ayrıca sözdizimi (syntax'ı) genel olarak daha basittir.
* oluşturduğumuz monitoring.sh dosyasına komutlar eklendiğinde son hali şöyle olmalı:
<img width="1000" height="675" alt="image" src="https://github.com/user-attachments/assets/a75a29f1-61c2-42e4-b9b1-6b09c95e8979" />

    - arc=$(uname -a): Sistem mimarisi hakkında tüm bilgiyi (uname -a) alır. İşletim sistemi çekirdeği, hostname, donanım ile ilgili bilgileri içerir.
    - pcpu=$(grep "physical id" /proc/cpuinfo | sort | uniq | wc -l): Sistemdeki fiziksel CPU (işlemci) sayısını bulur.
              ->  /proc/cpuinfo, Linux'un kernel hakkında tüm bilgileri tuttuğu dosya. bu dosya içerisinde "physical id" metnini arıyoruz. "sort", grep                     çıktısını alfabetik olarak sıralar. "uniq" ile tekrar eden satırlardan sadece 1 tane bırakılır. "wc" ise _word count_'tan gelir ve -l ile                     listelenen satırları sayar.
    -  vcpu=$(grep "^processor" /proc/cpuinfo | wc -l): Mantıksal işlemci (vCPU) sayısını bulmak için tasarlanmıştır.
              -> "^processor" ifadesindeki '^', gerp'in arama yapacağı kelimenin sadece cümle başında olduğu durumları bulması gerektiğini ifade eder. Kalan                ifadelerde de farklı bir şey yok.
    - fram=$(free -m | awk '$1 == "Mem:" {print $2}'): Bu komut, sistemdeki toplam fiziksel belleği (RAM) megabayt cinsinden bulur, fram(fullram)'e atar.
              -> free -m komutu sistemin bellek alanını gösteren komuttur. -m paramtersi çıktıyı **megabyte** cinsine ayarlar.
              -> awk '$1 == "Mem:" {print $2}' kısmında, **awk** görüntü işleme görevinde bir komuttur. awk komutunu kullanarak her satırdaki ilk alanın                     ($1 kısmı bunu sağlıyor), "Mem"e eşit olup olmadığını kontrol eder. Koşul sağlanırsa satırdaki ikinci alanı yazdırmasını söyler. 
    - uram=$(free -m | awk '$1 == "Mem:" {print $3}'): Bu komut, sistemde kullanılan fiziksel belleği (RAM) megabayt cinsinden bulup uram(usedram)                 değişkenine atar.
    - pram=$(free | awk '$1 == "Mem:" {printf("%.2f"), $3/$2*100}'): Bu komut, sistemdeki bellek (RAM) kullanım yüzdesini hesaplar ve sonucu iki ondalık           basamaklı bir sayı olarak biçimlendirir.
    - fdisk=$(df -Bg | grep '^/dev/' | grep -v '/boot$' | awk '{ft += $2} END {print ft}'): Ana disk bölümlerinin toplam boyutunu gigabayt cinsinden bulur.
    - udisk=$(df -Bm | grep '^/dev/' | grep -v '/boot$' | awk '{ut += $3} END {print ut}'): Ana disk bölümlerinde kullanılan toplam alanı megabayt cinsinden       bulur.
    - pdisk=$(df -Bm | grep '^/dev/' | grep -v '/boot$' | awk '{ut += $3} {ft+= $2} END {printf("%d"), ut/ft*100}'): Ana disk bölümlerinin kullanım yüzdesini hesaplar ve tam sayı olarak gösterir.
              -> Yukarıdaki 3 komutta da -B komutu kullanılmış. Bu komut çıktıyı Megabyte ya da Gigabyte olarak ayarlar. Başka kullanımlardan biri de df,                   disk free. Disk kullanımını gösterir. grep '^/dev/' kısmı ise dev ile başlayanları yani disk bölümlerine ait olanları listeler.
              -> -v parametesi grep'in ters davranışını sergiler. satır sonuna 'boot' olanları hariç tutar. Bu, genellikle küçük boyutta olan boot bölümünün                toplam disk kullanım değerlerini etkilemesin diye yapılır. Peki neden etkilemesin istiyoruz? Bunun sebebi boot'un kullanım amacının farklı                    olması. boot dosyası kullanıcı verilerini değil, sistem dosyalarını saklar. Bu yüzden çıkartıyoruz.
      - lb=$(who -b | awk '$1 == "system" {print $3 " " $4}'): sistemin en son ne zaman başlatıldığını bulmak içindir.
              -> who -b: sisteme kimlerin bağlı olduğunu 'who' komutu söyler. -b(boot time) eklentisi, who'ya sistemin başlama zamanını göstermesini söyler.
              -> $1 == "system": Her satırın ilk alanı "system" mi diye kontrol eder.
              -> {print $3 " " $4}: Eğer öyleyse, o satırdaki 3. ve 4. alanı (genellikle tarih ve saat) boşlukla birlikte ekrana yazdırır.
      -  lvmt=$(lsblk | grep "lvm" | wc -l) ve lvmu=$(if [ $lvmt -eq 0 ]; then echo no; else echo yes; fi): Bu iki komut, sistemde Mantıksal Birim Yönetimi                 (LVM) kullanılıp kullanılmadığını kontrol etmek için birlikte çalışır. Birinci komut LVM birimlerinin sayısını bulur, ikinci komut ise bu                     sayıya göre yes veya no çıktısı üretir.
      -  ctcp=$(cat /proc/net/sockstat{,6} | awk '$1 == "TCP:" {print $3}'): toplam soket sayısını bulur. /proc/net/sockstat ve /proc/net/sockstat6 dosyalarını okur, "TCP:" satırını bulur ve 3. alanı (genellikle "inuse") alır.
      -  ulog=$(users | wc -w): sisteme şu anda giriş yapmış kullanıcı sayısını verir. -w kelime sayılacağı anlamına gelir.
      -  ip=$(hostname -I): bilgisayarın IP adresini alır. -I, makinenin IP adreslerini döndürür.
      -  mac=$(ip link show | awk '$1 == "link/ether" {print $2}'): bilgisayarın MAC adresini alır. 
      -  cmds=$(journalctl _COMM=sudo | grep COMMAND | wc -l): sudo komutuyla çalıştırılan komut sayısını verir.

Bu komutlardan sonra wall komutu bulunuyor. Wall komutu "write all" anlamına gelir. Sistmede o an oturum açmış tüm kullanıcılara termianl ekranlarına mesaj gönderir. Wall komutuna bağlı kurallarla da sistem durumu kullanıcı tarafından görülebilir. Yukarıdaki komutların çktıları burada mesajlara atanıyor. 
Wall komutu hakkında bazı önemli noktalar:
- wall mesajı, sisteme giriş yapan tüm kullanıcılara gider. Ama bazı kullanıcılar belli bir komutla terminal mesajlarını kapatabilir. Bu kullanıcılara mesaj gitmez.
- Wall komutu girdi olarak metin alır. Aldığı metni direkt olarak yazdırır.
- Normal bir kullanıcı tarafından gönderilen wall mesajı, alıcı tarafından engellenebilir. Ancak oot kullanıcısının gönderdiği mesajlar ise genellikle bu engellemeyi aşar.
- Normal kullanıcı wall komutunu kullandığında, mesajın başında kimlik bilgisi yer alır. Mesaj, kime ait olduğu belli olacak şekilde gönderilir.

monitoring.sh dosyasını oluşturduktan sonra **"sudo bash /usr/local/bin/monitoring.sh"*** ile çalıştırabilirsiniz. 

- Monitoring.sh çıktısının zaman ayarını yapmamız gerkeiyor. bunu da **"sudo crontab -e"** komutu ile yapıyoruz.
      * crontab, zamanlanmış görevleri düzenlemek içindir.
      * -e eklentisi "edit" anlamındadır.
      * başa sudo ekleyerek bunu root kullanıcısı için ayarlamış oluyoruz.
  Açılan dosyaya ***/10 * * * * bash /usr/local/bin/monitoring.sh** satırını ekliyoruz.
  - Her 10 dakikada bir anlamına gelir. Yani dakika kısmı her 10 dakikada bir (0,10,20,30,40,50) çalıştırılır.
  - bash ile monitoring.sh dosyasını çalıştırmak içindir.
      -> sonuç olarak her 10 dakikada bir monitoring.sh dosyası çalıştırılarak, Wall mesajı yazdırılır.

  Crontab’ın durumunu görmek, durdurmak ve başlatmak için şu komutları kullanıyoruz:
    * sudo systemctl status cron : Durumunu gösterir
    * sudo systemctl stop cron : Durdurur
    * sudo systemctl start cron : Başlatır
    * sudo systemctl restart cron : Yeniden başlatır



  _**MANDATORY KISIM BİTTİ.**_
 
