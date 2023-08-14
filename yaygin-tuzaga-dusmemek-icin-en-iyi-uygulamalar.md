# Yaygın Tuzağa Düşmemek İçin En İyi Uygulamalar

Bu makale, sunucu sahiplerinin karşılaştığı yaygın tuzağa düşmelere açıklık getirmeyi amaçlamaktadır.

## Her Zaman Yedek Alın
İki tür insan vardır - yedek alanlar ve yedek almaya başlayacak olanlar. Veri kaybı yaşamanızın sadece bir zaman meselesi olduğunu unutmayın. Dünyalarınızı veya eklenti verilerinizi kaybetmemek için her zaman kopyalarını alın. Bu sadece Minecraft değil, herhangi bir bilgisayarla ilgili iş akışına uygulayabileceğiniz bir ilkedir.

## Güncel Olmayan Yazılım Kullanmayın
Güncel olmayan yazılım sürümlerini çalıştırarak, oyuncuların yama alınmamış açıkları istismar etme riski altında olursunuz; buna madde çoğaltma (sonsuz madde) dahildir. Ayrıca oyuncularınızın sunucunuzla eşleşmesi için özellikle istemci sürümünü düşürmeleri gerekeceğinden, bu bir rahatsızlık faktörü de ekler. Bu, bir protokol hilesi kullanılarak atlatılabilir, ancak ideal bir çözüm değildir.

## Artık Bukkit/Spigot Kullanmayın
Bukkit ve Spigot temel olarak bakım modundadır. Yeni bir sürüm geldiğinde ve kritik bir açık bulunduğunda güncellenirler, ancak performans güncellemeleri eklenmez. Bu, bu yazılımlarda yaşayabileceğiniz herhangi bir performans sorununun zaman içinde geliştirilmeyeceği anlamına gelir. Bu sorunları önlemek için [Paper](https://papermc.io/downloads) veya [Purpur](https://purpurmc.org/downloads) gibi alternatiflere geçiş yapın. Bukkit/Spigot eklentileri, listelenen sunucu yazılımlarıyla aynı şekilde çalışacaktır (belki de daha iyi). Eğer çalışmazlarsa, eklenti geliştiricisinin ya yapmaması gereken şeyleri yaptığını ya da eklentisini oluştururken ihmalkar davrandığını varsaymak güvenlidir. Ayrıca, birden fazla işlemci çekirdeğinden faydalanabilen bir yükleme sistemi gibi iyileştirme yama eklerler veya sunucunun oyuncuya gönderdiğinden daha az yığın attığı bir ayar gibi optimizasyon yamaları eklerler. Daha fazla ayrıntı için [ana optimizasyon rehberine](https://github.com/LewisLosa/Minecraft-Sunucu-Optimizasyonu) bakın.

## Mümkünse Paylaşımlı Barındırmadan Kaçının
Paylaşımlı barındırmalar genellikle en ucuz seçenektir ve bunun geçerli bir nedeni vardır. Size iki tür kaynak sunarlar - garanti edilen ve paylaşılan kaynaklar. Garanti edilen kaynaklar genellikle güldürecek kadar düşüktür ve birkaç oyuncu için sunucu çalıştırmak için yeterli olmayabilir. Öte yandan paylaşılan kaynaklar genellikle makul performansla sunucu çalıştırmak için yeterlidir. Ancak bir tuzağı vardır; paylaşılan kaynaklar, adından da anlaşılacağı gibi, fiziksel olarak aynı makinedeki diğer sunucularla paylaşılır. Sunucunuz bu kaynaklardan yararlanabilir, ancak başka bir sunucu bunları kullanmadığında. Paylaşılan kaynakların tamamen kullanıldığı durum oldukça nadirdir, çünkü çoğu paylaşımlı barındırma sağlayıcısı kaynaklarını aşırı satar. Uçak bileti gibi, barındırma sitesi kullanılabilir kaynaklardan daha fazlasını satar, umut eder ki hepsi kullanılmayacaktır. Bu genellikle tüm sunucuların kaynakların yetersiz olduğu için yavaşladığı durumlara yol açar.

## Komut fonksiyonları kullanan veri paketlerinden kaçının
Komut çalıştıran veri paketleri son derece yavaştır. Birkaç oyuncuyla pek fazla olmayabilir, ancak bu oyuncu sayısıyla ölçeklendirilemez ve oyuncu sayınız arttıkça sunucunuzu hızlı bir şekilde yavaşlatabilir. Biyomları, yağma tablolarını değiştiren veri paketleri iyidir. Bir eklenti alternatifi aramak daha iyi bir seçenektir.

## Donanım Seçimi
Sadece ne kadar RAM'e ihtiyacınız olduğuna bakmayın. Bunun yerine, hangi türde bir CPU kullanmanız gerektiğine odaklanmalısınız, çünkü CPU sunucunun en önemli parçasıdır. Özellikle sunucu tek bir işlemci çekirdeğinde çalıştığı için [tek çekirdek performansında iyi](https://www.cpubenchmark.net/singleThread.html) bir şey istersiniz. Ancak Paper gibi sistemlerde asenkron yığın yükleme gibi çoklu iş parçacığı uzun süredir kullanılmaktadır.

Kesinlikle Hard Diskleri (HDD'ler) kullanmaktan kaçının. Hızları, minecraft'in giriş/çıkış işlemlerinde (özellikle yüksek görüş mesafeleri ve yüksek oyuncu sayılarıyla) çok yavaştır, bu nedenle bir sunucuyu üzerlerinde çalıştırmayı haklı çıkarmak için. Katı Hal Sürücüsü (SSD), çok daha hızlı giriş/çıkış hızına sa

hip olduğu için çok daha iyi bir seçenektir.
