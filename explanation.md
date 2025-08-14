# LVM VE DİSK  BÖLÜMLEME 

LVM yani Logical Volume Manager, özellikle Linux sistemlerindeki disk depolama alnanını daha genişletilebilir ve dinamik  bir şekilde yönetmemizi sağlayan bir teknolojidir. LVM sayesinde ayırdığımız toplam alanı havuz gibi kullanıp içinden 
istediğimiz boyutlarda sanal disk bölümleri oluşturabiliyoruz. 

LVM kullanarak istedğim boyutlarda disk bölğmleri açabiliyorum. Bonusun bir kısmı da bu adımda yapıldığı için önce "Primary" bir bölüm, sonra da "Logical" toplam 7 bölüm oluşturmamız gerekiyor. 
Primary bölümde bilgisayarın açılmasını sağlayacak işletim sistemi dosyaları bulunur. Bilgisayar açıldığında direkt olarak boot çalışır, yani primary bölüm.

Primary ayırdığımız bölüm için Mount Point'ini "boot" olarak seçiyoruz.  Sonrasında bu bölümün nerede gözükeceğine Mount Point ile karar veriyorum. Mount point ayarlandıktan sonra sadece primary volume için 'bootable flag' on hale 
getiriliyor. Bu flag'i açık hale getirerek aslında bilgisayara "boot için gerekli bilgilerin burda" diyoruz. Yani  Mount Point'i boot'a bağlayarak bilgisayarın boot edilmesi için gerekli dosyaları bulunduruyorum, d bilgisayara bootable 
flag'i on hale getirerek dosyaların primary'de olacağını haber veriyorum. Ve Primary bölüm oluştu.

Sırada Logical bölümler var. Toplam sanal makine için 32GB yer ayırmıştım. Bunun 500MB'sini Primary için ayrııp kalanın tamamını Logical bölümlere ayırıyorum. Kalan alanı root(10GB), home(5GB), swap(2.3GB), var(3GB), srv(3GB), tmp(3GB) ve  
var-log(4GB) adında 7 bölüme ayırıyorum. Yanlarındaki depolama alanları kadar yer verdim, çünkü subject'teki örnekte bu sayılar verilmiş, ben de onun üzerinden ilerledim. Sırasıyla her bölüm için 'use as' ayarı yapıp bir klasöre bağlayacağız.

* home -> use as = Ext4 -> Mount Point = /home                = kullanıcının kişisel soayalarının olduğu normal home yolu. 
* root -> use as = Ext4 -> Mount Point = /                    = home- tmp gibi diğer dosyalar / yoluna bağlanır. root, tüm dosya sisteminin köküdür.
* srv > use as = Ext4 > Mount Point = /srv                    = srv, service'ten gelir. çeşitli ağ hizmetleri verilerini bulundurur.
* swap > use as = swap area (Mount Point yok.)                = swap area, Ram yetersiz kaldığında kullanılması için yedek bir "sanal bellek" alanıdır.  
* tmp > use as = Ext4 > Mount Point = /tmp                    = sistem geçici dosyaları içindir. 
* var > use as = Ext4 > Mount Point = /var                    = var,  variables'dan gelir. yani sürekli değişen sistem verilerini tutar. e-posta kutuları, veriatabanı dosyaları gibi. 
* var-log > Ext4 = Mount Point > Enter manually = /var/log    = /var dizini altında sistem günlüklerini tutan bir bölümdür. var'dan ayrı tutularak, dışarıdan bir saldırıda ya da aşırı yoğun günlük işlemde giriş dosyaları dolarsa sistem korunur.

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# SUDO 
- ilk makineyi çalıştırınca girilmesini istenen şifre root şifresi, bu şifre disk için. Bi sabit disk ayırıp onları primary ve logical olarak bölmüştüm. O diske erişmek için şifre gerekiyor.
    * root şifresi, Linux'ta en yüksek yetkiye sahip kullanıcı root yani "süper kullanıcı" şifresidir ve root bilgisayarın tam kontrolüne sahiptir.
    * kullanıcı kendi ana dizini (/home/kullanici) üzerinde değşiklikler yapabilir. Yetki gerektiren işlemleri (burada yapacağımız sudo gibi) root şifresiyle yapabilir.
- ilk başta sudo indirmek için apt indiriyip güncelliyoruz,  sonrasında _**apt install sudo -y**_ ile sudo programını da indiriyoruz.
- bu kısım bende bir kafa karışıklığı oluşturduğu için açıklayayım: apt komutunun kendisi sudo'yu kurmuyor. apt bir paket yöneticisi ve root yetkisiyle apt kullanarak sudo paketini indiriyoruz. yani aslında apt komutu sudo paketini kurmamız
için gerekli olduğundan önce onu indiriyoruz. _(önce apt indiriyo olduğumuz için sudo'nnun apt içinde gelen bir paket olduğunu sanmıştım, yanlışmış. aklında bu şekilde şekillenen olduysa açıklamak istedim.)_
- sudo başlı başına bir programdır ve açılımı "super user do"dur. sudo komutu kullanarak bilgsiyarda en yetkin kullanıcı adına komutu çalıştırmış oluyoruz.
