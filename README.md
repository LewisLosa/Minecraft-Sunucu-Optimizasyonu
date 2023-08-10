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

`Good starting value: 3`

Allows you to reduce the range (in chunks) of where mobs will spawn around the player. Depending on your server's gamemode and its playercount you might want to reduce this value along with [bukkit.yml]'s `spawn-limits`. Setting this lower will make it feel as if there are more mobs around you. This should be lower than or equal to your simulation distance, and never larger than your hard despawn range / 16.

#### entity-activation-range

```
Good starting values:

      animals: 16
      monsters: 24
      raiders: 48
      misc: 8
      water: 8
      villagers: 16
      flying-monsters: 48
```

You can set what distance from the player an entity should be for it to tick (do stuff). Reducing those values helps performance, but may result in irresponsive mobs until the player gets really close to them. Lowering this too far can break certain mob farms; iron farms being the most common victim.

#### entity-tracking-range

```
Good starting values:

      players: 48
      animals: 48
      monsters: 48
      misc: 32
      other: 64
```

This is distance in blocks from which entities will be visible. They just won't be sent to players. If set too low this can cause mobs to seem to appear out of nowhere near a player. In the majority of cases this should be higher than your `entity-activation-range`.

#### tick-inactive-villagers

`Good starting value: false`

This allows you to control whether villagers should be ticked outside of the activation range. This will make villagers proceed as normal and ignore the activation range. Disabling this will help performance, but might be confusing for players in certain situations. This may cause issues with iron farms and trade restocking.

#### nerf-spawner-mobs

`Good starting value: true`

You can make mobs spawned by a monster spawner have no AI. Nerfed mobs will do nothing. You can make them jump while in water by changing `spawner-nerfed-mobs-should-jump` to `true` in [paper-world configuration].

### [paper-world configuration]

#### despawn-ranges

```
Good starting values:

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

Lets you adjust entity despawn ranges (in blocks). Lower those values to clear the mobs that are far away from the player faster. You should keep soft range around `30` and adjust hard range to a bit more than your actual simulation-distance, so mobs don't immediately despawn when the player goes just beyond the point of a chunk being loaded (this works well because of `delay-chunk-unloads-by` in [paper-world configuration]). When a mob is out of the hard range, it will be instantly despawned. When between the soft and hard range, it will have a random chance of despawning. Your hard range should be larger than your soft range. You should adjust this according to your view distance using `(simulation-distance * 16) + 8`. This partially accounts for chunks that haven't been unloaded yet after player visited them.

#### per-player-mob-spawns

`Good starting value: true`

This option decides if mob spawns should account for how many mobs are around target player already. You can bypass a lot of issues regarding mob spawns being inconsistent due to players creating farms that take up the entire mobcap. This will enable a more singleplayer-like spawning experience, allowing you to set lower `spawn-limits`. Enabling this does come with a very slight performance impact, however it's impact is overshadowed by the improvements in `spawn-limits` it allows.

#### max-entity-collisions

`Good starting value: 2`

Overwrites option with the same name in [spigot.yml]. It lets you decide how many collisions one entity can process at once. Value of `0` will cause inability to push other entities, including players. Value of `2` should be enough in most cases. It's worth noting that this will render maxEntityCramming gamerule useless if its value is over the value of this config option.

#### update-pathfinding-on-block-update

`Good starting value: false`

Disabling this will result in less pathfinding being done, increasing performance. In some cases this will cause mobs to appear more laggy; They will just passively update their path every 5 ticks (0.25 sec).

#### fix-climbing-bypassing-cramming-rule

`Good starting value: true`

Enabling this will fix entities not being affected by cramming while climbing. This will prevent absurd amounts of mobs being stacked in small spaces even if they're climbing (spiders).

#### armor-stands.tick

`Good starting value: false`

In most cases you can safely set this to `false`. If you're using armor stands or any plugins that modify their behavior and you experience issues, re-enable it. This will prevent armor stands from being pushed by water or being affected by gravity.

#### armor-stands.do-collision-entity-lookups

`Good starting value: false`

Here you can disable armor stand collisions. This will help if you have a lot of armor stands and don't need them colliding with anything.

#### tick-rates

```
Good starting values:

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

> It is not recommended to change these values from their defaults while [Pufferfish's DAB](#dabenabled) is enabled!

This decides how often specified behaviors and sensors are being fired in ticks. `acquirepoi` for villagers seems to be the heaviest behavior, so it's been greately increased. Decrease it in case of issues with villagers finding their way around.

### [pufferfish.yml]

#### dab.enabled

`Good starting value: true`

DAB (dynamic activation of brain) reduces the amount an entity is ticked the further away it is from players. DAB works on a gradient instead of a hard cutoff like EAR. Instead of fully ticking close entities and barely ticking far entities, DAB will reduce the amount an entity is ticked based on the result of a calculation influenced by [dab.activation-dist-mod](#dabactivation-dist-mod).

#### dab.max-tick-freq

`Good starting value: 20`

Defines the slowest amount entities farthest from players will be ticked. Increasing this value may improve the performance of entities far from view but may break farms or greatly nerf mob behavior. If enabling DAB breaks mob farms, try decreasing this value.

#### dab.activation-dist-mod

`Good starting value: 7`

Controls the gradient in which mobs are ticked. Decreasing this will activate DAB closer to players, improving DAB's performance gains, but will affect how entities interact with their surroundings and may break mob farms. If enabling DAB breaks mob farms, try increasing this value.

#### enable-async-mob-spawning

`Good starting value: true`

If asynchronous mob spawning should be enabled. For this to work, the Paper's per-player-mob-spawns setting must be enabled. This option does not actually spawn mobs asynchronous, but does offload much of the computational effort involved with spawning new mobs to a different thread. Enabling this option should not be noticeable on vanilla gameplay.

#### enable-suffocation-optimization

`Good starting value: true`

This option optimises a suffocation check (the check to see if a mob is inside a block and if they should take suffocation damage), by rate limiting the check to the damage timeout. This optimisation should be impossible to notice unless you're an extremely technical player who's using tick-precise timing to kill an entity at exactly the right time by suffocation.

#### inactive-goal-selector-throttle

`Good starting value: true`

Throttles the AI goal selector in entity inactive ticks, causing the inactive entities to update their goal selector every 20 ticks instead of every tick. Can improve performance by a few percent, and has minor gameplay implications.

### [purpur.yml]

#### zombie.aggressive-towards-villager-when-lagging

`Good starting value: false`

Enabling this will cause zombies to stop targeting villagers if the server is below the tps threshold set with `lagging-threshold` in [purpur.yml].

#### entities-can-use-portals

`Good starting value: false`

This option can disable portal usage of all entities besides the player. This prevents entities from loading chunks by changing worlds which is handled on the main thread. This has the side effect of entities not being able to go through portals.

#### villager.lobotomize.enabled

`Good starting value: true`

> This should only be enabled if villagers are causing lag! Otherwise, the pathfinding checks may decrease performance.

Lobotomized villagers are stripped from their AI and only restock their offers every so often. Enabling this will lobotomize villagers that are unable to pathfind to their destination. Freeing them should unlobotomize them.

---

## Misc

### [spigot.yml]

#### merge-radius

```
Good starting values:

      item: 3.5
      exp: 4.0
```

This decides the distance between the items and exp orbs to be merged, reducing the amount of items ticking on the ground. Setting this too high will lead to the illusion of items or exp orbs disappearing as they merge together. Setting this too high will break some farms, as well as allow items to teleport through blocks. There are no checks done to prevent items from merging through walls. Exp is only merged on creation.

#### hopper-transfer

`Good starting value: 8`

Time in ticks that hoppers will wait to move an item. Increasing this will help improve performance if there are a lot of hoppers on your server, but will break hopper-based clocks and possibly item sorting systems if set too high.

#### hopper-check

`Good starting value: 8`

Time in ticks between hoppers checking for an item above them or in the inventory above them. Increasing this will help performance if there are a lot of hoppers on your server, but will break hopper-based clocks and item sorting systems relying on water streams.

### [paper-world configuration]

#### alt-item-despawn-rate

```
Good starting values:

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
```

This list lets you set alternative time (in ticks) to despawn certain types of dropped items faster or slower than default. This option can be used instead of item clearing plugins along with `merge-radius` to improve performance.

#### redstone-implementation

`Good starting value: ALTERNATE_CURRENT`

Replaces the redstone system with faster and alternative versions that reduce redundant block updates, lowering the amount of logic your server has to calculate. Using a non-vanilla implementation may introduce minor inconsistencies with very technical redstone, but the performance gains far outweigh the possible niche issues. A non-vanilla implementation option may additionally fix other redstone inconsistencies caused by CraftBukkit.

The `ALTERNATE_CURRENT` implementation is based off of the [Alternate Current](https://modrinth.com/mod/alternate-current) mod. More information on this algorithm can be found on their resource page.

#### hopper.disable-move-event

`Good starting value: false`

`InventoryMoveItemEvent` doesn't fire unless there is a plugin actively listening to that event. This means that you only should set this to true if you have such plugin(s) and don't care about them not being able to act on this event. **Do not set to true if you want to use plugins that listen to this event, e.g. protection plugins!**

#### hopper.ignore-occluding-blocks

`Good starting value: true`

Determines if hoppers will ignore containers inside full blocks, for example hopper minecart inside sand or gravel block. Keeping this enabled will break some contraptions depending on that behavior.

#### tick-rates.mob-spawner

`Good starting value: 2`

This option lets you configure how often spawners should be ticked. Higher values mean less lag if you have a lot of spawners, although if set too high (relative to your spawners delay) mob spawn rates will decrease.

#### optimize-explosions

`Good starting value: true`

Setting this to `true` replaces the vanilla explosion algorithm with a faster one, at a cost of slight inaccuracy when calculating explosion damage. This is usually not noticeable.

#### treasure-maps.enabled

`Good starting value: false`

Generating treasure maps is extremely expensive and can hang a server if the structure it's trying to locate is in an ungenerated chunk. It's only safe to enable this if you pregenerated your world and set a vanilla world border.

#### treasure-maps.find-already-discovered

```
Good starting values:
      loot-tables: true
      villager-trade: true
```

Default value of this option forces the newly generated maps to look for unexplored structure, which are usually in not yet generated chunks. Setting this to true makes it so maps can lead to the structures that were discovered earlier. If you don't change this to `true` you may experience the server hanging or crashing when generating new treasure maps. `villager-trade` is for maps traded by villagers and loot-tables refers to anything that generates loot dynamically like treasure chests, dungeon chests, etc.

#### tick-rates.grass-spread

`Good starting value: 4`

Time in ticks between the server trying to spread grass or mycelium. This will make it so large areas of dirt will take a little longer to turn to grass or mycelium. Setting this to around `4` should work nicely if you want to decrease it without the decreased spread rate being noticeable.

#### tick-rates.container-update

`Good starting value: 1`

Time in ticks between container updates. Increasing this might help if container updates cause issues for you (it rarely happens), but makes it easier for players to experience desync when interacting with inventories (ghost items).

#### non-player-arrow-despawn-rate

`Good starting value: 20`

Time in ticks after which arrows shot by mobs should disappear after hitting something. Players can't pick these up anyway, so you may as well set this to something like `20` (1 second).

#### creative-arrow-despawn-rate

`Good starting value: 20`

Time in ticks after which arrows shot by players in creative mode should disappear after hitting something. Players can't pick these up anyway, so you may as well set this to something like `20` (1 second).

### [pufferfish.yml]

#### disable-method-profiler

`Good starting value: true`

This option will disable some additional profiling done by the game. This profiling is not necessary to run in production and can cause additional lag.

### [purpur.yml]

#### dolphin.disable-treasure-searching

`Good starting value: true`

Prevents dolphins from performing structure search similar to treasure maps

#### teleport-if-outside-border

`Good starting value: true`

Allows you to teleport the player to the world spawn if they happen to be outside of the world border. Helpful since the vanilla world border is bypassable and the damage it does to the player can be mitigated.

---

## Helpers

### [paper-world configuration]

#### anti-xray.enabled

`Good starting value: true`

Enable this to hide ores from x-rayers. For detailed configuration of this feature check out [Configuring Anti-Xray](https://docs.papermc.io/paper/anti-xray). Enabling this will actually decrease performance, however it is much more efficient than any anti-xray plugin. In most cases the performance impact will be negligible.

#### nether-ceiling-void-damage-height

`Good starting value: 127`

If this option is greater that `0`, players above the set y level will be damaged as if they were in the void. This will prevent players from using the nether roof. Vanilla nether is 128 blocks tall, so you should probably set it to `127`. If you modify the height of the nether in any way you should set this to `[your_nether_height] - 1`.

---

# Java startup flags
[Vanilla Minecraft and Minecraft server software in version 1.19 requires Java 17 or higher](https://docs.papermc.io/java-install-update). Oracle has changed their licensing, and there is no longer a compelling reason to get your java from them. Recommended vendors are [Adoptium](https://adoptium.net/) and [Amazon Corretto](https://aws.amazon.com/corretto/). Alternative JVM implementations such as OpenJ9 or GraalVM can work, however they are not supported by Paper and have been known to cause issues, therefore they are not currently recommended.

Your garbage collector can be configured to reduce lag spikes caused by big garbage collector tasks. You can find startup flags optimized for Minecraft servers [here](https://docs.papermc.io/paper/aikars-flags) [`SOG`]. Keep in mind that this recommendation will not work on alternative JVM implementations.
It's recommended to use the [flags.sh](https://flags.sh) startup flags generator to get the correct startup flags for your server

In addition, adding the beta flag `--add-modules=jdk.incubator.vector` before `-jar` in your startup flags can improve performance. This flag enables Pufferfish to use SIMD instructions on your CPU, making some maths faster. Currently, it's only used for making rendering in game plugin maps (like imageonmaps) possibly 8 times faster.

# "Too good to be true" plugins

## Plugins removing ground items
Absolutely unnecessary since they can be replaced with [merge-radius](#merge-radius) and [alt-item-despawn-rate](#alt-item-despawn-rate) and frankly, they're less configurable than basic server configs. They tend to use more resources scanning and removing items than not removing the items at all.

## Mob stacker plugins
It's really hard to justify using one. Stacking naturally spawned entities causes more lag than not stacking them at all due to the server constantly trying to spawn more mobs. The only "acceptable" use case is for spawners on servers with a large amount of spawners.

## Plugins enabling/disabling other plugins
Anything that enables or disables plugins on runtime is extremely dangerous. Loading a plugin like that can cause fatal errors with tracking data and disabling a plugin can lead to errors due to removing dependency. The `/reload` command suffers from exact same issues and you can read more about them in [me4502's blog post](https://madelinemiller.dev/blog/problem-with-reload/)

# What's lagging? - measuring performance

## mspt
Paper offers a `/mspt` command that will tell you how much time the server took to calculate recent ticks. If the first and second value you see are lower than 50, then congratulations! Your server is not lagging! If the third value is over 50 then it means there was at least 1 tick that took longer. That's completely normal and happens from time to time, so don't panic.
  
## Spark
[Spark](https://spark.lucko.me/) is a plugin that allows you to profile your server's CPU and memory usage. You can read on how to use it [on its wiki](https://spark.lucko.me/docs/). There's also a guide on how to find the cause of lag spikes [here](https://spark.lucko.me/docs/guides/Finding-lag-spikes).

## Timings
Way to see what might be going on when your server is lagging are Timings. Timings is a tool that lets you see exactly what tasks are taking the longest. It's the most basic troubleshooting tool and if you ask for help regarding lag you will most likely be asked for your Timings. Timings is known to have a serious performance impact on servers, it's recommended to use the Spark plugin over Timings and use Purpur or Pufferfish to disable Timings all together.

To get Timings of your server, you just need to execute the `/timings paste` command and click the link you're provided with. You can share this link with other people to let them help you. It's also easy to misread if you don't know what you're doing. There is a detailed [video tutorial by Aikar](https://www.youtube.com/watch?v=T4J0A9l7bfQ) on how to read them.

[`SOG`]: https://www.spigotmc.org/threads/guide-server-optimization%E2%9A%A1.283181/
[server.properties]: https://minecraft.fandom.com/wiki/Server.properties
[bukkit.yml]: https://bukkit.fandom.com/wiki/Bukkit.yml
[spigot.yml]: https://www.spigotmc.org/wiki/spigot-configuration/
[paper-global configuration]: https://docs.papermc.io/paper/reference/global-configuration
[paper-world configuration]: https://docs.papermc.io/paper/reference/world-configuration
[purpur.yml]: https://purpurmc.org/docs/Configuration/
[pufferfish.yml]: https://docs.pufferfish.host/setup/pufferfish-fork-configuration/
[Petal]: https://github.com/Bloom-host/Petal
