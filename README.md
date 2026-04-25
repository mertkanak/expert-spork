# Android Kotlin Senior Yol Haritası

> **Hedef:** Intermediate → Senior geçişi. AI'ın yazdığı kodu yorumlayabilen, mimari kararlar alabilen, performans/güvenlik/build sistemine hâkim Android developer olmak.
>
> **Süre:** Toplam ~22 hafta (yaklaşık 5 ay). Günde 1.5-2 saat. Hafta sonları proje günü.
>
> **Felsefe:** Her konuyu **3 katmanda** öğren — *Ne (sözdizimi), Nasıl (kullanım), Neden (iç işleyiş)*. Senior olmanın farkı bu 3. katmanda yatar. AI sana "ne" ve "nasıl"ı verir; "neden"i sen kapatmalısın.
>
> **Ön koşul:** Kotlin temelleri (sınıf, fun, when, null safety, collection'lar, basit lambda).

---

## 📚 Plan Yapısı

| Faz | Hafta | Hedef Seviye | Odak |
|-----|-------|--------------|------|
| **Faz 0** | 1-2 | Temel Sağlamlama | JVM, Gradle, Build Sistemi |
| **Faz 1** | 3-6 | Intermediate | Mimari, Coroutines, Flow, DI |
| **Faz 2** | 7-10 | Intermediate+ | Compose, Networking, Persistence |
| **Faz 3** | 11-14 | Pre-Senior | Test, Performans, Güvenlik |
| **Faz 4** | 15-18 | Senior | Modularization, Build Optimization, Baseline Profiles |
| **Faz 5** | 19-22 | Senior+ | System Design, KMP, Liderlik Pratikleri |

---

## 🎯 Haftalık Çalışma Şablonu (Her Hafta İçin)

| Gün | Süre | Aktivite |
|-----|------|----------|
| Pzt | 90 dk | Yeni konuyu **resmi dokümanlarla** öğren. Notlarını kendi cümlelerinle yaz. |
| Sal | 90 dk | Konunun **iç işleyişini** araştır. "Nasıl çalışıyor?" değil "Neden böyle tasarlanmış?" sor. |
| Çar | 90 dk | **Kendi başına** kod yaz. AI'a sorma, deneyerek öğren. Hatalarını blog'a/notuna yaz. |
| Per | 90 dk | AI'ın yazdığı bir kodu **satır satır** review et. "Bu satır neden burada?" diye sor. |
| Cum | 60 dk | Stack Overflow / GitHub'da gerçek dünya örneklerini incele. |
| Cmt | 3 saat | Hafta projesi: O haftaki konuyu küçük bir uygulamada uygula. |
| Paz | 60 dk | **Spaced repetition**: Önceki hafta öğrendiklerini hatırlamaya çalış. Notları gözden geçir. |

> **Önemli:** Pazar günü tekrarı atlama. Senior olmanın sırrı yeni konu sürekli görmek değil, eskileri unutmamaktır.

---

# 📘 FAZ 0 — TEMEL SAĞLAMLAMA (Hafta 1-2)

> **Neden buradan başlıyoruz?** Çoğu Android developer Gradle ve JVM'i "olduğu gibi kabul eder". Sen senior olacaksın — build sistemi senin için kara kutu olamaz.

## Hafta 1 — JVM, Bytecode, Kotlin Internals

### Pzt-Sal: JVM ve Android Runtime
- **Konular:**
  - JVM nedir? Class loading, bytecode, JIT.
  - ART (Android Runtime) ne yapar? AOT vs JIT compilation.
  - Dalvik → ART geçişi neden önemliydi?
  - Garbage Collection: Generational GC, mark-and-sweep, ART'ta GC.
  - Kotlin → bytecode: `kotlinc -Xshow-bytecode` ile incele.
- **Kaynaklar:**
  - [Android Runtime overview](https://source.android.com/docs/core/runtime)
  - "Java Performance" - Scott Oaks (kitap, ilk 3 bölüm yeterli)
  - [Kotlin Bytecode Decompiler in IntelliJ](https://kotlinlang.org/docs/whatsnew11.html#tools-for-bytecode-and-decompilation)
- **Pratik:** Bir Kotlin `data class` yaz, IntelliJ'de "Show Kotlin Bytecode" → "Decompile" yap. `equals/hashCode/copy/componentN` nasıl üretilmiş gör.

### Çar-Per: Kotlin Internals
- **Konular:**
  - `inline`, `noinline`, `crossinline` — gerçekten ne işe yarar? Lambda allocation maliyeti.
  - `reified` type parameters — neden sadece `inline` ile çalışır?
  - `value class` (eski adıyla `inline class`) — primitive boxing'i nasıl önler?
  - `object` vs `companion object` — Java tarafından nasıl görünür?
  - Delegated properties: `by lazy`, `by Delegates.observable`, `by viewModels()`.
  - Kotlin Contracts API: `contract { returns(true) implies (...) }`.
- **Kaynaklar:**
  - [Kotlin Inline Functions](https://kotlinlang.org/docs/inline-functions.html)
  - [Kt Academy - Effective Kotlin](https://kt.academy/book/effectivekotlin) (Item 46-49)
- **Pratik:** Kendi `inline fun <reified T> Bundle.getEnum(key: String): T` extension'ını yaz.

### Cmt: Mini Proje
Mevcut bir projedeki tüm `lateinit var` kullanımlarını sırasıyla analiz et: Hangileri `by lazy` olabilir? Hangileri `value class` ile sarılabilir? `inline reified` ile sadeleşebilecek util fonksiyonları bul.

---

## Hafta 2 — Gradle ve Build Sistemi (En Çok İhmal Edilen Konu)

### Pzt-Sal: Gradle Mental Modeli
- **Konular:**
  - **3 Aşama:** Initialization → Configuration → Execution. Hangisi ne zaman çalışır?
  - `settings.gradle.kts` ne yapar? `pluginManagement`, `dependencyResolutionManagement`.
  - `build.gradle.kts` (Kotlin DSL) vs Groovy DSL — neden Kotlin DSL?
  - Plugin'ler: `id("com.android.application")` arka planda ne yapıyor?
  - `dependencies` block: `implementation` vs `api` vs `compileOnly` vs `runtimeOnly`. Hangi kütüphane hangisinde olmalı?
  - `buildTypes` (debug/release) ve `productFlavors` (free/paid, dev/prod).
  - `BuildConfig` üretimi nasıl çalışır?
- **Kaynaklar:**
  - [Gradle User Manual - Build Lifecycle](https://docs.gradle.org/current/userguide/build_lifecycle.html)
  - [Configure your build (Android Developers)](https://developer.android.com/build)

### Çar-Per: Version Catalog ve Convention Plugins
- **Konular:**
  - `libs.versions.toml` formatı: `[versions]`, `[libraries]`, `[bundles]`, `[plugins]`.
  - `buildSrc` neden artık önerilmiyor? Composite builds (`build-logic`).
  - Convention Plugin yazma: Tüm modüllerde aynı Compose+Hilt+Detekt config'ini tek satırla uygulamak.
  - `apply plugin` vs Kotlin DSL plugins block.
- **Kaynaklar:**
  - [Now in Android - build-logic](https://github.com/android/nowinandroid/tree/main/build-logic) (KAYNAK PROJE — saatlerce inceleyebilirsin)
  - [Sharing build logic between subprojects](https://docs.gradle.org/current/samples/sample_convention_plugins.html)

### Cum-Cmt: Build Performance
- **Konular:**
  - Configuration Cache, Build Cache, Gradle Daemon.
  - `--profile` flag → build time analizi.
  - Gradle Build Scan (`--scan`) → bottleneck tespiti.
  - Modülarizasyonun build time'a etkisi (parallelization).
- **Pratik:** Mevcut bir projeyi clone'la (örn. NowInAndroid). `./gradlew assembleDebug --profile` çalıştır, raporu incele. Hangi task en uzun sürüyor?

### Paz: Tekrar
1. Gradle 3 aşamayı kendi cümlelerinle anlat. (Yazılı not.)
2. `implementation` ile `api`'yi 1 paragrafta açıkla.
3. Convention plugin'in `buildSrc`'a göre avantajını söyle.

> **🎯 Faz 0 Çıktısı:** Artık herhangi bir Android projesinin `build.gradle.kts` dosyasını açıp her satırı açıklayabiliyorsun. Bu çoğu mid-level developer'ın yapamadığı şey.

---

# 📗 FAZ 1 — INTERMEDIATE TEMELLERİ (Hafta 3-6)

## Hafta 3 — MVVM, Lifecycle, ViewModel Derinlemesine

### Konular
- **Activity/Fragment lifecycle:** `onCreate` → `onStart` → `onResume` → `onPause` → `onStop` → `onDestroy`. Configuration change'de ne olur?
- **ViewModel neden survive eder?** `ViewModelStore`, `ViewModelStoreOwner`, `ComponentActivity`'nin rolü.
- **`viewModels()` vs `ViewModelProvider(this)`:** Aynı şeyin syntactic sugar'ı.
- **`SavedStateHandle`:** Process death'e dayanıklı state. Nereden geliyor? Bundle ile ilişkisi.
- **MVVM vs MVP vs MVI:** Aralarındaki gerçek farklar (sadece "isim farkı" değil, veri akışı yönü farkı).
- **Single Source of Truth (SSOT) prensibi.**

### Kaynaklar
- [ViewModel overview (resmi)](https://developer.android.com/topic/libraries/architecture/viewmodel)
- [Guide to App Architecture](https://developer.android.com/topic/architecture)
- [Manuel Vivo - Locking ViewModels](https://manuelvivo.dev/viewmodels-locks)

### Pratik
Bir sayaç uygulaması yaz. 4 farklı versiyonu olsun:
1. `var` ile Activity içinde state → Configuration change'de kayıp.
2. `onSaveInstanceState` Bundle ile → kayıp olmuyor ama boilerplate çok.
3. ViewModel ile → temiz çözüm.
4. ViewModel + SavedStateHandle ile → process death'e de dayanır.

Her birinde "Don't keep activities" açarak test et.

---

## Hafta 4 — Coroutines Derinlemesine

> **Bu hafta kritik.** Coroutine'leri "yüzeyde" bilen çok kişi var. Sen iç işleyişini öğreneceksin.

### Pzt-Sal: Coroutine Mekanizması
- **Konular:**
  - **Continuation-Passing Style (CPS):** `suspend` fonksiyonlar bytecode'da nasıl `Continuation` parametresi alıyor?
  - State machine: `suspend` fonksiyonun decompile edilmiş hali.
  - `CoroutineContext`: `Job`, `Dispatcher`, `CoroutineExceptionHandler`, `CoroutineName` — birleşmiş context.
  - **Structured concurrency:** Parent-child Job hiyerarşisi. `coroutineScope` vs `supervisorScope`.
  - `launch` vs `async` (deferred result).
  - `Job` lifecycle: New → Active → Completing → Completed (veya Cancelled).
- **Kaynaklar:**
  - [Coroutines Guide (resmi)](https://kotlinlang.org/docs/coroutines-guide.html) — TÜMÜNÜ oku.
  - [Roman Elizarov - Coroutines: First things first](https://elizarov.medium.com/structured-concurrency-722d765aa952)
  - [Decompiling Kotlin Coroutines](https://www.youtube.com/watch?v=YrrUCSi72E8) - Roman Elizarov

### Çar-Per: Dispatcher'lar ve Cancellation
- **Konular:**
  - `Dispatchers.Main` vs `Main.immediate`: ne zaman fark yaratır?
  - `Dispatchers.IO` thread pool boyutu (varsayılan 64 + cores). Network/disk için neden?
  - `Dispatchers.Default`: CPU-bound iş. Pool boyutu = CPU çekirdek sayısı.
  - **Cancellation cooperative'tir:** `yield()`, `ensureActive()`, `isActive`. CPU-bound loop'larda neden gerekli?
  - `withContext(NonCancellable)` — ne zaman?
  - `CancellationException` neden özel? Niye `try/catch (Throwable)` kötü fikir?
- **Pratik:** Bir CPU-bound işlem yaz (büyük sayı çarpımı vb.). Cancel et. İşlem durmuyor — neden? `ensureActive()` ekleyip çöz.

### Cum-Cmt: Exception Handling
- **Konular:**
  - `try/catch` vs `CoroutineExceptionHandler` — `launch` vs `async`'te farklı davranış.
  - `supervisorScope` ile child failure'un parent'ı etkilememesi.
  - `runCatching` vs `try/catch` Kotlin idiomları.
- **Mini Proje:** Paralel olarak 5 API çağrısı yapan bir fonksiyon yaz. Bir tanesi fail olunca diğerleri devam etmeli (supervisor) veya hepsi durmalı (regular). İki versiyonu da implement et.

### Paz: Tekrar Soruları
1. `launch { ... }` ile başlatılan coroutine'de exception fırlarsa ne olur?
2. `viewModelScope` cancel olduğunda içindeki child'lar ne olur?
3. `withContext(Dispatchers.IO)` çağrısı yeni coroutine başlatır mı?

---

## Hafta 5 — Flow Derinlemesine

### Pzt-Sal: Cold Flow Temelleri
- **Konular:**
  - Cold flow: `flow { emit() }` — collector olmadan çalışmaz.
  - `flow.collect { }` — neden suspend?
  - Operatör'ler: `map`, `filter`, `transform`, `onEach`, `catch`, `onCompletion`.
  - `flowOn(Dispatchers.IO)` — context preservation, downstream'i etkilemez.
  - `buffer`, `conflate`, `collectLatest`.
- **Kaynaklar:**
  - [Flow guide (resmi)](https://kotlinlang.org/docs/flow.html)
  - [Roman Elizarov - Flow](https://elizarov.medium.com/cold-flows-hot-channels-d74769805f9)

### Çar-Per: Hot Flow (StateFlow & SharedFlow)
- **Konular:**
  - **StateFlow:** Conflated, replay=1, son değeri tutar. UI state için.
  - **SharedFlow:** Konfigüre edilebilir replay/buffer. Event'ler için.
  - **One-time event problemi:** StateFlow neden uygun değil? `Channel` alternatifi.
  - `stateIn(scope, SharingStarted.WhileSubscribed(5000), initialValue)` — **5000 ms neden?**
    - Configuration change sırasında collector'lar 1-2 frame için yok.
    - 0 olsaydı flow restart olur, gereksiz API çağrısı.
    - 5000 = "kullanıcı geri gelirse yeniden başlatma" güvenlik marjı.
  - `shareIn` vs `stateIn`.
- **Pratik:** Bir search feature yaz: `MutableStateFlow<String>(query)` → `debounce(300)` → `distinctUntilChanged()` → `flatMapLatest { api.search(it) }` → `stateIn`.

### Cum-Cmt: Flow Testing & Patterns
- **Konular:**
  - `Turbine` kütüphanesi: `flow.test { awaitItem() }`.
  - `combine`, `zip`, `merge` — ne zaman hangisi?
  - `collectAsStateWithLifecycle()` neden `collectAsState`'ten iyi?
- **Mini Proje:** İki StateFlow'u (kullanıcı + ayarlar) `combine` ile birleştirip türetilmiş UI state üreten bir ViewModel yaz. Turbine ile test et.

---

## Hafta 6 — Dependency Injection (Hilt) Derinlemesine

### Konular
- **DI neden var?** Manuel DI ile Hilt'i karşılaştırma. Service Locator anti-pattern'i.
- **Hilt mekanizması:**
  - `@HiltAndroidApp` → Application'a code generation.
  - `@AndroidEntryPoint` → Activity/Fragment'a inject support.
  - `@Inject constructor` → primary constructor injection.
- **Component hiyerarşisi:**
  - `SingletonComponent` (app lifetime) → `ActivityRetainedComponent` (config change'e dayanır) → `ViewModelComponent` → `ActivityComponent` → `FragmentComponent` → `ViewComponent`.
- **Modül anatomisi:**
  - `@Module @InstallIn(SingletonComponent::class)`.
  - `@Provides` vs `@Binds` — `@Binds` daha verimli (compile-time'da çözülür).
  - `@Qualifier` ve `@Named` — aynı tipten 2 farklı instance.
- **`@HiltViewModel` ve `hiltViewModel()`** Compose entegrasyonu.
- **Multi-binding:** `@IntoSet`, `@IntoMap` — plugin pattern.
- **Test'te DI:** `@TestInstallIn`, `@HiltAndroidTest`.

### Kaynaklar
- [Hilt guide](https://developer.android.com/training/dependency-injection/hilt-android)
- [Hilt under the hood](https://dagger.dev/hilt/) (Dagger dokümanları)
- [NowInAndroid Hilt setup](https://github.com/android/nowinandroid/tree/main/core/data) — gerçek örnek

### Pratik
1. Manuel DI ile bir login feature yaz: `LoginActivity` → `LoginViewModel` → `AuthRepository` → `AuthApi`. Tüm bağımlılıkları elinle geç.
2. Aynı projeyi Hilt'e çevir. Ne kadar boilerplate gitti gör.
3. Testte `FakeAuthRepository` kullanmak için `@TestInstallIn` yaz.

> **🎯 Faz 1 Çıktısı:** Artık MVVM + Coroutines + Flow + Hilt'i sadece kullanmıyorsun, **iç işleyişini** anlıyorsun. Bir API çağrısının nereden gelip nereye gittiğini her katmanda açıklayabiliyorsun.

---

# 📕 FAZ 2 — INTERMEDIATE+ (Hafta 7-10)

## Hafta 7 — Networking (Retrofit + OkHttp) Production-Ready

### Konular
- **OkHttp Interceptor zinciri:**
  - Application interceptor vs Network interceptor — fark nedir? Cache nasıl etkilenir?
  - Logging interceptor — production'da neden kapatmalı?
  - Auth interceptor + Authenticator — token refresh mekanizması.
- **Retrofit 2 internals:**
  - `@GET`, `@POST`, `@Body`, `@Path`, `@Query`, `@QueryMap`.
  - Converter (Moshi vs Gson vs kotlinx.serialization).
  - **Moshi neden Gson'dan iyi?** Codegen, null safety, Kotlin support.
- **Suspend support:** Retrofit `suspend fun` döndürür → Call adapter gerekmez.
- **Hata yönetimi:**
  - `Result<T>` sealed class pattern.
  - `runCatching` vs Retrofit'in HTTP exception'ları.
  - `IOException` vs `HttpException` ayrımı.
- **Caching:**
  - OkHttp cache (`Cache(File, Long)`).
  - `Cache-Control` header'ları.
  - ETag, If-None-Match.
- **Certificate Pinning:** `CertificatePinner` — MITM koruması.

### Kaynaklar
- [Retrofit docs](https://square.github.io/retrofit/)
- [OkHttp interceptors](https://square.github.io/okhttp/features/interceptors/)
- [Jake Wharton - Making Retrofit Work for You (talk)](https://www.youtube.com/watch?v=nyPUkjEbFeU)

### Pratik
GitHub API client yaz:
- Login → token al
- Auth interceptor ekle (her isteğe Bearer token)
- Authenticator ekle (401 alınca refresh)
- Cache yapılandır
- Logging interceptor (sadece debug)
- Sealed `NetworkResult<T>` ile UI'a sun

---

## Hafta 8 — Persistence (Room + DataStore)

### Pzt-Sal: Room İleri Seviye
- **Konular:**
  - `@Entity`, `@PrimaryKey`, `@ColumnInfo`, `@Index`.
  - `@Embedded` (composition) — düz tablo, ilişkisiz.
  - `@Relation` — one-to-many, many-to-many.
  - `@Junction` — many-to-many ara tablo.
  - `@TypeConverter` — Date, List<String> gibi custom tipler.
  - `Flow<T>` döndüren DAO — reactive query.
  - **Migration:** `Migration(from, to)`, `AutoMigration`, `fallbackToDestructiveMigration` ne zaman OK?
  - `@DatabaseView` — read-only logical view.
  - Multi-instance lock — neden Room singleton olmalı?
- **Kaynaklar:**
  - [Room with a view (resmi rehber)](https://developer.android.com/training/data-storage/room)
  - [Room Migrations](https://developer.android.com/training/data-storage/room/migrating-db-versions)

### Çar-Per: DataStore (SharedPreferences'ı Bırak)
- **Konular:**
  - **Preferences DataStore:** Key-value, type-safe değil.
  - **Proto DataStore:** Schema-defined (.proto file), type-safe.
  - Singleton şart — neden? File corruption riski.
  - `Flow<Preferences>` — reactive okuma.
  - `edit { preferences -> ... }` — atomic write.
  - SharedPreferences'tan migration.
- **Pratik:** Mevcut `SharedPreferences` kullanan bir feature'ı Proto DataStore'a migrate et.

### Cum-Cmt: Offline-First Mimarisi
- **Konular:**
  - **NetworkBoundResource pattern:** Cache → Network → Update Cache.
  - Single Source of Truth (UI sadece DB'yi okur, network DB'yi günceller).
  - Refresh stratejileri: Pull-to-refresh, periodic sync (WorkManager), event-based.
  - Data freshness: `lastUpdated` timestamp pattern.
- **Mini Proje:** Haber uygulaması:
  - Room cache + Retrofit + Proto DataStore (kullanıcı tercihleri).
  - Internet yokken cache'den göster.
  - Pull-to-refresh ile güncelle.
  - WorkManager ile her 1 saatte sync.

---

## Hafta 9-10 — Jetpack Compose

> **Not:** Compose roadmap dosyasındaki içeriği aynen takip et. Burada ek olarak ihmal edilen konuları topluyorum.

### Hafta 9: Compose Mental Modeli ve State (Compose roadmap'inde Konu 1-5)
- Compose declarative UI mantığı
- `remember`, `mutableStateOf`, `rememberSaveable`
- Side effects: `LaunchedEffect`, `DisposableEffect`, `SideEffect`
- Modifier sistemi (sıra önemli!)
- Material 3

### Hafta 10: Compose'da Performans + Test (Compose roadmap'inde Konu 18, 16-17)
- **Stability ve Strong Skipping Mode:**
  - `@Stable`, `@Immutable` annotation'ları.
  - `kotlinx-collections-immutable` neden? `List<T>` neden unstable?
  - Lambda memoization.
  - Compose Compiler Metrics: `composeCompiler { reportsDestination.set(...) }`.
- **Recomposition tracing:** Layout Inspector + recomposition counts.
- **`derivedStateOf` ne zaman?** Yanlış kullanılırsa zarar verir.
- **`@SnapshotFlow`** — Compose state'i Flow'a çevirme.

### Ek Compose Konuları (Roadmap'te Olmayan)
- **Custom Layout:** `Layout(measurePolicy)` ile sıfırdan layout yazma.
- **Custom Modifier:** `Modifier.Node` API (Compose 1.6+).
- **Animation API:** `animateXxxAsState`, `Transition`, `AnimatedContent`, `LookaheadLayout`.
- **Compose Multiplatform** kısa giriş.

### Kaynaklar
- [Now in Android](https://github.com/android/nowinandroid) — production Compose örneği
- [Compose Performance](https://developer.android.com/develop/ui/compose/performance)
- [Strong Skipping Mode](https://developer.android.com/develop/ui/compose/performance/stability/strongskipping)
- [Jorge Castillo - Composable functions in depth](https://newsletter.jorgecastillo.dev/)

> **🎯 Faz 2 Çıktısı:** Compose'da production-grade ekran yazabiliyorsun. Networking ve persistence katmanlarında en iyi pratikleri uyguluyorsun. Offline-first mimarisini kuruyorsun.

---

# 📙 FAZ 3 — PRE-SENIOR (Hafta 11-14)

## Hafta 11 — Test Stratejisi (Sadece Yazmak Değil, Doğru Test Yazmak)

### Konular
- **Test piramidi:** Unit (çok) → Integration (orta) → UI (az).
- **Unit Test:**
  - JUnit 4 vs JUnit 5 — Android'de hâlâ JUnit 4 dominant.
  - **`MockK` vs `Mockito-Kotlin`** — MockK Kotlin idiomatic.
  - **Mock vs Fake vs Stub vs Spy** — farkları (bu interview sorusu).
  - **Tercih:** Fake > Mock. Mock kırılgan, Fake kontrolünde.
- **Coroutine/Flow Test:**
  - `kotlinx-coroutines-test`: `runTest`, `TestScope`, `StandardTestDispatcher` vs `UnconfinedTestDispatcher`.
  - `MainDispatcherRule` — `Dispatchers.setMain(testDispatcher)`.
  - **Turbine** — Flow test için endüstri standardı.
- **Robolectric:** JVM'de Android framework'ünü simüle etme. Hızlı, ama gerçek cihaz değil.
- **Compose UI Test:**
  - `createComposeRule()` vs `createAndroidComposeRule()`.
  - `onNodeWithTag` (locale-bağımsız) > `onNodeWithText`.
  - `waitUntilNodeCount`, `waitForIdle`.
- **Espresso:** XML View'lı projeler için — eğer mevcut projende varsa.
- **Screenshot test:** Paparazzi (JVM, hızlı), Roborazzi.

### Kaynaklar
- [Testing in Android](https://developer.android.com/training/testing)
- [Turbine](https://github.com/cashapp/turbine)
- [Now in Android - Testing](https://github.com/android/nowinandroid/tree/main/sync/work/src/test)
- [MockK documentation](https://mockk.io/)

### Pratik
Önceki haftalarda yazdığın bir feature'ın:
1. ViewModel için unit test yaz (Turbine + Fake repo).
2. Compose ekranı için UI test yaz (testTag ile).
3. Repository için integration test (gerçek Room + Fake API).
4. Coverage raporu üret (`./gradlew testDebugUnitTestCoverage`).

---

## Hafta 12 — Performans Profiling

### Konular
- **Android Studio Profiler:**
  - **CPU Profiler:** Method tracing, sample tracing, system tracing.
  - **Memory Profiler:** Heap dump, allocation tracking. Memory leak tespiti.
  - **Energy Profiler:** Battery impact.
  - **Network Profiler:** Request inspection.
- **Memory Leaks:**
  - Activity leak (Context referansı).
  - **LeakCanary** — production'da kullanma! Sadece debug.
  - Static field leak'leri.
- **Frame drops & jank:**
  - 60fps = 16.67ms/frame, 120fps = 8.33ms/frame.
  - Choreographer, VSYNC.
  - Layout Inspector — recomposition counts.
- **ANR (Application Not Responding):**
  - 5 saniye main thread bloklanma.
  - StrictMode — debug'da disk/network on main thread tespiti.
- **Macrobenchmark vs Microbenchmark:**
  - Microbenchmark: Function-level, Jetpack benchmark library.
  - Macrobenchmark: Cold/warm startup, scrolling jank.
- **Baseline Profiles:** AOT compilation hint'i. Cold start %30 hızlanma.
- **App Startup Library:** ContentProvider startup'ı engelleme.

### Kaynaklar
- [Profile your app performance](https://developer.android.com/studio/profile)
- [Macrobenchmark](https://developer.android.com/topic/performance/benchmarking/macrobenchmark-overview)
- [Baseline Profiles](https://developer.android.com/topic/performance/baselineprofiles)
- [Now in Android - Baseline Profile](https://github.com/android/nowinandroid/tree/main/benchmarks)

### Pratik
1. Bir LazyColumn'lu liste yaz, 1000 item ekle.
2. Layout Inspector ile recomposition'ları izle.
3. Macrobenchmark testi yaz: cold startup ölç, baseline profile generate et.
4. Cold startup'ı baseline profile öncesi/sonrası karşılaştır.

---

## Hafta 13 — Güvenlik

### Konular
- **Network Security:**
  - HTTPS zorunluluğu (`networkSecurityConfig.xml`).
  - Certificate Pinning — MITM koruması (OkHttp `CertificatePinner`).
  - Cleartext traffic engelleme.
- **Data Security:**
  - **EncryptedSharedPreferences** ve **EncryptedFile** (Jetpack Security).
  - **Android Keystore** — anahtar saklama.
  - Biometric authentication (BiometricPrompt).
  - **DataStore + EncryptedFile** kombinasyonu.
- **Code Security:**
  - **R8 (eski adıyla ProGuard):**
    - `minifyEnabled true` — ne yapar? Shrink, optimize, obfuscate.
    - `proguard-rules.pro` — keep rule'lar.
    - Reflection kullanan library'ler için keep rule (Retrofit, Moshi, Hilt — çoğu artık otomatik).
  - **APK analizi:** Android Studio "Analyze APK".
  - **Dexguard** vs ProGuard (commercial).
- **Permission'lar:**
  - Runtime permissions (Android 6+).
  - Permission rationale.
  - **Scoped Storage** (Android 10+).
  - Photo Picker (Android 13+).
- **Backup ve Privacy:**
  - `android:allowBackup="false"` — ne zaman?
  - `data_extraction_rules.xml` (Android 12+).
  - Privacy manifest (Apple-style, Android 14+).

### Kaynaklar
- [App security best practices](https://developer.android.com/topic/security/best-practices)
- [Jetpack Security](https://developer.android.com/topic/security/data)
- [R8 documentation](https://developer.android.com/build/shrink-code)

### Pratik
1. Login feature'ında token'ı `EncryptedSharedPreferences`'ta sakla.
2. Certificate pinning ekle (örnek bir public API'ye).
3. Release build'de `minifyEnabled true` aç. Crash olursa ProGuard rule'larını yaz.
4. APK'yi analyze et, küçültmeden önce/sonra boyut farkı ne?

---

## Hafta 14 — Background Work ve Sistem Entegrasyonu

### Konular
- **WorkManager:**
  - `OneTimeWorkRequest`, `PeriodicWorkRequest`.
  - Constraints (network, charging, idle).
  - `Worker` vs `CoroutineWorker`.
  - Chained work, parallel work.
  - `setExpedited()` — Android 12+.
  - **Hilt + WorkManager:** `@HiltWorker`.
- **Foreground Service:**
  - Android 14+ kısıtlamaları (`foregroundServiceType` zorunlu).
  - Notification gerekliliği.
- **Broadcast Receiver:**
  - Static (manifest) vs dynamic (kod).
  - Android 8+ background limitations.
  - Local Broadcast (deprecated, alternatif: `LiveData`/`Flow`).
- **Notification:**
  - Channel'lar (Android 8+).
  - Notification permission (Android 13+).
  - PendingIntent flag'leri (FLAG_IMMUTABLE Android 12+).
- **Deep Linking:**
  - Implicit deep link (intent-filter).
  - App Links (verified).
  - Compose Navigation deep link.

### Kaynaklar
- [WorkManager guide](https://developer.android.com/topic/libraries/architecture/workmanager)
- [Background work overview](https://developer.android.com/develop/background-work/background-tasks)

### Pratik
WorkManager ile:
1. Her 6 saatte API'den haber sync eden periodic work yaz.
2. Sadece WiFi'da çalışsın.
3. Hilt ile inject et.
4. Notification göster (yeni haber sayısı).

> **🎯 Faz 3 Çıktısı:** Test yazmak alışkanlık oldu. Performans profiling yapabiliyorsun. Güvenlik en iyi pratiklerini uyguluyorsun. Background work doğru kullanıyorsun. Bu seviyede mid-level → senior eşiğindesin.

---

# 🚀 FAZ 4 — SENIOR (Hafta 15-18)

## Hafta 15-16 — Modularization

### Hafta 15: Mimari ve Modül Tipleri
- **Neden Modularization?**
  - Build time (parallel compilation).
  - Separation of concerns.
  - Reusability.
  - Team scalability.
  - Dynamic feature delivery.
- **Modül tipleri:**
  - `app` — entry point.
  - `feature:xxx` — UI + ViewModel + screen-specific logic.
  - `core:domain` — UseCase'ler, business logic (pure Kotlin).
  - `core:data` — Repository implementations, data sources.
  - `core:network` — Retrofit, OkHttp config.
  - `core:database` — Room.
  - `core:datastore` — DataStore.
  - `core:model` — Domain model'leri (pure Kotlin).
  - `core:designsystem` — Compose theme, atomic components.
  - `core:ui` — shared UI helpers, common Composables.
  - `core:common` — utilities.
- **Bağımlılık yönü kuralları:**
  - `feature → core` ✅
  - `feature → feature` ❌ (navigator pattern ile çöz)
  - `core:data → core:network, core:database` ✅
  - `core:domain → core:model` ✅, `core:domain → core:data` ❌ (interface kullan)
- **`api` vs `implementation` modüller arası:**
  - `core:database` Room'u `implementation` ile alır → diğer modüller Room'u görmez. Doğru.
  - `core:network` Retrofit'i `api` mı `implementation` mı? Genelde `implementation` (encapsulation).

### Hafta 16: Convention Plugins ve Pratik
- **build-logic/ klasörünü kur:**
  - `composite build` ile included build.
  - `nowinandroid.android.application`, `nowinandroid.android.library`, `nowinandroid.android.library.compose`, `nowinandroid.android.feature`, `nowinandroid.android.hilt`.
- Her modülün `build.gradle.kts` 5-10 satır olur (sadece `plugins { id(...) }`).
- **Inter-module navigation:**
  - Navigation Compose'da feature module'ler arası navigation.
  - Type-safe `@Serializable` route'ları paylaşmak için `core:model` veya ayrı `core:navigation` modülü.

### Kaynaklar
- [Now in Android - ModularizationLearningJourney.md](https://github.com/android/nowinandroid/blob/main/docs/ModularizationLearningJourney.md) — **MUTLAKA OKU**
- [Modularization guide (resmi)](https://developer.android.com/topic/modularization)
- [Modularization patterns](https://developer.android.com/topic/modularization/patterns)

### Pratik (2 hafta)
Mevcut bir tek-modül projeni modularize et:
1. Önce `core:model` ve `core:network` çıkar.
2. Sonra `core:database` ve `core:data`.
3. Feature'ları `feature:xxx`'a böl.
4. `app` modülü sadece DI graph'ını birleştirsin ve navigation'ı barındırsın.
5. Convention plugin'leri yaz.
6. Build time'ı önce/sonra ölç.

---

## Hafta 17 — Build Optimization & CI/CD

### Build Optimization
- **Gradle profiling:**
  - `--profile` ile HTML rapor.
  - `--scan` ile cloud-based analiz.
- **Configuration Cache:** `org.gradle.configuration-cache=true`.
- **Parallel execution:** `org.gradle.parallel=true`.
- **JVM tuning:** `org.gradle.jvmargs=-Xmx4g -XX:+UseG1GC`.
- **Build Cache:** Local + remote (Gradle Enterprise).
- **KSP > KAPT:** Annotation processor performansı. Hilt KSP destekliyor.
- **Incremental compilation:** Kotlin compiler.
- **Module dependency graph:** `dependencyAnalysis` plugin ile gereksiz dependency tespiti.

### CI/CD
- **GitHub Actions:**
  - Build, test, lint workflow.
  - Matrix build (multiple Android versions).
  - Caching Gradle dependencies.
- **Firebase App Distribution:** Internal beta dağıtımı.
- **Google Play Console API:** Otomatik upload (`gradle-play-publisher` plugin).
- **Code quality:**
  - **Detekt** — static analysis.
  - **ktlint** — formatting.
  - **Android Lint** — built-in.

### Kaynaklar
- [Optimize your build speed](https://developer.android.com/build/optimize-your-build)
- [GitHub Actions for Android](https://github.com/android/nowinandroid/tree/main/.github/workflows)
- [Detekt](https://detekt.dev/)

### Pratik
1. Mevcut projende build time'ı `--profile` ile ölç.
2. Configuration cache aç, fark gör.
3. KAPT kullanan annotation processor'ları KSP'ye çevir.
4. GitHub Actions ile basit CI kur (PR'da build + test).
5. Detekt + ktlint + lint'i pre-commit hook ile çalıştır.

---

## Hafta 18 — İleri Compose ve Optimizasyon

### Konular
- **Custom Layout:** `Layout(measurePolicy)` ile sıfırdan UI yapısı.
- **Modifier.Node API:** Compose 1.6+ — daha performanslı custom modifier.
- **`SubcomposeLayout`** — bağımlı ölçüm ihtiyacı (Scaffold, BoxWithConstraints).
- **`LookaheadLayout`** — animasyonlu layout transition.
- **Compose'da performans avları:**
  - `@Stable`/`@Immutable` rastgele kullanma — yanlışsa bug üretir.
  - `derivedStateOf` ne zaman? (Çok tetiklenen state'ten az değişen state türetirken.)
  - Compose compiler metrics/reports — hangi composable skippable değil?
- **Compose Multiplatform:**
  - iOS, desktop, web target'ları.
  - `expect`/`actual` mekanizması.

### Kaynaklar
- [Custom layouts](https://developer.android.com/develop/ui/compose/layouts/custom)
- [Modifier.Node](https://developer.android.com/develop/ui/compose/custom-modifiers)
- [Compose Multiplatform](https://www.jetbrains.com/compose-multiplatform/)

### Pratik
Custom layout yaz: Bir "FlowRow" implementasyonu (item'ları yatay sıralayıp taşınca alta kaydıran). `Layout` API ile.

> **🎯 Faz 4 Çıktısı:** Senior seviyesindesin. Multi-module projeyi kurabiliyorsun. Build performansını optimize ediyorsun. CI/CD pipeline'ın var. Compose'da custom layout yazabiliyorsun.

---

# 🌟 FAZ 5 — SENIOR+ (Hafta 19-22)

## Hafta 19 — Mobile System Design

> **Senior interview'larda bu konu kritik.** "Tasarla bana bir Instagram feed" gibi sorular gelir.

### Konular
- **Yaygın senaryolar:**
  - Infinite scrolling feed (Pagination 3 + caching).
  - Real-time chat (WebSocket, FCM, message queue).
  - Image-heavy app (Coil/Glide, memory management, disk cache).
  - Offline-first uygulama (Room + WorkManager sync).
  - Multi-tenant uygulama (kullanıcıya göre branding).
- **Tasarım soruları için framework:**
  1. **Functional requirements:** Ne yapacak?
  2. **Non-functional:** Latency, throughput, offline, battery, data usage.
  3. **High-level architecture:** Hangi katmanlar?
  4. **Data model:** Entity'ler, ilişkiler.
  5. **API design:** Endpoint'ler, pagination stratejisi.
  6. **Caching stratejisi:** Memory, disk, network.
  7. **Sync stratejisi:** Pull, push, polling.
  8. **Edge cases:** Network kaybı, low memory, low battery.
  9. **Security:** Auth, encryption.
  10. **Monitoring:** Crashlytics, Analytics, custom metrics.

### Kaynaklar
- ["System Design Interview" - Alex Xu](https://www.amazon.com/dp/B08CMF2CQF) (cilt 1 ve 2, mobile bölümleri)
- [Exponent - Mobile System Design](https://www.tryexponent.com/courses/system-design-mobile)
- [Now in Android - Architecture](https://github.com/android/nowinandroid/blob/main/docs/ArchitectureLearningJourney.md)

### Pratik
Kendine 5 senaryo yaz, her birinin tasarımını çiz (whiteboard veya Excalidraw):
1. Twitter benzeri timeline.
2. WhatsApp benzeri messaging.
3. Spotify benzeri streaming.
4. Uber benzeri location-based matching.
5. Instagram benzeri photo upload + feed.

---

## Hafta 20 — Kotlin Multiplatform (KMP)

### Konular
- **KMP nedir, neden?** Code sharing — business logic, network, persistence.
- **`expect`/`actual`** mekanizması.
- **Common code yazma:**
  - `commonMain`, `androidMain`, `iosMain`.
  - Pure Kotlin business logic.
- **KMP ekosistemi:**
  - **Ktor Client** — multiplatform HTTP.
  - **SQLDelight** — multiplatform SQL.
  - **kotlinx.coroutines** — multiplatform.
  - **kotlinx.serialization** — multiplatform.
  - **Koin** — multiplatform DI (Hilt sadece Android).
- **Compose Multiplatform** — UI sharing (deneysel ama hızla olgunlaşıyor).
- **iOS interop:** Swift'ten Kotlin çağırma. KMM Plugin.
- **Sınırlamalar:** Reflection (sınırlı), Java library'leri (yok).

### Kaynaklar
- [Kotlin Multiplatform docs](https://kotlinlang.org/docs/multiplatform.html)
- [Touchlab KMP samples](https://github.com/touchlab/KaMPKit)
- [JetBrains KMP samples](https://github.com/JetBrains/compose-multiplatform-template)

### Pratik
Bir mini KMP projesi: Hava durumu uygulaması. `commonMain` içinde Repository + Network + Models. `androidMain` Compose UI. (iOS opsiyonel — Mac yoksa atlanabilir.)

---

## Hafta 21 — Senior Pratikleri ve Kod Review

### Konular
- **Code Review felsefesi:**
  - Style/syntax değil, mimari ve niyet üzerinde durmak.
  - "Bu kod 6 ay sonra başkası tarafından bakıldığında anlaşılır mı?"
  - "Bu test ne dokümanlıyor?"
- **Yaygın anti-pattern'ler:**
  - God Object (her şeyi yapan ViewModel).
  - Service Locator yerine Singleton DI.
  - Boolean flag patlaması (sealed class kullan).
  - Nested callback (suspend/Flow kullan).
  - String'lerle navigation (type-safe route).
  - `!!` operatörü (Kotlin'in en kötü düşmanı).
- **Refactoring:**
  - "Refactor → Run tests → Commit" döngüsü.
  - Martin Fowler refactor kataloğu (Extract Method, Move Method, Introduce Parameter Object).
- **Documentation:**
  - KDoc syntax (`@param`, `@return`, `@throws`, `@sample`).
  - Public API'lerde `@VisibleForTesting`.
  - Architecture Decision Records (ADR).
- **Mentorship pratikleri:** Junior'a açıklarken kullanılan dil.

### Kaynaklar
- ["Clean Code" - Robert Martin](https://www.amazon.com/dp/0132350882)
- ["Refactoring" - Martin Fowler](https://www.amazon.com/dp/0134757599)
- ["A Philosophy of Software Design" - John Ousterhout](https://www.amazon.com/dp/1732102201) — kısa ama altın
- [Effective Kotlin - Marcin Moskala](https://kt.academy/book/effectivekotlin)

### Pratik
GitHub'da popüler bir Android projesinin (örn. NowInAndroid) PR'larını incele. Code review yorumlarına bak — senior'lar neye dikkat ediyor?

---

## Hafta 22 — Bonus: Edge Konular ve Final Proje

### Edge Konular (Hızlı Tarama)
- **Hilt'in Dagger ile farkı:** Hilt = Android-aware Dagger preset.
- **Anvil:** Square'ın Dagger plugin'i (Hilt yerine).
- **Detekt rules custom:** Kendi static analysis rule'ını yazma.
- **Lint custom rule:** Architecture enforcement (örn. "feature module data layer'a direkt erişmesin").
- **Macrobenchmark startup tracking:** Cold/warm/hot startup farkları.
- **Compose Hot Reload:** Live Edit, Compose Preview ekosistemi.
- **Layout Inspector remoting:** Production'da debug.
- **Firebase ekosistemi:** Crashlytics, Performance Monitoring, Remote Config, A/B testing.

### Final Proje
22 haftanın bilgisini birleştiren bir uygulama yaz:
- **Multi-module** mimarisi (en az 6 modül).
- **MVVM + Clean Architecture** (UI → domain → data).
- **Hilt** ile DI.
- **Compose** ile UI.
- **Type-safe navigation**.
- **Coroutines + Flow + StateFlow**.
- **Retrofit + OkHttp** (interceptor, auth, cache).
- **Room + DataStore** (offline-first).
- **WorkManager** (background sync).
- **Unit test + UI test + screenshot test** (en az %70 coverage).
- **R8** ile minify.
- **Baseline Profile**.
- **Detekt + ktlint** entegrasyonu.
- **GitHub Actions** CI.
- **README** + **ADR**'ler.

GitHub'a push'la — bu senin **portföy projen**.

> **🎯 Faz 5 Çıktısı:** System design soruları cevaplayabiliyorsun. KMP'ye giriş yaptın. Code review yapabiliyorsun. Final projen var.

---

# 📋 KAYNAK ÖZETI

## Mutlaka Takip Et
| Kaynak | Tip | Niçin |
|--------|-----|-------|
| [Now in Android](https://github.com/android/nowinandroid) | Proje | Production-grade örnek. Saatlerce inceleyebilirsin. |
| [Android Developers](https://developer.android.com) | Resmi doc | İlk başvuru kaynağı. |
| [Kotlin Docs](https://kotlinlang.org/docs/) | Resmi doc | Dil seviyesi sorular için. |
| [r/androiddev](https://reddit.com/r/androiddev) | Topluluk | Haftalık takip et. |

## YouTube Kanalları (Türkçe ve İngilizce)
- **Philipp Lackner** — pratik tutorial (İngilizce, çok iyi).
- **Code with Cal** — modern best practices.
- **Stevdza-San** — KMP tutorials.
- **Kotlin Conf** — yıllık konferans, kayıtlar muazzam.
- **Android Developers** (resmi) — feature deep dive'ları.

## Bloglar
- [Manuel Vivo](https://manuelvivo.dev/) — Android team'den, Flow/Coroutines uzmanı.
- [Jorge Castillo](https://newsletter.jorgecastillo.dev/) — Compose internals.
- [Kt Academy](https://kt.academy/articles) — Marcin Moskala, derinlemesine.
- [ProAndroidDev (Medium)](https://proandroiddev.com/) — kalite değişken ama iyi makaleler var.
- [Android Weekly](https://androidweekly.net/) — haftalık newsletter.

## Kitaplar
- **Effective Kotlin** - Marcin Moskala (Kotlin best practices)
- **Clean Code** - Robert Martin (klasik)
- **Refactoring** - Martin Fowler (klasik)
- **A Philosophy of Software Design** - John Ousterhout (kısa, altın değerinde)
- **System Design Interview Vol 1 & 2** - Alex Xu (interview hazırlığı)

## Topluluklar
- **Kotlin Slack** — `slack.kotlinlang.org` (resmi).
- **Android Türkiye Discord/Telegram**.
- **Android Dev Backstage** podcast.

---

# 💡 GENEL TAVSİYELER

## "Senior" Olmanın Sırrı
1. **AI'a güvenme, doğrula.** Her AI çıktısını "neden böyle?" sorusuyla kontrol et.
2. **Source code oku.** Kullandığın kütüphanelerin (OkHttp, Coroutines, Compose) source'una bak.
3. **Açıklama yap.** Yapamadığın bir şeyi açıklayamıyorsan henüz öğrenmemişsindir. Junior'a anlatabiliyorsan biliyorsundur.
4. **Trade-off düşün.** "En iyi çözüm" yoktur, sadece bağlama göre uygun çözüm vardır.
5. **Engineering > Coding.** Senior, kod yazan değil, doğru kararı veren kişidir.

## Spaced Repetition Şablonu
| Aralık | Ne yap |
|--------|--------|
| 1 gün sonra | Notlarını gözden geçir. |
| 1 hafta sonra | Konuyla ilgili 3 soru cevapla. |
| 1 ay sonra | Konuyu birine açıklar gibi yaz. |
| 3 ay sonra | Mini bir proje yap. |

## Anti-Pattern: Tutorial Hell'den Kaçın
- Sürekli izleyip kod yazmamak → kalıcı öğrenme yok.
- Her yeni kütüphaneye atlamak → temel sağlamlaşmaz.
- 2 saat takılıp ilerlememek → bir gün ara ver, dön.
- Mükemmeliyetçilik → "production-ready" obsesyonu junior'ın işidir.

## Saatler ve Beklentiler
- **Hafta 1-2 (Faz 0):** "Bu konular Android dışı, niye?" diyebilirsin. Sabret — her şeyin temeli.
- **Hafta 3-6 (Faz 1):** Ezberlemediğin bilgileri ezberlemekle uğraşma — tekrarla pekiştir.
- **Hafta 7-10 (Faz 2):** İlk gerçek production-grade kodun bu fazda çıkar.
- **Hafta 11-14 (Faz 3):** Burada bırakanlar mid-level kalır. Senin avantajın test ve perf bilgini içselleştirmek.
- **Hafta 15-18 (Faz 4):** Modularization en zor konsept gibi görünür ama 2 hafta sonra "neden hep böyle yazılmamış?" diyeceksin.
- **Hafta 19-22 (Faz 5):** Senior+ — interview'lara hazırsın.

---

# 🚦 AŞAMA AŞAMA HEDEFLER

## Faz 0 sonu — "Build sistemi artık karanlık değil."
- [ ] `build.gradle.kts`'in her satırını açıklayabilirim.
- [ ] Convention plugin yazabilirim.
- [ ] Kotlin'in JVM'e nasıl compile olduğunu anlatabilirim.

## Faz 1 sonu — "Intermediate seviyesindeyim."
- [ ] MVVM + Hilt + Coroutines + Flow ile basit feature yazabilirim.
- [ ] StateFlow vs SharedFlow farkını anlatabilirim.
- [ ] Suspend fonksiyon bytecode'ta nasıl görünür biliyorum.

## Faz 2 sonu — "Production-ready kod yazıyorum."
- [ ] Offline-first mimari kurabilirim.
- [ ] Compose ile akıcı, performanslı UI yazıyorum.
- [ ] OkHttp interceptor + auth refresh implementasyonu yapabilirim.

## Faz 3 sonu — "Pre-senior seviyedeyim."
- [ ] Test piramidini doğru kuruyorum, %70+ coverage'a sahibim.
- [ ] Performance profiling yapıp bottleneck tespit ediyorum.
- [ ] Güvenlik en iyi pratiklerini biliyorum.

## Faz 4 sonu — "Senior seviyedeyim."
- [ ] Multi-module mimari kurabiliyorum, build time optimize ediyorum.
- [ ] CI/CD pipeline kurabilirim.
- [ ] Compose custom layout yazabilirim.

## Faz 5 sonu — "Senior+ seviyedeyim."
- [ ] System design sorularına hâkimim.
- [ ] KMP'ye giriş yapmışım.
- [ ] Code review yapabilir, junior'a mentor'luk edebilirim.
- [ ] Portföy projem hazır.

---

# 🎓 SON SÖZ

Bu plan **22 hafta** ama gerçekçi olmak gerekirse 6-8 ay sürebilir. Acele etme — senior olmak hız değil **derinlik** meselesidir. AI kod yazıyor, senin işin ise **doğru kodu** yazdırmak ve **niye doğru olduğunu** bilmek.

Her hafta sonunda kendine sor:
- Bu hafta gerçekten neyi anladım?
- Hangi konuyu hâlâ "ezbere" biliyorum?
- Önümüzdeki haftaya hangi soruyla giriyorum?

**Bilgi unutulur, anlayış kalır.** İyi şanslar. 🚀
