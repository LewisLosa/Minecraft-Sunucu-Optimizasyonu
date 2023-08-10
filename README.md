# Minecraft Sunucu Optimizasyon Rehberi

Not: Vanilla, Fabric veya Spigot (veya Paper'ın altında olan diğer sürümler) kullanan kullanıcılar - server.properties dosyanıza gidin ve `sync-chunk-writes` değerini `false` olarak değiştirin. Bu seçenek Paper ve türevlerinde zorunlu olarak `false` olarak ayarlanmıştır, ancak diğer sunucu uygulamalarında bunu manuel olarak `false` olarak değiştirmeniz gerekmektedir. Bu seçenek, sunucunun parçaları ana iş parçacığı dışında kaydetmesine izin verir ve bu da ana tik döngüsündeki yükü azaltır.

Versiyon 1.20 için rehber. Bazı şeyler hala 1.15 - 1.19'a uygulanabilir.

Bu rehber, [bu rehber](https://www.spigotmc.org/threads/guide-server-optimization%E2%9A%A1.283181/) ve diğer kaynaklara dayanmaktadır (ilgili olduğunda rehber boyunca bağlantılar verilmiştir).

Bu rehberde gezinmeyi kolaylaştırmak için yukarıdaki tabloyu (README.md yanındaki) kullanarak içerik listesini kullanabilirsiniz.

# Giriş
Mükemmel sonuçlar sunan bir rehber asla olmayacaktır. Her sunucunun kendi ihtiyaçları ve ne kadar ödün verilebileceği sınırları vardır. Seçeneklerle oynamak ve onları sunucunuzun ihtiyaçlarına göre ayarlamak, işin temelidir. Bu rehber, yalnızca performans üzerinde etkisi olan seçenekleri ve tam olarak neyi değiştirdiklerini anlamanıza yardımcı olmayı amaçlamaktadır. Bu rehberde yanlış bilgi bulduğunuzu düşünüyorsanız, düzeltmek için bir sorun açabilir veya bir çekme isteği yapabilirsiniz.

# Hazırlıklar

## Sunucu JAR
Sunucu yazılımınızın seçimi, performans ve API olasılıkları açısından büyük bir fark yaratabilir. Şu anda birden fazla popüler sunucu JAR'ı bulunmaktadır, ancak çeşitli nedenlerle kaçınılması gerekenler de mevcuttur.

Tavsiye edilen en iyi seçenekler:
* [Paper](https://github.com/PaperMC/Paper) - Oynanış ve mekanik tutarsızlıklarını düzeltmeyi amaçlayan en popüler sunucu yazılımıdır.
* [Pufferfish](https://github.com/pufferfish-gg/Pufferfish) - Sunucu performansını daha da iyileştirmeyi amaçlayan Paper türevidir.
* [Purpur](https://github.com/PurpurMC/Purpur) - Özelliklere ve özelleştirmeye özgürlüğe odaklanan Pufferfish türevidir.

Kaçınılması gerekenler:
* Herhangi bir ödeme yapmanız gereken async iddiasında bulunan sunucu JAR'ı - %99.99 dolandırıcılık olasılığı vardır.
* Bukkit/CraftBukkit/Spigot - Diğer sunucu yazılımlarına göre performans açısından son derece eski kalmıştır.
* Herhangi bir eklentiyi/programı çalışma zamanında etkinleştiren/devre dışı bırakan/yeniden yükleyen yazılım. Nedenini anlamak için [bu bölüme](#plugins-enablingdisabling-other-plugins) bakabilirsiniz.
* Pufferfish veya Purpur'dan daha aşağıda olan birçok dalga, kararsızlık ve diğer sorunlarla karşılaşabilir. Daha fazla performans kazanmaya çalışıyorsanız, sunucunuzu optimize edin veya kişisel bir özel dalga oluşturmak için yatırım yapın.

## Harita Önceden Oluşturma (Map Pregen)
Yıllar içinde parça üretimine eklenen çeşitli optimizasyonlar sayesinde, harita önceden oluşturma artık yalnızca korkunç, tek iş parçacıklı veya sınırlı CPU'ya sahip sunucularda faydalıdır. Ancak, pregenerasyon genellikle Pl3xMap veya Dynmap gibi dünya haritası eklentileri için parçaların oluşturulmasını sağlamak için kullanılır.

Hala dünyayı önceden oluşturmak isterseniz, bunu yapmak için [Chunky](https://github.com/pop4959/Chunky) gibi bir eklenti kullanabilirsiniz. Oyuncularınızın yeni parçalar oluşturmaması için bir dünya sınırı ayarladığınızdan emin olun! Unutmayın ki pregenerasyon bazen, pregen eklentisinde belirlediğiniz yarıçapa bağlı olarak saatler sürebilir. Paper ve üstü sürümlerle tps'niz parça yüklemesinden etkilenmeyecektir, ancak sunucunuzun işlemcisi aşırı yüklendiğinde parçaların yüklenme hızı önemli ölçüde yavaşlayabilir.

Ayrıca, overworld (ana dünya), nether ve the end (son) dünyalarının ayrı dünya sınırlarının her biri için ayar yapmanız gerektiğini unutmamanız önemlidir. Nether boyutu overworld boyutunun 8 katı daha küçüktür (veri paketi ile değiştirilmediyse), bu nedenle boyutu yanlış ayarlarsanız oyuncularınız dünya sınırının dışına çıkabilir!

**Vanilya dünya sınırını ayarladığınızdan emin olun (`/worldborder set [çap]`), çünkü bazı işlevlerin sınırlanmasına neden olan, gecikme nedeni olabilecek hazine haritaları için arama menzili gibi özellikleri kısıtlar.**

# Konfigürasyonlar

## Ağ Ayarları

### [server.properties]

#### network-compression-threshold

`İyi başlangıç değeri: 256`

Bu, sunucunun sıkıştırmayı denemeden önce bir paketin boyutunun üst sınırını belirlemenizi sağlar. Daha yüksek bir değer ayarlamak, bant genişliğini tasarruf edebilirken CPU kaynaklarını kurtarabilir, ancak -1 ayarlamak bunu devre dışı bırakır. Bunun daha yüksek ayarlanması, daha yavaş ağ bağlantılarına sahip istemcileri etkileyebilir. Sunucunuz bir proxy ağında veya aynı makinede (2 ms'den düşük gecikme ile) ise, bunu devre dışı bırakmak (-1) yararlı olacaktır, çünkü dahili ağ hızları genellikle ek sıkıştırılmamış trafiği işleyebilir.

### [purpur.yml]

#### use-alternate-keepalive

`İyi başlangıç değeri: true`

Purpur'un alternatif keepalive sistemini etkinleştirebilirsiniz, böylece kötü bağlantıya sahip oyuncular daha az sıklıkla zaman aşımına uğrarlar. TCPShield ile bilinen uyumsuzluğa sahiptir.

> Bu, bir oyuncuya saniyede bir kez bir keepalive paketi gönderir ve sadece 30 saniye boyunca hiçbiri yanıtlanmazsa zaman aşımına uğrar. Onlardan herhangi birine herhangi bir sırayla yanıt vermek, oyuncunun bağlantısının devam etmesini sağlar. Yani, bir paket düşerse oyuncularınızı atmaz  
~ https://purpurmc.org/docs/Configuration/#use-alternate-keepalive

---

## Parçalar (Chunks)

### [server.properties]

#### simulation-distance

`İyi başlangıç değeri: 4`

Simülasyon mesafesi, sunucunun işleyeceği oyuncunun etrafındaki parçaların mesafesidir. Temel olarak, oyuncudan uzaklıkta nelerin gerçekleşeceği mesafesidir. Bu, işlemci yükünü azaltmak için bilinçli olarak düşük bir değer olarak ayarlamak istediğiniz bir seçenektir, yaklaşık olarak `3` veya `4` civarında bir değer, `view-distance`'ın varlığı nedeniyle. Bu, parçaları işlemlemeden daha fazla parçanın yüklenmesine olanak tanır. Bu, oyuncuların aynı performans etkisi olmadan daha uzağı görmesine olanak tanır.

#### view-distance

`İyi başlangıç değeri: 7`

Bu, oyunculara gönderilecek parçaların (chunks) mesafesidir, Paper'ın no-tick-view-distance'ına benzer.

Toplam görüntü mesafesi, `simulation-distance` ve `view-distance` arasındaki en büyük değere eşit olacaktır. Örneğin, simülasyon mesafesi 4 olarak ayarlandıysa ve görüntü mesafesi 12 ise, istemciye gönderilen toplam mesafe 12 parça olacaktır.

### [spigot.yml]

#### view-distance

`İyi başlangıç değeri: default`

Bu değer, `default` olarak ayarlanmadıysa server.properties'yi üzerine yazar. Daha kolay yönetim için simülasyon ve görüntü mesafesini aynı yerde tutmak için varsayılanı kullanmalısınız.

### [paper-world yapılandırması]

#### delay-chunk-unloads-by

`İyi başlangıç değeri: 10s`

Bu seçenek, oyuncu ayrıldıktan sonra parçaların ne kadar süreyle yüklenmiş kalacağını yapılandırmanıza olanak tanır. Bir oyuncu ileri geri hareket ettiğinde aynı parçaları sürekli yükleme ve boşaltma konusunda yardımcı olur. Çok yüksek değerler çok fazla parçanın aynı anda yüklenmesine neden olabilir. Sık sık teleport edilen ve yüklenen bölgelerde, alanın sürekli yüklü kalmasını sağlamayı düşünün. Bu, oyuncuların sürekli olarak parça yükleme ve boşaltma yerine, daha hafif bir yüke neden olur.

#### max-auto-save-chunks-per-tick

`İyi başlangıç değeri: 8`

Daha iyi ortalama performans için artık artık dünyayı yavaşça kaydetmeye yayarak inkremental dünya kaydetmeyi yavaşlatmanızı sağlar. 20-30 oyuncudan fazlasıyla daha yüksek bir değer ayarlamak isteyebilirsiniz. İnkremental kaydetme belirli bir sürede tamamlanamazsa, bu atıkları otomatik olarak kaydeder ve işlemi yeniden başlatır.

#### prevent-moving-into-unloaded-chunks

`İyi başlangıç değeri: true`

Etkinleştirildiğinde, oyuncuların yüklenmemiş parçalara geçmesini ve ana iş parçacığını yavaşlatan senkron yüklemeleri neden olmasını engeller. Görüntü mesafeniz ne kadar düşükse, bir oyuncunun yüklenmemiş bir parçaya rasgele düşme olasılığı o kadar yüksektir.

#### entity-per-chunk-save-limit

```
İyi başlangıç değerleri:

    area_effect_cloud: 8
    arrow: 16
    dragon_fireball: 3
    egg: 8
    ender_pearl: 8
    experience_bottle: 3
    experience_orb: 16
    eye_of_ender: 8
    fireball: 8
    firework_rocket: 8
    llama_spit: 3
    potion: 8
    shulker_bullet: 8
    small_fireball: 8
    snowball: 8
    spectral_arrow: 16
    trident: 16
    wither_skull: 4
```

Bu girişin yardımıyla belirtilen türdeki varlıkların kaçının kaydedilebileceğini sınırlandırabilirsiniz. Herhangi bir ölçekteki projektileri sınırlamak için en az bir sınır belirtmelisiniz, aksi takdirde çok sayıda projektilin kaydedilmesi ve sunucunuzun yüklenirken çökmesi sorunları ortaya çıkabilir. Burada herhangi bir varlık kimliği (ID) girebilirsiniz, varlık kimliklerini bulmak için Minecraft wiki'ye bakabilirsiniz. Lütfen sınırı isteğinize göre ayarlayın. Tüm projektiler için önerilen değer yaklaşık olarak `10`'dur. Ayrıca, bu listeye tür adlarını ekleyerek diğer varlıkları da ekleyebilirsiniz. Bu yapılandırma seçeneği, oyuncuların büyük canavar çiftlikleri oluşturmalarını engellemek için tasarlanmamıştır.

### [pufferfish.yml]

#### max-loads-per-projectile

`İyi başlangıç değeri: 8`

Bir projektilin ömrü boyunca yükleyebileceği maksimum parça sayısını belirtir. Daha düşük bir değere ayarlamak, varlık projektileri nedeniyle yüklenen parça yükünü azaltır, ancak tridentler, ender incileri vb. ile sorunlara neden olabilir.

---

## Yaratıklar (Mobs)

### [bukkit.yml]

#### spawn-limits

```
İyi başlangıç değerleri:

    monsters: 20
    animals: 5
    water-animals: 2
    water-ambient: 2
    water-underground-creature: 3
    axolotls: 3
    ambient: 1
```

Yaratıkları sınırlamanın matematiği `[oyuncusayısı] * [limit]`, burada "oyuncusayısı" sunucudaki mevcut oyuncu sayısıdır. Mantıksal olarak, sayılar ne kadar küçükse, göreceğiniz yaratıklar o kadar az olur. `per-player-mob-spawn` bunun üzerine ek bir sınırlama getirir ve yaratıkların oyuncular arasında eşit şekilde dağıtılmasını sağlar. Bu, iş yükünüzün daha düşük olması anlamına gelir, ancak bazı oyun modları için doğal yolla oluşan yaratıklar oyunun büyük bir bölümünü oluşturur. Mob sınırlarını ayarlamak için `20` veya daha düşük bir değere gidebilirsiniz, eğer `mob-spawn-range`'i uygun şekilde ayarlarsanız. `mob-spawn-range` değerini düşürmek, her oyuncu etrafında daha fazla yaratık olduğu hissini verir. Paper kullanıyorsanız, [paper-world yapılandırması] ile dünya başına mob sınırları belirleyebilirsiniz.

#### ticks-per

```
İyi başlangıç değerleri:

    monster-spawns: 10
    animal-spawns: 400
    water-spawns: 400
    water-ambient-spawns: 400
    water-underground-creature-spawns: 400
    axolotl-spawns: 400
    ambient-spawns: 400
```

Bu seçenek, belirli canlı varlıkların ne sıklıkla (tick cinsinden) doğmaya çalışılacağını belirler. Su/çevresel varlıklar genellikle çok hızlı öldürülmediği için her tick'te doğmaları gerekmez. Canavarlar için: Doğmalar arasındaki süreyi hafifçe artırmak, hatta canavar çiftliklerinde bile doğum oranlarını etkilememelidir. Bu seçenek altındaki tüm değerlerin genellikle `1` değerinden yüksek olması gerekmektedir. Bu değeri yükseltmek aynı zamanda, yaratık doğmasının devre dışı bırakıldığı bölgelerle daha iyi başa çıkmanıza olanak sağlar.
### [spigot.yml]

#### mob-spawn-range

`İyi başlangıç değeri: 3`

Yaratıkların oyuncunun etrafında hangi parçalarda doğacaklarının mesafesini (parça cinsinden) azaltmanıza olanak tanır. Sunucunuzun oyun moduna ve oyuncu sayısına bağlı olarak, bu değeri [bukkit.yml]'nin `spawn-limits` değeri ile birlikte düşürebilirsiniz. Bu değeri düşürmek, etrafınızda daha fazla yaratık olduğu hissini verir. Bu, simülasyon mesafenizden daha düşük veya buna eşit olmalıdır ve asla sert kaybolma mesafenizden büyük olmamalıdır / 16.

#### entity-activation-range

```
İyi başlangıç değerleri:

      animals: 16
      monsters: 24
      raiders: 48
      misc: 8
      water: 8
      villagers: 16
      flying-monsters: 48
```

Bir varlığın oyuncudan kaç blok uzaklıkta tikleneceğini (işlemlerini yapacağını) belirlemenize olanak tanır. Bu değerleri azaltmak performansı artırır, ancak oyuncunun çok yakınına gelene kadar yaratıkların tepki vermemesine neden olabilir. Bu değeri çok fazla düşürmek, belirli yaratık çiftliklerini bozabilir; en yaygın kurbanları demir çiftlikleri.

#### entity-tracking-range

```
İyi başlangıç değerleri:

      players: 48
      animals: 48
      monsters: 48
      misc: 32
      other: 64
```

Bu, varlıkların ne kadar blok uzaklıktan görüneceğini belirler. Sadece oyunculara gönderilmeyecekler. Bu değeri çok düşük ayarlarsanız, yaratıkların bir oyuncunun yakınında aniden ortaya çıkıyormuş gibi görünmesine neden olabilir. Çoğu durumda, bu değer, `entity-activation-range`'den daha yüksek olmalıdır.

#### tick-inactive-villagers

`İyi başlangıç değeri: false`

Bu, köylülerin etkinleştirme mesafesi dışında da tiklenip tiklenmeyeceğini kontrol etmenizi sağlar. Bu, köylülerin normal olarak devam etmelerini ve etkinleştirme mesafesini görmezden gelmelerini sağlar. Bu, performansa yardımcı olur, ancak belirli durumlarda oyuncular için kafa karıştırıcı olabilir. Demir çiftlikleri ve ticaret yeniden doldurulmasıyla ilgili sorunlara neden olabilir.

#### nerf-spawner-mobs

`İyi başlangıç değeri: true`

Bir canavar üretecinin doğurduğu yaratıkların AI'sını kaldırtabilirsiniz. Zayıflatılmış yaratıklar hiçbir şey yapmaz. [paper-world yapılandırması] içindeki `spawner-nerfed-mobs-should-jump`'ı `true` olarak değiştirerek suda zıplamalarını sağlayabilirsiniz.

### [paper-world yapılandırması]

#### despawn-ranges

```
İyi başlangıç değerleri:

      ambient:
        hard: 56
        soft: 30
      axolotls:
        hard: 56
        soft: 30
      creature:
        hard: 56
        soft: 30
      misc:
        hard: 56
        soft: 30
      monster:
        hard: 56
        soft: 30
      underground_water_creature:
        hard: 56
        soft: 30
      water_ambient:
        hard: 56
        soft: 30
      water_creature:
        hard: 56
        soft: 30
```

Varlıkların kaybolma mesafelerini (blok cinsinden) ayarlamanıza olanak tanır. Bu değerleri düşürerek oyuncudan uzakta olan yaratıkları daha hızlı temizleyebilirsiniz. Yumuşak aralığı yaklaşık `30` olarak tutmalı ve sert aralığı gerçek simülasyon mesafenizin biraz üzerine ayarlamalısınız, böylece oyuncu bir parçanın yüklenmiş olduğu noktanın hemen ötesine geçtiğinde yaratıklar hemen kaybolmazlar (bu, [paper-world yapılandırması] içindeki `delay-chunk-unloads-by` sayesinde iyi çalışır). Bir yaratık sert aralığın dışındaysa, anında kaybolur. Yumuşak ve sert aralık arasında olan bir yaratığın kaybolma olasılığı rastgele olur. Sert aralığınız yumuşak aralığınızdan

 daha büyük olmalıdır. Bu değeri görüş mesafenize göre ayarlamanız gerekmektedir `(simulation-distance * 16) + 8`. Bu, oyuncu ziyaret ettikten sonra henüz yüklenmemiş olan parçaların kısmen hesaba katılmasını sağlar.

#### per-player-mob-spawns

`İyi başlangıç değeri: true`

Bu seçenek, yaratık doğmalarının hedef oyuncunun etrafındaki yaratıkların sayısını hesaba katıp katmayacağını belirler. Tüm yaratıkların üretiminin tutarlı olmadığı sorunları aşmanızı sağlar. Bu, daha tek oyunculu bir doğma deneyimi sağlar ve daha düşük `spawn-limits` ayarlamaları yapmanıza olanak tanır. Bu seçeneği etkinleştirmek, çok hafif bir performans etkisi getirir; ancak, bunun performans üzerindeki etkisi, izin verdiği `spawn-limits` iyileştirmeleri tarafından gölgede bırakılır.

#### max-entity-collisions

`İyi başlangıç değeri: 2`

[spigot.yml] içinde aynı isme sahip seçeneği geçersiz kılar. Bir varlığın aynı anda kaç çarpışması işleyebileceğini belirlemenizi sağlar. Değeri `0` olan varlık, diğer varlıkları, oyuncular dahil, itme yeteneğini kaybeder. Değeri `2`, çoğu durumda yeterli olmalıdır. Bu, maxEntityCramming oyun kuralını bu yapılandırma seçeneğinin değerinden yüksekse işlevsiz hale getirir.

#### update-pathfinding-on-block-update

`İyi başlangıç değeri: false`

Bu seçeneği devre dışı bırakmak, daha az yol aramasının yapılmasına neden olur ve performansı artırır. Bazı durumlarda bu, yaratıkların daha fazla gecikmeli görünmesine neden olacaktır; Sadece pasif olarak yolu her 5 tickte bir (0.25 saniye) güncellerler.

#### fix-climbing-bypassing-cramming-rule

`İyi başlangıç değeri: true`

Bu, yaratıkların tırmanırken boğulma kurallarından etkilenmemesini sağlar. Bu, tırmanan yaratıkların bile küçük alanlarda aşırı miktarda yığılmalarını önler (örümcekler).

#### armor-stands.tick

`İyi başlangıç değeri: false`

Çoğu durumda bunu güvenli bir şekilde `false` olarak ayarlayabilirsiniz. Zırh duraklarını veya davranışlarını değiştiren herhangi bir eklenti kullanıyorsanız ve sorun yaşıyorsanız, yeniden etkinleştirebilirsiniz. Bu, su tarafından itilmelerini veya yerçekiminden etkilenmelerini önler.

#### armor-stands.do-collision-entity-lookups

`İyi başlangıç değeri: false`

Burada zırh duraklarının çarpışmalarını devre dışı bırakabilirsiniz. Bu, çok sayıda zırh duraklarınız varsa ve bunların herhangi bir şeyle çarpışmamasını istiyorsanız yardımcı olur.

#### tick-rates

```
İyi başlangıç değerleri:

  behavior:
    villager:
      validatenearbypoi: 60
      acquirepoi: 120
  sensor:
    villager:
      secondarypoisensor: 80
      nearestbedsensor: 80
      villagerbabiessensor: 40
      playersensor: 40
      nearestlivingentitysensor: 40
```

> [Pufferfish'in DAB](#dabenabled) etkinleştirildiğinde bu değerlerin varsayılan değerlerini değiştirmeniz önerilmez!

Bu, belirli davranışların ve sensörlerin kaç tickte bir ateşlendiğini belirler. Köylüler için `acquirepoi` en yoğun davranış gibi görünüyor, bu nedenle büyük ölçüde artırıldı. Köylülerin yollarını bulmada sorun yaşanması durumunda bunu azaltın.

### [pufferfish.yml]

#### dab.enabled

`İyi başlangıç değeri: true`

DAB (dinamik beyin etkinleştirmesi), bir varlığın oyunculardan ne kadar uzak olduğuna bağlı olarak tiklenme miktarını azaltır. DAB, sert bir kesim yerine bir gradyan üzerinde çalışır. Yakındaki varlıkları tamamen tiklemek ve uzaktaki varlıkları neredeyse tiklememek yerine, DAB, [dab.activation-dist-mod](#dabactivation-dist-mod) tarafından etkilenen bir hesaplamanın sonucuna dayalı olarak bir varlığın tiklenme miktarını azaltır.

#### dab.max-tick-freq

`İyi başlangıç değeri: 20`

En uzak oyunculardan en uzak varlıkların ne sıklıkla tikleneceğini tanımlar. Bu değeri artırmak, görüşten uzak varlıkların performansını artırabilir, ancak çiftlikleri bozabilir veya yaratık davranışını büyük ölçüde zayıflatabilir. DAB'ı etkinleştirmek çiftlikleri bozarsa, bu değeri azaltmayı deneyin.

#### dab.activation-dist-mod

`İyi başlangıç değeri: 7`

Varlıkların tiklenmesi için gradyanı kontrol eder. Bu değeri azaltmak, DAB'ı oyunculara daha yakın bir şekilde etkinleştirir, DAB'ın performans kazançlarını artırır, ancak varlıkların çevreleriyle nasıl etkileşimde bulunduğunu etkiler ve çiftlikleri bozabilir. DAB, çiftlikleri bozars

a, bu değeri artırmayı deneyin.

#### enable-async-mob-spawning

`İyi başlangıç değeri: true`

Asenkron yaratık doğumlarının etkinleştirilip etkinleştirilmeyeceğini belirler. Bu çalışması için Paper'ın per-player-mob-spawns ayarının etkinleştirilmesi gereklidir. Bu seçenek, yaratıkların asenkron olarak doğmasını sağlamaz, ancak yeni yaratıkların doğmasıyla ilgili hesaplama çabasının büyük bir kısmını farklı bir iş parçacığına aktarır. Bu seçeneği etkinleştirmek, genellikle vanilla oynanışında fark edilmeyecektir.

#### enable-suffocation-optimization

`İyi başlangıç değeri: true`

Bu seçenek, bir varlığın bir blok içinde olup olmadığını ve boğulma hasarı alması gerekip gerekmediğini kontrol eden bir boğulma kontrolünü (suffocation check) optimize eder. Bu optimizasyon, kontrolü hasar zaman aşımına göre sınırlayarak yapılır. Bu optimizasyon, varlıkların bir bloğun içinde olup olmadığını hızlıca kontrol ederken daha az işlem yapar. Bu optimizasyon, yaratıkların bir bloğun içinde olup olmadığını hızlıca kontrol ederken daha az işlem yapar.

#### inactive-goal-selector-throttle

`İyi başlangıç değeri: true`

Varlık etkisiz durum tıklama sıralayıcısını (goal selector) varlık etkisiz ticklerinde yavaşlatır, böylece etkisiz varlıkların sıralayıcısını her 20 tickte bir değil, her tickte bir güncellerler. Bu, birkaç yüzde performansı iyileştirebilir ve hafif oyun oynamaya etki eder.

### [purpur.yml]

#### zombie.aggressive-towards-villager-when-lagging

`İyi başlangıç değeri: false`

Bu etkinleştirildiğinde, zombilerin sunucu `lagging-threshold` değeri ile ayarlanan tps eşiği altındaysa köylülere saldırmayı bırakmasına neden olur.

#### entities-can-use-portals

`İyi başlangıç değeri: false`

Bu seçenek, oyuncu dışındaki tüm varlıkların portal kullanımını devre dışı bırakabilir. Bu, varlıkların blokları değiştirerek dünyalar arasında geçiş yapmasını engeller. Bu ayrıca varlıkların portal kullanarak geçmesini önler.

#### villager.lobotomize.enabled

`İyi başlangıç değeri: true`

> Sadece köylüler gecikmeye neden oluyorsa etkinleştirilmelidir! Aksi takdirde, yol araması kontrolleri performansı azaltabilir.

Lobotomize edilmiş köylüler, yalnızca tekliflerini belirli aralıklarla yeniler ve AI'larını çıkarılmış köylülerdir. Bu, yollarını bulamayan köylüleri lobotomize edecektir. Onları serbest bırakmak onları lobotomize edecektir.

[paper-world Yapılandırması]
alt-item-despawn-rate
yaml
Copy code
İyi başlangıç ​​değerleri:

      enabled: true
      items:
        cobblestone: 300
        netherrack: 300
        sand: 300
        red_sand: 300
        gravel: 300
        dirt: 300
        grass: 300
        pumpkin: 300
        melon_slice: 300
        kelp: 300
        bamboo: 300
        sugar_cane: 300
        twisting_vines: 300
        weeping_vines: 300
        oak_leaves: 300
        spruce_leaves: 300
        birch_leaves: 300
        jungle_leaves: 300
        acacia_leaves: 300
        dark_oak_leaves: 300
        mangrove_leaves: 300
        cactus: 300
        diorite: 300
        granite: 300
        andesite: 300
        scaffolding: 600
Bu liste, belirli türdeki düşen öğelerin varsayılan hızdan daha hızlı veya daha yavaş bir şekilde kaybolmasını ayarlamanıza olanak tanır. Bu seçenek, performansı artırmak için öğe temizleme eklentileri yerine kullanılabilir.

redstone-implementation
`İyi başlangıç ​​değeri: ALTERNATE_CURRENT`

Kırmızı taş sistemini daha hızlı ve alternatif sürümlerle değiştirir, gereksiz blok güncellemelerini azaltarak sunucunuzun hesaplaması gereken mantık miktarını azaltır. Alternatif bir uygulama kullanmak, çok teknik kırmızı taş ile küçük tutarsızlıklara neden olabilir, ancak performans kazançları olası dar alandaki sorunların ötesindedir. ALTERNATE_CURRENT uygulaması Alternate Current moduna dayanır. Bu algoritma hakkında daha fazla bilgiye kaynak sayfalarında ulaşabilirsiniz.

hopper.disable-move-event
İyi başlangıç ​​değeri: false

InventoryMoveItemEvent, bu olayı dinlemek için etkin bir eklentinin olmadığı durumda ateşlenmez. Bu, bu olaya tepki veren eklentilerinizin olması durumunda sadece bu değeri true olarak ayarlamanız gerektiğini gösterir. Bu olayı dinleyen eklentileri kullanmak istiyorsanız true olarak ayarlamayın, örneğin koruma eklentileri gibi!

hopper.ignore-occluding-blocks
`İyi başlangıç ​​değeri: true`

Hoperların dolu blokları yoksayıp yoksaymayacağını belirler, örneğin kum veya çakıl bloğunun içindeki hoper madeni arabası. Bu seçenek etkin tutulursa, bazı mekanizmaların bu davranışa bağlı olduğu düzeltilebilir.

tick-rates.mob-spawner
`İyi başlangıç ​​değeri: 2`

Bu seçenek, üreteçlerin ne sıklıkla işleneceğini yapılandırmanıza olanak tanır. Daha yüksek değerler, birçok üreteç varsa daha az gecikme sağlayabilir, ancak çok yüksek değerler (üreteç gecikmesine göre yüksek) mob spawn oranlarını düşürebilir.

optimize-explosions
`İyi başlangıç ​​değeri: true`

Bu seçeneği true olarak ayarlarsanız, patlama algoritmasını daha hızlı bir sürümle değiştirir, ancak patlama zararı hesaplamalarında hafif bir sapma olabilir. Bu genellikle fark edilmez.

treasure-maps.enabled
`İyi başlangıç ​​değeri: false`

Define haritası üretimi son derece maliyetlidir ve hedeflediği yapının oluşturulmamış bir yongağa sahip olduğu durumda sunucunuzu askıya alabilir. Bu seçeneği yalnızca dünyanızı önceden oluşturduysanız ve vanilla dünya sınırını belirlediyseniz etkinleştirmek güvenlidir.

treasure-maps.find-already-discovered
`İyi başlangıç ​​değerleri:
      loot-tables: true
      villager-trade: true`
Bu seçeneğin varsayılan değeri, yeni oluşturulan haritaların genellikle henüz oluşturulmamış yongağlarda bulunan keşfedilmemiş yapılara yönlendirmesini sağlar. Bu seçeneği true olarak ayarlarsanız, haritalar daha önce keşfedilen yapılara yönlendirebilir. Eğer bu değeri true olarak ayarlamazsanız, yeni define haritaları oluşturulurken sunucunun askıya alınması veya çökmesi gibi sorunlarla karşılaşabilirsiniz. villager-trade, köylüler tarafından satılan haritalar için, loot-tables ise define sandıkları, mağara sandıkları vb. gibi dinamik olarak ganimet oluşturan her şeyi ifade eder.

tick-rates.grass-spread
`İyi başlangıç ​​değeri: 4`

Çimen veya mantarın yayılma hızını ayarlar. Bu, büyük alanlardaki toprağın çim veya mantara dönüşmesinin biraz daha uzun sürmesini sağlar. Bu değeri yaklaşık olarak 4 olarak ayarlarsanız, azalmış yayılma hızı farkedilir olmadan çalışabilir.

tick-rates.container-update
`İyi başlangıç ​​değeri: 1`

Konteyner güncellemeleri arasındaki süreyi ayarlar. Bu değeri artırmak, nadiren gerçekleşse de konteyner güncellemelerinin sorunlara neden olmasına yardımcı olabilir, ancak oyuncuların envanterlerle etkileşimlerinde desenkronizasyon yaşama olasılığını artırır (hayalet öğeler).

non-player-arrow-despawn-rate
`İyi başlangıç ​​değeri: 20`

Canavarlar tarafından atılan okların bir şeye çarptıktan sonra kaybolması gereken süreyi belirtir. Oyuncular bu okları zaten alamaz, bu nedenle bu değeri 20 gibi bir değere ayarlayabilirsiniz (1 saniye).

creative-arrow-despawn-rate
`İyi başlangıç ​​değeri: 20`

Yaratıcı moddaki oyuncular tarafından atılan okların bir şeye çarptıktan sonra kaybolması gereken süreyi belirtir. Oyuncular bu okları zaten alamaz, bu nedenle bu değeri 20 gibi bir değere ayarlayabilirsiniz (1 saniye).

[pufferfish.yml]
disable-method-profiler
`İyi başlangıç ​​değeri: true`

Bu seçenek, oyundaki ek profillemeyi devre dışı bırakır. Bu profillemenin üretimde çalıştırılması gereksizdir ve ek gecikmelere neden olabilir.

[purpur.yml]
dolphin.disable-treasure-searching
`İyi başlangıç ​​değeri: true`

Yunusların define haritasına benzer şekilde yapı aramasını engeller.

teleport-if-outside-border
`İyi başlangıç ​​değeri: true`

Oyuncunun dünya sınırının dışında olması durumunda onları dünya ortasına teleport etmenize olanak tanır. Vanilla dünya sınırı atlama işlemleri gerçekleştirilebilir ve bu işlemin oyuncuya verdiği hasar azaltılabilir.

Yardımcılar
[paper-world Yapılandırması]
anti-xray.enabled
İyi başlangıç ​​değeri: true

X-raycılardan cevherleri gizlemek için bu seçeneği etkinleştirin. Bu özelliğin ayrıntılı yapılandırması için Anti-Xray'ı Yapılandırma belgesine bakın. Bu, genellikle performansı azaltır, ancak çoğu durumda performans etkisi ihmal edilebilir.

nether-ceiling-void-damage-height
`İyi başlangıç ​​değeri: 127`

Bu seçenek 0'dan büyükse, belirtilen y seviyesinin üstündeki oyuncular sanki boşlukta gibi zarar görür. Bu oyuncuların nether tavanını kullanmalarını engeller. Vanilla nether 128 blok yüksekliğindedir, bu nedenle muhtemelen 127 olarak ayarlamalısınız. Eğer nether yüksekliğini herhangi bir şekilde değiştirirseniz, bunu [sizin_nether_yüksekliği] - 1 olarak ayarlamanız gerekebilir.

Java başlatma bayrakları
Vanilla Minecraft ve Minecraft sunucu yazılımı 1.19 sürümünde Java 17 veya daha yükseğini gerektirir. Oracle lisansını değiştirdi ve artık java'yı onlardan almak için ikna edici bir neden yok. Tavsiye edilen tedarikçiler Adoptium ve Amazon Corretto. OpenJ9 veya GraalVM gibi alternatif JVM uygulamaları çalışabilir, ancak Paper tarafından desteklenmez ve sorunlara neden olma olasılıkları olduğundan şu anda önerilmez.

Çöp toplayıcınız, büyük çöp toplayıcı görevlerinden kaynaklanan gecikme sıkıntılarını azaltmak için yapılandırılabilir. Minecraft sunucuları için optimize edilmiş başlatma bayraklarını burada bulabilirsiniz. Bununla birlikte, bu öneri alternatif JVM uygulamalarında çalışmayabilir. Doğru başlatma bayraklarını almak için flags.sh başlatma bayrakları oluşturucu aracını kullanmanız önerilir.

Ayrıca, başlatma bayrağındaki -jar öncesine beta bayrağı --add-modules=jdk.incubator.vector eklemek, performansı artırabilir. Bu bayrak, Pufferfish'in CPU'nuzdaki SIMD talimatlarını kullanmasını sağlar, böylece bazı matematiksel işlemleri daha hızlı yapabilir. Şu anda yalnızca oyun içi eklenti haritalarının (örneğin imageonmaps gibi) renderini oluşturmak için kullanılır ve bu sayede olası 8 kat daha hızlı hale getirilir.

"Gerçek olamayacak kadar iyi" eklentiler
Yerdeki öğeleri kaldıran eklentiler
Kesinlikle gereksizdirler çünkü merge-radius ve alt-item-despawn-rate ile yerine konulabilirler ve temel sunucu yapılandırmalarından daha az yapılandırılabilirler. Genellikle öğeleri tarayan ve kaldıran eklentiler, öğeleri kaldırmamaktan daha fazla kaynak kullanır.

Canavar yığınlayıcı eklentiler
Kullanmaları gerçekten zordur. Doğal olarak üretilmiş varlıkları yığınlamak, sürekli olarak daha fazla canavar üretmeye çalışan sunucunun daha fazla gecikme yaşamasına neden olur. Tek kabul edilebilir kullanım durumu, büyük miktarda üreticiye sahip sunucularda üretilen canavar yığınlarıdır.

Diğer eklentileri etkinleştiren/devre dışı bırakan eklentiler
Eklentileri etkinleştiren veya devre dışı bırakan herhangi bir şey son derece tehlikelidir. Bu şekilde bir eklenti yüklemek, izleme verileriyle ilgili ölümcül hatalara neden olabilir ve bir eklentiyi devre dışı bırakmak, bağımlılıkları kaldırdığı için hatalara neden olabilir. /reload komutu da tam olarak aynı sorunlara neden olur ve bu sorunlar hakkında daha fazla bilgi için me4502'nin blog gönderisini okuyabilirsiniz.

Neden gecikiyor? - performans ölçümü
mspt
Paper, son zamanlarda işlenen tikleri hesaplamak için kullanılan zamanı size söyleyen /mspt komutunu sunar. İlk ve ikinci değer 50'nin altındaysa, tebrikler! Sunucunuz gecikmiyor demektir! Üçüncü değer 50'nin üzerindeyse, en az bir tikin daha uzun sürdüğü anlamına gelir. Bu tamamen normaldir ve zaman zaman meydana gelir, endişelenmeyin.

Spark
Spark, sunucunuzun CPU ve bellek kullanımını profillemenizi sağlayan bir eklentidir. Nasıl kullanılacağı hakkında bilgi almak için wiki sayfasına bakabilirsiniz. Ayrıca, gecikme artışının nedenini bulmaya yardımcı olacak bir kılavuz da burada bulunmaktadır.

Timings
Sunucunuz geciktiğinde neyin olabileceğini görmek için Timings kullanılabilir. Timings, en uzun süren görevleri tam olarak gösteren bir araçtır. Bu, en temel sorun giderme aracıdır ve gecikme hakkında yardım isterseniz muhtemelen Timings'e ihtiyacınız olacaktır. Timings'in sunucular üzerinde ciddi bir performans etkisi vardır, bu nedenle Spark eklentisini Timings yerine kullanmanız ve Timings'i tamamen devre dışı bırakmak için Purpur veya Pufferfish kullanmanız önerilir.

Sunucunuzun Timings'ini almak için sadece /timings paste komutunu çalıştırmanız ve size sağlanan bağlantıya tıklamanız gerekir. Bu bağlantıyı başkalarıyla paylaşarak size yardımcı olmalarını sağlayabilirsiniz. Ayrıca, ne yaptığınızı bilmiyorsanız yanlış okuma ihtimali vardır. Timings'i nasıl okuyacağınıza dair detaylı bir Aikar'ın video öğreticisi bulunmaktadır.

[`SOG`]: https://www.spigotmc.org/threads/guide-server-optimization%E2%9A%A1.283181/
[server.properties]: https://minecraft.fandom.com/wiki/Server.properties
[bukkit.yml]: https://bukkit.fandom.com/wiki/Bukkit.yml
[spigot.yml]: https://www.spigotmc.org/wiki/spigot-configuration/
[paper-global configuration]: https://docs.papermc.io/paper/reference/global-configuration
[paper-world configuration]: https://docs.papermc.io/paper/reference/world-configuration
[purpur.yml]: https://purpurmc.org/docs/Configuration/
[pufferfish.yml]: https://docs.pufferfish.host/setup/pufferfish-fork-configuration/
[Petal]: https://github.com/Bloom-host/Petal
