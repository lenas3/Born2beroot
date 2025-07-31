# Born2beroot

**Virtual Machine Nedir?**

 Virtual machine bir donanımın kopyasıdır. Fiziksel bir bilgisayar içinde tamamen bağımsız ve izole çalışan bilgisayar ortamı oluşturur. Kısaca makinemizin içinde başka bir makine oluşturuyoruz.

  **Subject'te geçen bazi ifadelerin açıklamaları şu şekilde:**
  
- "The use of snapshots is prohibited."

  _Snapshot, Linux sistemlerde sanpshot'lar belirli bir zamandaki dosya sistemi ya da disk bölümünün kopyasını oluşturan, salt okunur anlık görüntülerdir. Yani ekran görüntüsü gibi düşünürsek sistemin o anlık durumunu kaydeder. Bu görünüt asıl dosay sisteminin kopyası olarak saklanır. Bu sayede sistemde değişkilikler yapılırsa o anki veriler güvende kalır._

- "Since it is a matter of setting up a server, you will install the minimum of services. For this reason, a graphical interface is of no use here. It is therefore forbidden to install X.org or any other equivalent graphics server. Otherwise, your grade will be 0."

    _Yeni bir sunucu kuracağımız için minimum sayıda hizmet kurmalıyız. Bu yzüden de graphical interface yasak._
    _Graphical interface(GUI), bilgisyardaki tüm görsel ögeler (örneğin ikonlar, pencereler, menüler vs.) ile etkileşime geçilmesini sağlayan sistemdir. Kısaca ekrandaki mouse imleci, ekran ya da klavye ile etkileşime geçilen her şey GUI'ın bir parçası._
    _Bahsedilen X.org ise Linux'ta kullanılan en yaygın grafik sunucusu._

- "Setting up Rocky is quite complex. Therefore, you don’t have to set up KDump. However, SELinux must be running at startup and its configuration has to be adapted for the project’s needs. AppArmor for Debian must be running at startup too."

    _3 ana noktadan bahsedilmiş: Rocky Linux ve Kdump, SELinux ve AppArmor for Debian_

    * _Rocky Linux ve Kdump: Rocky, kurumsaldüzeyde bir Linux ürünüdür. Yeni başlayanlar için daha komplike bir yapısı olabilir. Kdump ise sistem çöktüğünde belleğin bir kopyasını alarak sorunun sebeini anlamaya yardımcı bir mekanizmadır. Subject burada Rocky kuracaklara, komplike bir yapısı olduğundan dolayı, Kdump kurmanın zorunluluk olmadığını belirtmiş._
    
    * _AppArmor for Debian: AppArmor her uygulamanın sistem kaynaklarına erişimini kısıtlayan bir güvenlik duvarı gibidir. Linux çekirdeğinin bir parçası olarak çalışır.  Subject'in dediğine göre AppArmor'ın sadece sistem çalışırken değil, sistem her başladığında otomatik olarak etkinleşmesi ve çalışır durumda olması gerekir.__

    * _SELinux(For Rocky Linux): Debian'da AppArmor'un yaptığı işi benzer bir şekilde Rocky için yapar._

  **(Genel olarak Debian kullanıldığı için bu kısımlara Rocky kısımlarına ait çok detaya girmedim. Ama subject'te geçtiği için önümüze çıkabilir.)**

-----------------------------------------------------------------------------------------------------------------------------------------------------------------

  **Subject Üzerinde Geçen Bazı Kavramlar**
  
  **Aptitude ve Apt**
  
 *  Paket yönetimi için kullanılan iki önemli araç. (Paket: bir ayzılımın dağıtım, kurulum  ve  yönetimi için gerekli dosayları içeren sıkıştırılmış bi arşiv denebilir.)
  
  - Apt (Advanced Package Tool): içinde bazı komutları içeren bir paket yöneticisidir. Paketleri kurmak güncellemek, kaldırmak gibi görevler için komutlar içerir. Paket bağımlılıklarını da yönetir, yani bir paketi kurduğunuzda onun ihtiyaç duyacağı diğer paketleri de indirir ve kurar.
    
  - Aptitude: APT kütüphanelerinin üzerinde çalışan daha üst seviyeli bir paket yöneticisidir. Daha karmaşık paket bağmılıklarını yönetebilir. Herhangi bir çakışma ya da bağımlılık sorunlarında daha kapsamlı çözümler sunarak kullanıcının bunlar arasından seçim yapmasına imkan sağlar.

        * APT komut tabanlı ilerlerken aptitude komutlarla birlikte bize bir arayüz de sunar.
        * İkisi de paket bağımlılıklarına çözüm bulabilirken, aptitude daha akıllı ve gelişimiş çözümler üretir.
        * APT daha çok günlük kullanım ihtiyaçlarını karşılarken, aptitude daha karmaşık durumlariçin daha uygundur.

**SELinux ve AppArmor**
  * Linux'ta en yaygın kullanılan **Zorunlu Erişim Kontrolü (MAC- Mandatory Access Control)** sistemleridir.

- SELinux daha çok _etiket_ tabanlı bir sistem kullanır. Sitemdeki her dosya, kullanıcı ve işlem için bir etiket atanır. SELinux hangi type'ların hangi type'lara erişebileceğini tanımlayan kurallara sahip. Bir kullanıcı bir dosyaya erişmeye çalıştığında sistemdeki etiket ve kurallar kontrol edilerek erişim sağlanır ya da engellenir.

- AppArmor, Debian işletim sisteminde kullanılan bir güvenlik sistemidir. SELinux'a göre daha rahat anlaşılır ve yönetilir. Daha çok _yol(path)_ tabanlı çalışır. Güvenlik politikaları doğrudan dosya veya uygulamaların yoluna bağlı profillere bağlıdır ve o kişiler tarafından erişiliebilir.

- SELinux, AppArmor'a kıyasla daha yüksek seviyeli kontrol ve daha detaylı güvenlik sağlar.   
