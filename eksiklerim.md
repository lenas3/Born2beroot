* **siganture.txt**

.vdi uzantılı dosya konumuma gidip **shasum born2beroot.vdi** komutunu çalıştırıyorum. sonrasında makinenin hash değeri oluşturulyor. Ama bunu makineyi tammaen hazır hale getiirip
kapattıktan sonra yapmam gerek çünkü bundan sonra yapacağımız en küçük değişkilik, makineyi açmak bile (makinenin son açılıp kapanma verisi değişiyor çünkü) bu hash değerini değiştiriyor.  

* **diff komutu**
diff dosya1 dosya2 diyerek dosya1 ve dosya2 içeriğini karşılaştırabiliriz. O anki hash ile pushlanan has değerin aynı olması gerektiği hiçbir fark olmamalı, ve herhangi bir çıktı alınmamalıdır.

* **makineyi kopyalamak**
makineye sağ tık yapıp full clone ile klonlarsak ve orada değişiklik yaparsak orijinal dosyanın hash değeri değişmez. o yüzden evolar arası kopyalama yapmak daha sağlıklı olur.

**"Machine shouldn't have graphical environment"**
bu ayarı ilk kez kurulum sırasında tüm yıldızları kaldırdığımız bir aşamada yaptık. orada herhangi bir pencere görünümü, simge ya da menü istemediğimizi belirtiyoruz aslında.  

**viewing partitions**
lsblk

**last statement on the SSH part**
"the student being evaluated should help you use SSH in order to log in with newly created user. To do this, you can use a key or a simple password. It will depend on the student being evaluated.
Of course, you have to make sure that you canoot use SSH with the "root" user as stated in the subject."

* **Yani yeni kullanıcının hesabıyla ssh bağlantısı kurmalı --> DENE**

* **monitoring.sh çalışmasını scripti değiştirmeden değiştirme**
sudo systemctl stop cron -> cron çalışmasını durdurur.
sudo systemctl restart cron -> yeniden başlatır.
