# Android Kotlin Senior Yol Haritası (Google Standartları)

> **Hedef:** Now in Android’i sıfırdan yazabilen, Google senior bar’ını geçen, AI’ın ürettiği kodu mimari ve performans açısından eleştirebilen Android engineer olmak.
> 
> **Süre:** 32 hafta (~7-8 ay). Günde 2 saat hafta içi, 3-4 saat hafta sonu. Toplam ~500-600 saat.
> 
> **Felsefe:** Her konuyu **3 katmanda** öğren — *Ne (sözdizimi), Nasıl (kullanım), Neden (iç işleyiş, trade-off, tasarım kararı)*. Senior’ı diğerlerinden ayıran 3. katmandır. AI sana 1 ve 2’yi verir; 3’ü sen kapatacaksın.
> 
> **Ön koşul:** Kotlin temelleri (sınıf, fun, when, null safety, collection, lambda), bir XML+Activity uygulaması yazmış olmak.
> 
> **Bu plan neden 22 değil 32 hafta:** Google senior bar’ı, “Now in Android’i tamamen yazabilmek” + observability + Firebase + accessibility + i18n + Play Store + ileri Compose + ileri Coroutines + system design’ı kapsıyor. 22 hafta sadece “iyi mid-level” üretir. 32 hafta gerçekçi senior üretir.

-----

## 📚 Plan Yapısı

|Faz      |Hafta|Hedef Seviye         |Odak                                                                 |
|---------|-----|---------------------|---------------------------------------------------------------------|
|**Faz 0**|1-2  |Temel Sağlamlama     |JVM/ART, Kotlin Internals, Gradle, Build Sistemi                     |
|**Faz 1**|3-7  |Mimari Temeli        |Clean Architecture, MVVM/MVI, Coroutines, Flow, Hilt                 |
|**Faz 2**|8-12 |Production Katmanları|Compose, Networking, Persistence, Pagination, Navigation             |
|**Faz 3**|13-17|Kalite & Güvenlik    |Test stratejisi, Performans, Güvenlik, Observability, Background Work|
|**Faz 4**|18-22|Senior Mühendislik   |Modularization, Build Optimization, CI/CD, App Size, Release         |
|**Faz 5**|23-27|İleri Konular        |İleri Compose, İleri Coroutines, Firebase, Accessibility, i18n       |
|**Faz 6**|28-32|Senior+ Tamamlayıcı  |System Design, KMP, Now in Android Klonu, Mentorship                 |

-----

## 🎯 Haftalık Çalışma Şablonu

|Gün|Süre    |Aktivite                                                                                              |
|---|--------|------------------------------------------------------------------------------------------------------|
|Pzt|90 dk   |Yeni konu — **resmi dokümandan** oku, kendi cümlelerinle not al.                                      |
|Sal|90 dk   |**İç işleyiş** — source code, KEEP proposal’lar, Google blog yazıları. “Neden böyle tasarlanmış?” sor.|
|Çar|90 dk   |**Kendi başına** kod yaz. AI’a sormadan dene, hataları dokümente et.                                  |
|Per|90 dk   |AI’ın kodunu **satır satır** review et. Her satır için “neden bu?” sor. Alternatif yaz.               |
|Cum|60 dk   |GitHub’da gerçek kullanım: NowInAndroid, Tivi, Pokedex, Compose Samples.                              |
|Cmt|3-4 saat|Hafta projesi — konuyu mini bir uygulamada uygula.                                                    |
|Paz|60 dk   |**Spaced repetition** — geçmiş hafta(lar)ı gözden geçir, sorular cevapla.                             |


> **Pazar tekrarı opsiyonel değil.** Senior olmanın sırrı yeni konu görmek değil, eskileri unutmamaktır. 1 hafta, 1 ay, 3 ay tekrar döngüsünü uygula.

-----

# 📘 FAZ 0 — TEMEL SAĞLAMLAMA (Hafta 1-2)

> **Neden buradan?** Senior, build sistemini ve runtime’ı kara kutu olarak bırakamaz. Bu fazı atlayanlar 5 yıl sonra bile “Gradle bana sinir bozucu geliyor” der.

## Hafta 1 — JVM, ART, Kotlin Internals

### Pzt-Sal: JVM ve Android Runtime

**Konular:**

- JVM mimarisi: class loading, bytecode, JIT compilation, escape analysis.
- ART (Android Runtime): AOT + JIT hibrit, profile-guided optimization.
- Dalvik → ART geçişi neden? Register-based VM vs stack-based.
- Garbage Collection: Generational GC, ART’ta concurrent copying GC, GC pause süreleri.
- DEX format: `.class` → `.dex` → `.odex` → `.oat`.
- Kotlin → bytecode: `kotlinc -Xshow-bytecode`, IntelliJ “Show Kotlin Bytecode” → “Decompile to Java”.

**Kaynaklar:**

- [Android Runtime overview](https://source.android.com/docs/core/runtime)
- [ART and Dalvik](https://source.android.com/docs/core/runtime/dalvik)
- [Java Performance - Scott Oaks](https://www.oreilly.com/library/view/java-performance-2nd/9781492056102/) (bölüm 1-3)

**Pratik:** `data class User(val id: Int, val name: String)` yaz. Decompile et. `equals/hashCode/copy/componentN/toString` nasıl üretilmiş incele. `data class` yerine plain `class` yazsan ne olurdu?

### Çar-Per: Kotlin Internals (Senior’ı Mid’den Ayıran Konular)

**Konular:**

- `inline`, `noinline`, `crossinline` — lambda allocation maliyeti, ne zaman gerçekten gerekli?
- `reified` type parameters — neden sadece `inline` ile çalışır? Type erasure ile ilişkisi.
- `value class` (eski `inline class`) — primitive boxing’i nasıl önler? Hangi kısıtlamalar var?
- `sealed class` vs `sealed interface` (Kotlin 1.5+) — hiyerarşi tasarımı.
- `object` vs `companion object` — Java tarafından nasıl görünür? `@JvmStatic`, `@JvmField`.
- Delegated properties: `by lazy`, `by Delegates.observable`, `by viewModels()` — internals.
- Kotlin Contracts API: derleyiciye smart cast ipucu verme.
- `@JvmInline value class` ile type-safe ID pattern.
- Coroutine’ler için temel: `Continuation`, `suspend` keyword’ünün anlamı.
- K2 compiler — Kotlin 2.0 ile gelen değişiklikler.

**Kaynaklar:**

- [Kotlin Inline Functions](https://kotlinlang.org/docs/inline-functions.html)
- [Effective Kotlin - Marcin Moskala](https://kt.academy/book/effectivekotlin) (Item 46-49)
- [KEEP proposals](https://github.com/Kotlin/KEEP) — dilin geleceğine bak

**Pratik:**

1. `inline fun <reified T> Bundle.getEnum(key: String): T` extension’ı yaz.
1. `value class UserId(val value: String)` ve `value class OrderId(val value: String)` yaz — yanlış ID’leri karıştırmayı derleme zamanında engellesin.
1. `sealed interface UiState<out T>` ile `Loading/Success/Error` modelleyin.

### Cum-Cmt: Mini Proje

Mevcut bir projendeki ilk 30 dosyayı analiz et:

- Hangi `lateinit var`’lar `by lazy` olabilir?
- Primitive ID’ler `value class` ile sarılmaya aday mı?
- `inline reified` ile sadeleşecek util fonksiyonlar var mı?
- `sealed class` yerine `sealed interface` kullanılabilir mi?

Listele, refactor et, decompiled bytecode’a bak, fark gör.

### Paz: Tekrar

1. Inline fonksiyonun bytecode’a etkisini 1 paragrafta anlat.
1. ART’ta cold start sırasında ne olur?
1. `value class`’ın boxing yaptığı 3 senaryoyu söyle.

-----

## Hafta 2 — Gradle, Build Sistemi, Sürüm Yönetimi

> **Çoğu Android developer’ın eksiği:** Gradle güncel tutulmaz, AGP/Kotlin/KSP/Compose Compiler uyumluluk matrisi bilinmez, “build çalışmıyor” deyince sıfırdan plugin sürümü çevrilir. Senior bunu yapmaz.

### Pzt: Gradle Mental Modeli

**Konular:**

- **3 aşama:** Initialization → Configuration → Execution. Hangisi ne zaman çalışır?
- `settings.gradle.kts`: `pluginManagement`, `dependencyResolutionManagement`, `enableFeaturePreview`.
- `build.gradle.kts` (Kotlin DSL) — neden Groovy’i bırakmalı? Tip güvenliği, IDE desteği.
- Plugin’ler: `com.android.application` arka planda ne yapıyor? AGP’nin task graph’a etkisi.
- `dependencies` block: `implementation` vs `api` vs `compileOnly` vs `runtimeOnly` vs `testImplementation` vs `androidTestImplementation` vs `debugImplementation`.
- `buildTypes` (debug/release/staging) ve `productFlavors` (free/paid, dev/prod).
- `BuildConfig` üretimi — `buildConfigField`, `manifestPlaceholders`, `resValue`.

**Kaynaklar:**

- [Gradle Build Lifecycle](https://docs.gradle.org/current/userguide/build_lifecycle.html)
- [Configure your build (Android)](https://developer.android.com/build)
- [AGP release notes](https://developer.android.com/build/releases/gradle-plugin) — düzenli takip et

### Sal: Version Catalog ve Convention Plugins

**Konular:**

- `libs.versions.toml`: `[versions]`, `[libraries]`, `[bundles]`, `[plugins]`.
- `buildSrc` neden artık önerilmiyor? Composite builds (`build-logic`).
- Convention plugin yazma — tüm modüllerde tek satırla aynı config.
- Type-safe project accessors: `projects.core.data` syntax’ı.
- `apply plugin` vs `plugins { id(...) }` block.

**Pratik:** Boş bir proje aç. `build-logic` composite build kur. Şu convention plugin’leri yaz:

- `myapp.android.application`
- `myapp.android.library`
- `myapp.android.library.compose`
- `myapp.android.feature`
- `myapp.android.hilt`
- `myapp.kotlin.multiplatform` (ileride lazım)

NowInAndroid’in `build-logic`’ini referans al ama körü körüne kopyalama, satır satır anla.

### Çar: Build Performance ve Profiling

**Konular:**

- Configuration Cache, Build Cache (local + remote), Gradle Daemon.
- `--profile` flag → HTML build report.
- `--scan` (Develocity Build Scan) → cloud-based analiz.
- KSP > KAPT — neden? Annotation processor performansı (3-5x hızlı).
- `kotlin.incremental=true`, `kotlin.compiler.execution.strategy=in-process`.
- JVM tuning: `org.gradle.jvmargs=-Xmx4g -XX:+UseG1GC -XX:MaxMetaspaceSize=1g`.
- Parallel execution: `org.gradle.parallel=true`, `org.gradle.workers.max=8`.
- Module dependency graph — `dependencyAnalysis` plugin ile gereksiz dependency tespiti.

**Pratik:** NowInAndroid’i clone’la, `./gradlew assembleDebug --profile --scan` çalıştır. Hangi task en uzun? Configuration cache’i aç/kapat, fark ölç.

### Per: Sürüm Yönetimi ve Güncelleme Stratejisi

**Konular:**

- **AGP / Gradle / Kotlin / KSP / Compose Compiler uyumluluk matrisi** — hangisi neye bağlı?
  - AGP X gerektirir: Gradle Y, Java Z.
  - Kotlin X gerektirir: KSP Y, Compose Compiler Z.
- `gradle/wrapper/gradle-wrapper.properties` — wrapper upgrade.
- `./gradlew wrapper --gradle-version=X.Y.Z --distribution-type=bin`.
- AGP Upgrade Assistant (Android Studio).
- **Renovate** vs **Dependabot** — automated dependency updates.
- Semantic versioning (semver): MAJOR.MINOR.PATCH. Breaking change’leri ne zaman beklemeli?
- BoM (Bill of Materials): `androidx.compose:compose-bom`, `firebase-bom` — neden kullanmalı?
- `gradle-versions-plugin` — outdated dependency raporu.

**Kaynaklar:**

- [AGP/Gradle compatibility](https://developer.android.com/build/releases/gradle-plugin#updating-gradle)
- [Kotlin/KSP compatibility](https://github.com/google/ksp/releases)
- [Renovate Android setup](https://docs.renovatebot.com/modules/manager/gradle/)

**Pratik:** Mevcut bir projende `renovate.json` yaz. PR’ları auto-merge için kurallar belirle (patch otomatik, minor manuel, major asla otomatik değil).

### Cum-Cmt: K2 Compiler ve Modern Build Setup

**Konular:**

- Kotlin 2.0 K2 compiler — frontend rewrite, derleme hızı %100’e kadar artış.
- K2 ile Compose Compiler artık `org.jetbrains.kotlin.plugin.compose` plugin’i (eski `kotlinCompilerExtensionVersion` deprecated).
- `kotlin.code.style=official`.
- Java toolchain: `kotlin { jvmToolchain(17) }`.
- Android Studio version channels: Stable, Beta, Canary — ne zaman hangisi?

**Mini Proje:** Boş bir proje aç. En güncel AGP + Gradle + Kotlin + KSP + Compose Compiler ile kur. Convention plugin’lerle 5 modüllü iskelet hazırla. Renovate config ekle. Bu iskelet sonraki haftalar için “lab” olacak.

### Paz: Tekrar

1. `implementation` ile `api`’yi 1 paragrafta açıkla, hangi durumda hangisi?
1. AGP 8.X için minimum Gradle versiyonu nasıl bulunur?
1. KAPT’tan KSP’ye geçişin maliyeti ve kazanımı nedir?

> **🎯 Faz 0 Çıktısı:** Build sistemi senin için artık opak değil. Herhangi bir Android projesinin `build.gradle.kts`, `settings.gradle.kts`, `libs.versions.toml`, `build-logic/` dizinini açıp her satırı açıklayabiliyorsun. Renovate kurabiliyorsun. AGP/Kotlin/KSP/Compose Compiler matrisini biliyorsun. Mid-level developer’ların %90’ı buradadır.

-----

# 📗 FAZ 1 — MİMARİ TEMELİ (Hafta 3-7)

## Hafta 3 — Clean Architecture, MVVM, MVI

> **Mevcut planda eksikti.** Senior Android dev MVVM “izleyicisi” değil, **mimari kararı veren** kişidir.

### Pzt-Sal: Clean Architecture Katmanları

**Konular:**

- **3 katman:** Presentation (UI + ViewModel), Domain (UseCase + Model), Data (Repository + DataSource).
- Bağımlılık yönü: dış katmanlar içe bağlanır, içi dışı bilmez.
- **Domain layer pure Kotlin** — Android’e bağımlı değil. JVM’de testlenebilir.
- **Repository pattern:** Local + Remote DataSource’lardan hangisinin kullanılacağına karar veren katman.
- **UseCase (Interactor):** Tek bir iş eylemini temsil eder. `operator fun invoke()` ile çağrılır.
- UseCase ne zaman gerekli, ne zaman aşırı? (Sadece basit CRUD → ViewModel’den repository çağrısı yeterli olabilir.)
- **DTO (Data Transfer Object) → Domain Model → UI Model** mapping zinciri. Neden 3 farklı model? Network değişikliği UI’ı kırmasın.

**Kaynaklar:**

- [Guide to App Architecture (resmi)](https://developer.android.com/topic/architecture)
- [Clean Architecture - Robert Martin](https://www.amazon.com/dp/0134494164)
- [NowInAndroid Architecture](https://github.com/android/nowinandroid/blob/main/docs/ArchitectureLearningJourney.md) — **MUTLAKA OKU**

### Çar-Per: MVVM vs MVI ve UI State Modeling

**Konular:**

- **MVVM:** ViewModel state’i exposes eder, UI observer.
- **MVI (Model-View-Intent):** Tek yönlü veri akışı. State, Event/Intent, Effect/SideEffect.
- **`UiState` modeling:**
  
  ```
  sealed interface NewsUiState {
    data object Loading : NewsUiState
    data class Success(val news: List<News>) : NewsUiState
    data class Error(val throwable: Throwable) : NewsUiState
  }
  ```
- **State + Event ayrımı:** State (sürekli) vs Event (one-shot, snackbar/navigation).
- **`Result<T>` vs sealed class** — Kotlin’in `Result` neden direkt UI state olarak kullanılmaz? Generic variance, exception requirement.
- **Error handling stratejisi:** Network error, validation error, business error — domain layer’da Throwable subtype’ları.
- **Loading state’in 3 türü:** Initial loading, refresh, pagination loading.

**Kaynaklar:**

- [UI layer guide](https://developer.android.com/topic/architecture/ui-layer)
- [State holders](https://developer.android.com/topic/architecture/ui-layer/stateholders)

**Pratik:** Bir login feature’ı için 3 farklı versiyon yaz:

1. Saf MVVM — `LiveData<LoginState>`.
1. MVVM + Flow — `StateFlow<LoginState>`.
1. MVI — `StateFlow<UiState>` + `Channel<SideEffect>` (navigation, toast).

Hangisi hangi senaryoda iyi? Notuna yaz.

### Cum-Cmt: Mini Proje

**Twitter-lite feed:**

- 3 katman (presentation/domain/data).
- DTO → Domain → UI Model mapping.
- `UseCase`’ler: `GetTimelineUseCase`, `LikeTweetUseCase`.
- `UiState` sealed interface.
- ViewModel sadece UseCase çağırır, business logic UseCase’lerde.

### Paz: Tekrar

1. UseCase ne zaman over-engineering, ne zaman gereklilik?
1. `Loading` state’i nasıl modellersin — boolean mi sealed class mi?
1. One-shot event neden StateFlow ile çözülmemeli?

-----

## Hafta 4 — Coroutines (Kısım 1: Mekanizma ve Yapı)

> **Hedef: Coroutine “canavarı” olmak.** Bu konu 2 hafta. Yüzeysel öğrenirsen production’da bug üretirsin.

### Pzt: Suspend Mekanizması (CPS)

**Konular:**

- **Continuation-Passing Style (CPS):** `suspend fun foo(): Int` bytecode’da `fun foo(continuation: Continuation<Int>): Any?` olur.
- State machine: suspend fonksiyonun decompile edilmiş hali. `label` field, `when (label)` switch.
- `COROUTINE_SUSPENDED` sentinel value.
- `suspend` keyword’ünün anlamı: “Bu fonksiyon askıya alınabilir, threading sözleşmesi yok.”
- Suspend fonksiyon thread block etmez, **kendini** bloklar (continuation’ı sakla, thread’i serbest bırak).

**Kaynaklar:**

- [Kotlin Coroutines design document (KEEP)](https://github.com/Kotlin/KEEP/blob/master/proposals/coroutines.md) — uzun ama altın değerinde
- [Roman Elizarov - Deep dive into Coroutines](https://www.youtube.com/watch?v=YrrUCSi72E8)

**Pratik:** Basit bir suspend fun yaz, IntelliJ’de bytecode’a çevir, decompile et. State machine’i göz ile takip et.

### Sal: CoroutineContext ve Dispatcher’lar

**Konular:**

- `CoroutineContext` = `Job` + `Dispatcher` + `CoroutineExceptionHandler` + `CoroutineName`. `+` operator overload.
- **Dispatchers:**
  - `Main` vs `Main.immediate` — context check optimization.
  - `IO` — varsayılan 64 thread (veya core sayısı, hangisi büyükse).
  - `Default` — CPU-bound, pool boyutu = CPU çekirdek sayısı.
  - `Unconfined` — test dışında kullanma.
- `withContext(Dispatchers.IO)` — context switch, yeni coroutine değil.
- `Dispatchers.IO.limitedParallelism(4)` — IO içinde alt-dispatcher (DB serialize için).

**Pratik:** 100 paralel HTTP isteği yapan kod yaz. Önce `Dispatchers.IO`, sonra `IO.limitedParallelism(10)`. Throughput farkı?

### Çar: Structured Concurrency

**Konular:**

- Parent-child Job hiyerarşisi — child fail olursa parent ne yapar?
- `coroutineScope { }` — scope içindeki tüm child’lar bitince döner. Bir tanesi fail olursa hepsi cancel.
- `supervisorScope { }` — child fail parent’ı etkilemez.
- `viewModelScope`, `lifecycleScope`, custom scope.
- `Job` lifecycle: New → Active → Completing → Completed (veya Cancelling → Cancelled).
- `SupervisorJob()` vs `Job()`.
- `GlobalScope` — neden kullanma!

**Kaynaklar:**

- [Roman Elizarov - Structured Concurrency](https://elizarov.medium.com/structured-concurrency-722d765aa952)
- [Coroutines on Android (3-part series)](https://medium.com/androiddevelopers/coroutines-on-android-part-i-getting-the-background-3e0e54d20bb)

### Per: Cancellation Cooperative

**Konular:**

- Cancellation **cooperative**’tir. Coroutine kendi kontrol etmeli.
- `yield()`, `ensureActive()`, `isActive` — CPU-bound loop’larda neden gerekli?
- Suspend fonksiyonların çoğu `CancellationException` fırlatır cancellation’da.
- `CancellationException` neden özel? Niye `try/catch (Throwable)` kötü fikir?
- `withContext(NonCancellable) { }` — cleanup için (DB rollback, log gönderme).
- Resource cleanup: `try/finally`, `use { }`.

**Pratik:** CPU-bound bir hesaplama (Fibonacci 50, sıfırdan). Cancel et — durmuyor. `ensureActive()` ekle, çöz.

### Cum: Exception Handling

**Konular:**

- `launch` exception → uncaught, parent’a propagate, `CoroutineExceptionHandler` yakalar.
- `async` exception → `await()` çağrılana kadar saklar, sonra fırlatır (regular Job ise parent’a da bildirilir).
- `supervisorScope` içinde `async`’in farklı davranışı.
- `runCatching` vs `try/catch`.
- Multiple child failure — sadece ilki propagate, diğerleri suppressed.

**Pratik:** 5 paralel API çağrısı:

1. Hepsi başarılı olmalı, biri fail → hepsi cancel (`coroutineScope`).
1. Bir tanesi fail → diğerleri devam (`supervisorScope`).
1. Tüm sonuçları topla, fail olanları `Result.failure` ile döndür.

### Cmt-Paz: Mini Proje + Tekrar

“Parallel image downloader”: 50 image URL → 10 paralel download → progress callback → cancellable. Memory leak yok. Test yaz.

-----

## Hafta 5 — Coroutines (Kısım 2: İleri ve Channel/Select)

> **Mevcut planda yoktu.** Channel ve Select expression senior’ın araç kutusunda olmalı.

### Pzt-Sal: Channels

**Konular:**

- `Channel<T>` — coroutine’ler arası iletişim. Producer-consumer.
- Channel tipleri: `RENDEZVOUS` (default), `BUFFERED`, `UNLIMITED`, `CONFLATED`.
- `SendChannel<T>`, `ReceiveChannel<T>` — yön ayrımı.
- `produce { send() }`, `consumeEach { }`.
- `Channel.close()` — receiver `for` loop’u biter.
- `BroadcastChannel` deprecated → `SharedFlow` kullan.
- `actor { }` builder — state’e güvenli erişim için.

**Kaynaklar:**

- [Channels guide](https://kotlinlang.org/docs/channels.html)
- [Roman Elizarov - Cold flows, hot channels](https://elizarov.medium.com/cold-flows-hot-channels-d74769805f9)

### Çar: Select Expression

**Konular:**

- `select { }` — birden fazla suspend kaynağı arasında “ilk gelen” almak.
- `onReceive`, `onSend`, `onTimeout`, `onAwait`.
- Use case: race condition handling, timeout with multiple sources.

**Pratik:** İki API’den (cache + network) ilk gelen sonucu döndüren bir fonksiyon yaz.

### Per-Cum: Custom Suspend Functions ve Flow Builders

**Konular:**

- `suspendCoroutine { }` — callback API’yi suspend’a çevirme.
- `suspendCancellableCoroutine { }` — cancellation desteği.
- `callbackFlow { }` — callback API’yi Flow’a çevirme. `awaitClose { }` kritik.
- `channelFlow { }` — multi-emitter flow.
- Custom Flow operator: `fun <T> Flow<T>.myOperator() = flow { ... }`.

**Pratik:** Android `LocationManager`’ı (callback-based) `Flow<Location>`’a çeviren extension yaz. `awaitClose`’da `removeUpdates` çağır.

### Cmt-Paz: İleri Konular ve Tekrar

- Backpressure stratejileri: buffer, conflate, collectLatest.
- Cold flow vs Hot flow semantic farkı.
- Coroutine debugging: `-Dkotlinx.coroutines.debug` flag, IntelliJ Coroutine Debugger.

**Tekrar:**

1. `suspendCancellableCoroutine` ve `callbackFlow` arasındaki farkı bir senaryoyla anlat.
1. `Channel<T>` ne zaman SharedFlow’a tercih edilir?
1. Custom flow operator yazarken hangi tuzaklara dikkat?

-----

## Hafta 6 — Flow Derinlemesine

### Pzt-Sal: Cold Flow ve Operatörler

**Konular:**

- Cold flow: `flow { emit() }` — collector olmadan çalışmaz.
- Intermediate operators (lazy): `map`, `filter`, `transform`, `onEach`, `take`, `drop`.
- Terminal operators: `collect`, `toList`, `first`, `single`, `reduce`, `fold`.
- `flowOn(Dispatchers.IO)` — context preservation, **upstream**’i etkiler.
- `buffer()`, `conflate()`, `collectLatest()` — backpressure.
- `catch { }` — upstream exception (kendinden öncesi).
- `onCompletion { cause -> }` — finally semantic.
- `retry`, `retryWhen` — retry stratejileri.

**Kaynaklar:**

- [Kotlin Flow guide](https://kotlinlang.org/docs/flow.html)
- [Manuel Vivo - Things to know about Flows](https://manuelvivo.dev/things-to-know-flows-android)

### Çar-Per: StateFlow ve SharedFlow

**Konular:**

- **StateFlow:** Conflated, replay=1, equality-based distinct, son değeri tutar. UI state için.
- **SharedFlow:** Configurable replay/extraBufferCapacity/onBufferOverflow. Event/multi-collector için.
- `MutableStateFlow.value` thread-safe atomic mı? — Evet, ama `update { }` yan-etki için daha iyi.
- `MutableSharedFlow.tryEmit()` vs `emit()` — non-suspending vs suspending.
- **One-shot event problemi:** StateFlow neden uygun değil? `Channel.receiveAsFlow()`, `SharedFlow(replay = 0)` veya **state’e gömme** alternatifleri.
- **`stateIn(scope, SharingStarted.WhileSubscribed(5_000), initial)`** — neden 5000ms?
  - Configuration change sırasında collector 1-2 frame yok.
  - `Eagerly` — scope yaşadığı sürece aktif (gereksiz iş).
  - `Lazily` — ilk subscriber’da başlar, sonra hep aktif.
  - `WhileSubscribed(0)` — subscriber yokken hemen durur, gereksiz restart.
  - `WhileSubscribed(5000)` — 5sn buffer, config change emniyeti.
- `shareIn` vs `stateIn` farkı.

**Kaynaklar:**

- [Effective StateFlow.stateIn](https://medium.com/androiddevelopers/effective-statein-sharingstarted-strategies-bf1e22ce03cf)

### Cum-Cmt: Combine, Reactive Patterns ve Lifecycle

**Konular:**

- `combine`, `zip`, `merge`, `flatMapLatest`, `flatMapConcat`, `flatMapMerge`.
- `combine` — herhangi biri yeni değer verince yeniden compute.
- `flatMapLatest` — yeni emission gelince eski iç flow’u cancel.
- `debounce`, `sample`.
- `collectAsStateWithLifecycle()` — lifecycle-aware.
- `repeatOnLifecycle(Lifecycle.State.STARTED)` — Fragment’ta Flow toplama.

**Pratik:** Search feature:

- `MutableStateFlow<String>` query.
- `.debounce(300).distinctUntilChanged().filter { it.length >= 2 }`.
- `.flatMapLatest { api.search(it) }`.
- `.catch { emit(emptyList()) }`.
- `.stateIn(viewModelScope, WhileSubscribed(5000), emptyList())`.

User + settings combine ile derived state. Turbine ile test et.

### Paz: Tekrar

1. `flowOn(Dispatchers.IO)` zinciri içinde 2 kez çağrılırsa ne olur?
1. `StateFlow.collect`‘ten initial value’yu nasıl skip edersin?
1. `flatMapLatest` vs `flatMapConcat` race condition senaryosu yaz.

-----

## Hafta 7 — Dependency Injection: Hilt + Manuel DI

### Pzt-Sal: DI Felsefesi ve Manuel DI

**Konular:**

- DI neden var? Inversion of Control, testability, decoupling.
- Service Locator anti-pattern — niye kötü?
- **Manuel DI:** Application class’ında container yazma. Bunu yapabilmek = Hilt’i anlamak.
- Constructor injection vs field injection — neden ilki tercih?

**Pratik:** Bir mini app’te Hilt kullanmadan elinle DI yap. `AppContainer` class’ı, `lazy` ile instance’lar. Activity’den `(application as MyApp).container.userRepository` ile eriş. Boilerplate’i gör.

### Çar: Hilt Mekanizması

**Konular:**

- Code generation: Hilt = Dagger üzerine Android-aware preset.
- `@HiltAndroidApp` → Application’a code generation.
- `@AndroidEntryPoint` → Activity/Fragment/Service/View’a inject.
- `@Inject constructor` → primary injection.
- **Component hiyerarşisi:**
  - `SingletonComponent` (Application).
  - `ActivityRetainedComponent` (config change’e dayanır — ViewModel scope).
  - `ViewModelComponent`, `ActivityComponent`, `FragmentComponent`, `ViewComponent`, `ServiceComponent`.
- Scope: `@Singleton`, `@ActivityRetainedScoped`, `@ViewModelScoped`, `@ActivityScoped`.

### Per: Modüller, Qualifier’lar, Multi-binding

**Konular:**

- `@Module @InstallIn(SingletonComponent::class)`.
- `@Provides` vs `@Binds` — `@Binds` interface→implementation için, compile-time’da çözülür, daha verimli.
- `@Qualifier` ve `@Named` — aynı tipten 2 farklı instance.
  
  ```
  @Qualifier annotation class IoDispatcher
  @Provides @IoDispatcher fun provideIo() = Dispatchers.IO
  ```
- `@HiltViewModel` ve Compose `hiltViewModel()`.
- **Multi-binding:** `@IntoSet`, `@IntoMap` — plugin pattern.
- `EntryPoint` — Hilt-supported olmayan class’a manuel erişim.

**Kaynaklar:**

- [Hilt official guide](https://developer.android.com/training/dependency-injection/hilt-android)
- [Dagger documentation](https://dagger.dev/)
- [NowInAndroid Hilt setup](https://github.com/android/nowinandroid)

### Cum: Test’te Hilt

**Konular:**

- `@HiltAndroidTest`.
- `@TestInstallIn(replaces = [...], components = [SingletonComponent::class])` — modül replace.
- `HiltAndroidRule` test rule.
- `@BindValue` annotation — ad-hoc binding test’te.

### Cmt-Paz: Mini Proje + Tekrar

Login → Profile → Settings akışı:

- Manuel DI versiyonu yaz.
- Hilt’e çevir.
- Network module, database module, repository module.
- `@IoDispatcher`, `@MainDispatcher` qualifier’lar.
- `FakeAuthRepository` ile test inject.

**Tekrar:**

1. `@Provides` ve `@Binds` farkı? Performans nedeniyle hangisi tercih?
1. `ActivityRetainedComponent` neden var, `ActivityComponent`’le farkı?
1. `EntryPoint` nerede gerekli?

> **🎯 Faz 1 Çıktısı:** Clean architecture katmanlarını biliyorsun. UseCase ne zaman gerekli, ne zaman aşırı — kararını verebilirsin. Coroutines’in iç işleyişini, Channel/Select dahil, anlatabilirsin. Flow operatörlerini ve Hot/Cold flow semantiğini “neden öyle” mantığıyla biliyorsun. Hilt’i kara kutu olarak değil, kod üreten preset olarak görüyorsun.

-----

# 📕 FAZ 2 — PRODUCTION KATMANLARI (Hafta 8-12)

## Hafta 8 — Networking (Retrofit + OkHttp + Apollo/gRPC giriş)

### Pzt-Sal: OkHttp Internals

**Konular:**

- OkHttp interceptor zinciri — request/response pipeline.
- **Application interceptor** vs **Network interceptor** — fark, cache’e etkisi.
  - Application interceptor: redirect, retry öncesi.
  - Network interceptor: gerçek network’e en yakın, cache hit’leri görmez.
- Logging interceptor — production’da neden kapatmalı?
- Auth interceptor + `Authenticator` — token refresh mekanizması.
- Connection pool — varsayılan 5 connection, 5 dakika keep-alive.
- HTTP/2 multiplexing — neden HTTP/1.1’den iyi?
- HTTP/3 (QUIC) — destek durumu.
- DNS: `Dns` interface, custom DNS resolver.
- Connection spec: `ConnectionSpec.RESTRICTED_TLS` vs `MODERN_TLS` vs `COMPATIBLE_TLS`.

**Kaynaklar:**

- [OkHttp interceptors](https://square.github.io/okhttp/features/interceptors/)
- [OkHttp recipes](https://square.github.io/okhttp/recipes/)

### Çar: Retrofit Internals

**Konular:**

- Annotations: `@GET`, `@POST`, `@Body`, `@Path`, `@Query`, `@QueryMap`, `@HeaderMap`, `@Multipart`, `@Part`, `@Url` (dynamic URL).
- Converter Factory: Moshi, kotlinx.serialization, Gson — hangisi neden?
  - **Moshi vs Gson:** codegen, null safety, Kotlin support, performans.
  - **kotlinx.serialization** — tamamen Kotlin native, KMP uyumlu, Moshi’den hızlı.
- Suspend support: Retrofit 2.6+ `suspend fun` döndürür → Call adapter gerekmez.
- `Response<T>` wrapper — HTTP status code’a erişim.
- Custom call adapter yazma (örn. `Result<T>` döndüren).
- `@SerialName` (kotlinx) vs `@Json(name=)` (Moshi) vs `@SerializedName` (Gson).

### Per: Caching, Auth Refresh, Hata Yönetimi

**Konular:**

- OkHttp `Cache(File, maxSizeBytes)`.
- HTTP cache header’ları: `Cache-Control`, `ETag`, `If-None-Match`, `Last-Modified`.
- Cache control override: server header kontrolünü almak — `forceCache()`, `noCache()`, `maxStale`.
- **Token refresh patterns:**
  - `Authenticator` (401 alınca otomatik refresh) — concurrent request senkronizasyonu için Mutex.
  - Race condition: Birden fazla request 401 alırsa, refresh sadece 1 kez yapılmalı.
- Hata yönetimi:
  - Sealed `NetworkResult<T>`: `Success`, `Error(code, message)`, `Exception(throwable)`.
  - `IOException` (network hatası) vs `HttpException` (HTTP error code).
  - Repository katmanında map: domain `Result.failure(DomainError.NoInternet)` gibi.

### Cum: Certificate Pinning ve Güvenlik

**Konular:**

- Certificate Pinning: `CertificatePinner.Builder().add(host, "sha256/...").build()` — MITM koruması.
- Public Key Pinning vs Cert Pinning trade-off (cert rotation problemi).
- Network Security Config (`network_security_config.xml`).
- Cleartext traffic (HTTP) engelleme.
- TLS sürüm zorlaması.

### Cmt-Paz: WebSocket, SSE, gRPC, GraphQL Giriş

**Konular:**

- **OkHttp WebSocket:** `okHttpClient.newWebSocket(request, listener)` — full duplex.
- **Server-Sent Events (SSE):** OkHttp-EventSource — server push, unidirectional.
- **gRPC:** Protobuf-based RPC, bidirectional streaming, HTTP/2 üzerinde. `grpc-kotlin` library.
- **GraphQL:** Apollo Kotlin (Apollo Android) — query/mutation/subscription, codegen.
- Hangisi ne zaman? REST vs GraphQL vs gRPC trade-off.

**Mini Proje:** Production-ready GitHub API client:

- Login → token al, EncryptedSharedPreferences’ta sakla.
- Auth interceptor + Authenticator (refresh).
- Cache yapılandır (5 MB, 1 saat).
- Logging interceptor (sadece debug, BuildConfig).
- Certificate pinning (api.github.com).
- Sealed `NetworkResult<T>` ile UI’a sun.

**Kaynaklar:**

- [Retrofit docs](https://square.github.io/retrofit/)
- [Apollo Kotlin](https://www.apollographql.com/docs/kotlin)
- [grpc-kotlin](https://grpc.io/docs/languages/kotlin/)

-----

## Hafta 9 — Persistence (Room + DataStore + ileri)

### Pzt-Sal: Room İleri Seviye

**Konular:**

- `@Entity`, `@PrimaryKey(autoGenerate=true)`, `@ColumnInfo`, `@Index(unique=true)`.
- `@Embedded` (composition, düz tablo).
- `@Relation` — one-to-many, many-to-many.
- `@Junction` — many-to-many ara tablo.
- `@TypeConverter` — Date, List<String>, Enum custom tipleri.
- `Flow<T>` döndüren DAO — reactive query, value değiştiğinde re-emit.
- `suspend fun` DAO — Room otomatik IO dispatcher’a koyar.
- **Migration:**
  - Manuel `Migration(from, to)`.
  - `AutoMigration` (basit field add/remove için).
  - `fallbackToDestructiveMigration` — sadece dev’de OK, production’da ASLA.
  - `MigrationTestHelper` — migration test.
- `@DatabaseView` — read-only logical view, complex JOIN’i sarmak için.
- Multi-instance lock — Room neden singleton olmalı? File lock corruption.
- **Room FTS (Full-Text Search):** `@Fts4` annotation, `MATCH` query — local search için.
- **Database encryption:** SQLCipher integration. `SupportFactory(passphrase)`.

**Kaynaklar:**

- [Room official guide](https://developer.android.com/training/data-storage/room)
- [Room Migrations](https://developer.android.com/training/data-storage/room/migrating-db-versions)
- [SQLCipher for Android](https://www.zetetic.net/sqlcipher/sqlcipher-for-android/)

### Çar: DataStore

**Konular:**

- **Preferences DataStore:** Key-value, type-safe değil (her key için tip bilmen gerek).
- **Proto DataStore:** `.proto` schema, type-safe, evolution-friendly.
- Singleton şart — neden? File corruption riski (file-backed, atomic write).
- `Flow<Preferences>` — reactive okuma.
- `edit { preferences -> ... }` — atomic write transaction.
- SharedPreferences’tan migration: `SharedPreferencesMigration`.
- `corruptionHandler` — IO error recovery.
- `produceFile` lambda — file location.

**Pratik:** Mevcut `SharedPreferences` kullanan bir feature’ı Proto DataStore’a migrate et. `.proto` schema yaz, codegen incele.

### Per: Pagination 3

**Konular:**

- `PagingSource<Key, Value>` — sayfa yükleme kaynağı (network veya DB).
- `RemoteMediator<Key, Value>` — network + DB hibrit (offline-first paging).
- `Pager(config) { pagingSourceFactory }`.
- `Flow<PagingData<T>>` — UI’a stream.
- `LazyPagingItems` (Compose) — `collectAsLazyPagingItems()`.
- Load states: `LoadState.Loading`, `LoadState.NotLoading`, `LoadState.Error`.
- Headers/footers: `PagingDataAdapter` (View) veya Compose’da `item { LoadState }`.
- Refresh stratejileri: `pagingItems.refresh()`, invalidate.

**Kaynaklar:**

- [Paging 3 library](https://developer.android.com/topic/libraries/architecture/paging/v3-overview)
- [NowInAndroid pagination örneği yok ama Tivi var](https://github.com/chrisbanes/tivi)

### Cum: Offline-First Mimarisi

**Konular:**

- **Single Source of Truth (SSOT):** UI sadece DB’yi okur, network DB’yi günceller.
- **NetworkBoundResource pattern:** Cache → emit cache → fetch network → update cache → re-emit.
- Sync stratejileri: pull-to-refresh, periodic (WorkManager), event-based, push (FCM).
- **Conflict resolution:** Last-write-wins, version vector, CRDT (advanced).
- **Sync state UI:** Last synced timestamp, “syncing now” indicator.
- **Optimistic update:** UI’da hemen güncelle, başarısız olursa rollback.

**Mini Proje (Cmt-Paz):** Haber uygulaması:

- Room (cache) + Retrofit + Proto DataStore (kullanıcı tercihleri).
- Pagination 3 + RemoteMediator (network + DB hibrit).
- Internet yokken cache’den göster.
- Pull-to-refresh.
- WorkManager periodic sync (her 1 saat).
- FTS ile local search.
- DB encryption (SQLCipher).

-----

## Hafta 10 — Jetpack Compose (Kısım 1: Temel + State)

> Compose 3 hafta. Yüzeysel öğrenmek seni Compose “kullanıcısı” yapar, derin öğrenmek “tasarımcısı” yapar.

### Pzt-Sal: Compose Mental Modeli

**Konular:**

- Declarative UI — “ne göster” değil “ne olmalı”.
- **Composition** vs **Recomposition** — tree diff.
- `@Composable` annotation — fonksiyon nasıl Composable oluyor? Compose Compiler’ın yaptıkları.
- `Composer` parametresi — invisible argument.
- **Positional memoization** — `remember { }` nasıl çalışır?
- Slot API — `content: @Composable () -> Unit` slot pattern.
- Smart recomposition — sadece input değişen Composable’lar yeniden çağrılır.

**Kaynaklar:**

- [Thinking in Compose](https://developer.android.com/jetpack/compose/mental-model)
- [Jetpack Compose internals - Jorge Castillo](https://leanpub.com/composeinternals) (kitap)
- [Compose Compiler architecture](https://developer.android.com/jetpack/androidx/releases/compose-compiler)

### Çar: State Management

**Konular:**

- `mutableStateOf<T>()` — `MutableState<T>`, `value` getter/setter ile değişimi izlenir.
- `remember { }` — composition lifetime’ında saklar.
- `rememberSaveable { }` — config change ve process death’e dayanır (Bundle’a).
- `State hoisting` — state yukarıya, event aşağıya. Stateless composables.
- `derivedStateOf` — değişken state’ten az değişen state türetme.
  - **Yanlış kullanım uyarısı:** Tüm hesaplamayı `derivedStateOf`’a sarmak gereksizdir, sadece “ucuz olmayan” hesaplamalarda.
- `produceState` — async kaynak → state.
- `snapshotFlow { }` — Compose state’i Flow’a çevirme.
- `Snapshot` system — Compose’un MVCC’si (multi-version concurrency control).

### Per: Side Effects

**Konular:**

- Side effect = Composition dışı bir şeye etki.
- `LaunchedEffect(key)` — key değişince yeni coroutine başlar, eski cancel olur.
- `rememberCoroutineScope()` — event handler’dan coroutine başlatmak için (`onClick = { scope.launch { } }`).
- `DisposableEffect(key)` — setup + cleanup (listener register/unregister).
- `SideEffect { }` — her successful recomposition sonrası çalışır.
- `rememberUpdatedState` — closure problemi çözümü (LaunchedEffect içinde değişen lambda).

**Kaynaklar:**

- [Side effects guide](https://developer.android.com/jetpack/compose/side-effects)

### Cum: Modifier Sistemi

**Konular:**

- Modifier = immutable, ordered chain.
- **Sıra önemli:** `Modifier.padding(8.dp).background(Red)` ≠ `Modifier.background(Red).padding(8.dp)`.
- Built-in modifiers: padding, size, fillMaxSize, weight, clickable, background, border, shadow.
- `Modifier.Companion` — root.
- Custom modifier (eski): `composed { }`.
- **Modifier.Node API** (Compose 1.6+) — yeni, performanslı custom modifier API’si. Allocation maliyetini düşürür.

### Cmt-Paz: Material 3 ve Theming

**Konular:**

- Material 3 (Material You) — dynamic color, expressive theming.
- `MaterialTheme(colorScheme, typography, shapes)`.
- `colorScheme`: light/dark, dynamicColorScheme (Android 12+).
- Custom design system: kendi `MyAppTheme` wrapper’ın.
- `CompositionLocalProvider` — implicit dependency injection (theme, density).
- `LocalContext`, `LocalDensity`, `LocalConfiguration` — built-in.
- Custom CompositionLocal: `staticCompositionLocalOf` vs `compositionLocalOf` farkı.

**Mini Proje:** Kendi design system’ini yaz: theme, typography, shapes, custom CompositionLocal’lar (örn. `LocalSpacing`).

-----

## Hafta 11 — Jetpack Compose (Kısım 2: Layout, Animation, Performance)

### Pzt-Sal: Layout Sistemi

**Konular:**

- Built-in layouts: `Column`, `Row`, `Box`, `LazyColumn`, `LazyRow`, `LazyVerticalGrid`, `ConstraintLayout`.
- **Single-pass measurement** — Compose layout neden View’dan hızlı?
- `Layout(measurePolicy)` — sıfırdan layout yazma.
- Intrinsic measurements: `IntrinsicSize.Min`, `IntrinsicSize.Max` — performans maliyeti var.
- `SubcomposeLayout` — bağımlı ölçüm (Scaffold, BoxWithConstraints kullanır).
- `LookaheadLayout` (Compose 1.5+) — animasyonlu layout transition.
- Lazy lists: `key`, `contentType` — recomposition optimizasyonu.

**Pratik:** Custom `FlowRow` yaz (yatay sıralı, taşınca alta) `Layout` API ile.

### Çar: Animasyonlar

**Konular:**

- `animateXxxAsState` (animateFloatAsState, animateColorAsState, animateDpAsState) — yüksek seviye.
- `Animatable` — daha düşük seviye, manuel kontrol.
- `Transition` — birden çok property’i koordineli animate.
- `AnimatedVisibility`, `AnimatedContent`.
- `Crossfade`.
- `updateTransition` — state-based transition.
- Spring vs Tween vs Keyframes — easing curve seçimi.
- Gesture animation: `Modifier.draggable`, `swipeable`.
- Shared element transitions (Compose 1.7+).

**Kaynaklar:**

- [Compose animations](https://developer.android.com/jetpack/compose/animation)

### Per-Cum: Compose Performance Deep Dive

**Konular:**

- **Stability:**
  - `@Stable`, `@Immutable` — ne zaman gerçekten gerekli?
  - Yanlış stable annotation = bug üretir (input değişti ama Compose fark etmez).
  - `kotlinx-collections-immutable` (`ImmutableList<T>`) — neden? Standard `List<T>` unstable çünkü interface, mutable olabilir.
- **Strong Skipping Mode** (Compose 1.6+) — derleme flag, otomatik skip.
- **Lambda memoization:** `{ onClick(item.id) }` her recomposition’da yeni instance — Compose Compiler bunları otomatik remember eder (ama her zaman değil).
- **Compose Compiler Metrics:**
  
  ```
  composeCompiler {
    reportsDestination.set(layout.buildDirectory.dir("compose_reports"))
    metricsDestination.set(layout.buildDirectory.dir("compose_metrics"))
  }
  ```
  
  Çıktı dosyaları: `composables.txt` (skippable, restartable), `classes.txt` (stable, unstable).
- **Recomposition count tracing:** Layout Inspector → Show Recomposition Counts. Bir Composable çok fazla recompose oluyorsa neden?
- **`derivedStateOf` ne zaman?** Çok tetiklenen state’ten az değişen state türetirken (örn. `scrollState.firstVisibleItemIndex > 0` gibi).
- **`@SnapshotFlow` ile** Compose state’i Flow’a çevirme — RxJava/Flow zincirine bağlamak için.

**Kaynaklar:**

- [Compose Performance](https://developer.android.com/develop/ui/compose/performance)
- [Strong Skipping Mode](https://developer.android.com/develop/ui/compose/performance/stability/strongskipping)
- [Compose stability cheat sheet](https://chris.banes.dev/composable-metrics/) — Chris Banes
- [Jorge Castillo - Compose Compiler Reports](https://newsletter.jorgecastillo.dev/)

### Cmt-Paz: Mini Proje

Bir feed ekranı yaz, başlangıçta yavaş — Compose Compiler reports’u üret, hangi composable’lar restartable/skippable değil bul. Stable annotation, immutable list, lambda memoization ile düzelt. Frame time profile et.

-----

## Hafta 12 — Compose Navigation + Pagination Entegrasyonu

### Pzt-Sal: Navigation Compose

**Konular:**

- `NavController`, `NavHost`, `composable("route")`.
- `navigate("route")`, `popBackStack()`, `navigateUp()`.
- Argument passing: `composable("detail/{id}", arguments = ...)`.
- Deep links: `deepLinks = listOf(navDeepLink { uriPattern = "..." })`.
- Nested navigation graph: `navigation(startDestination, route)`.
- `BackStackEntry` ve scoped ViewModel: `hiltViewModel(navBackStackEntry)`.
- **Type-safe Navigation (Navigation 2.8+):** `@Serializable` data class route’lar, koordinatsız argument passing.

**Kaynaklar:**

- [Navigation Compose](https://developer.android.com/jetpack/compose/navigation)
- [Type-safe Navigation](https://developer.android.com/guide/navigation/design/type-safety)

### Çar: Pagination + Compose Entegrasyonu

- `collectAsLazyPagingItems()`.
- `LazyColumn { items(pagingItems.itemCount, key = pagingItems.itemKey) { ... } }`.
- Load state UI: `pagingItems.loadState.refresh`, `append`, `prepend`.
- Empty state, error state, retry button.

### Per-Cum: Inter-module Navigation

**Konular:**

- Feature module’ler arası navigation — feature’lar birbirini import etmemeli.
- `NavController`‘ı top-level’da tut, feature modülleri sadece route registration sağlar.
- Pattern 1: `feature:profile` modülü `profileScreen(navController)` extension Composable expose eder.
- Pattern 2: `core:navigation` modülünde route enum/sealed class — tüm feature’lar buraya bağlı.

### Cmt-Paz: Mini Proje

Önceki haftaların haberlerini multi-screen’e çevir: home (paginated list) → detail → settings. Type-safe navigation kullan. Deep link ile direkt detail’a açılabilsin.

> **🎯 Faz 2 Çıktısı:** Production-ready networking, persistence, paging, navigation kurabiliyorsun. Compose’da custom layout, animation, performance optimization yapabilirsin. Compose Compiler reports’u okuyup mid-level’in göremeyeceği bottleneck’leri görüyorsun. Offline-first mimarisi ve ileri pagination’u sıfırdan kurabiliyorsun.

-----

# 📙 FAZ 3 — KALİTE & GÜVENLİK (Hafta 13-17)

## Hafta 13 — Test Stratejisi

### Pzt: Test Felsefesi ve Piramit

**Konular:**

- Test piramidi: Unit (çok, hızlı) → Integration (orta) → UI/E2E (az, yavaş).
- **Trophy modeli (Kent C. Dodds):** Static (compiler, lint) → Unit → Integration (en çok) → E2E. Bazı ekipler bu modeli tercih ediyor.
- **Mock vs Fake vs Stub vs Spy** — interview sorusu, doğru cevap:
  - **Stub:** Sabit cevap döndüren basit obje.
  - **Mock:** Davranışı verify edilebilen obje (`verify(mock).method()`).
  - **Fake:** Çalışan ama basitleştirilmiş implementasyon (in-memory DB).
  - **Spy:** Gerçek objenin çağrılarını izleyen, partial mock.
- **Tercih:** Fake > Mock. Mock kırılgan (test refactor’a dirençsiz), Fake davranış kontrolünde.

### Sal: Unit Test Araçları

**Konular:**

- JUnit 4 vs JUnit 5 — Android’de hâlâ JUnit 4 dominant (Robolectric/AGP uyumu).
- **MockK** vs Mockito-Kotlin — MockK Kotlin idiomatic (`every { }`, `coEvery { }`, `verify { }`).
- **Truth** (Google) vs **AssertK** vs **Kotest assertions** — fluent assertion library’leri.
- **Coroutine test:**
  - `kotlinx-coroutines-test`: `runTest`, `TestScope`.
  - `StandardTestDispatcher` vs `UnconfinedTestDispatcher` — virtual time, manual advance.
  - `MainDispatcherRule` — `Dispatchers.setMain(testDispatcher)`.
  - `advanceTimeBy()`, `runCurrent()`.
- **Turbine** — Flow test endüstri standardı. `flow.test { awaitItem(); awaitComplete() }`.

**Pratik:** Önceki haftalardan bir ViewModel’i Turbine + Fake repo ile test et. Tüm `UiState` geçişlerini doğrula.

### Çar: Robolectric ve Android Framework Test

**Konular:**

- Robolectric — JVM’de Android framework simülasyonu. Hızlı, gerçek cihaz değil.
- `@Config(sdk = [33])`.
- `RuntimeEnvironment.application`.
- Robolectric vs Instrumentation test trade-off:
  - Robolectric: Hızlı, CI’da paralel, ama gerçek hardware davranışı yok.
  - Instrumentation: Yavaş, gerçek cihaz/emulator, native code çalışır.

### Per: Compose UI Test

**Konular:**

- `createComposeRule()` — sadece Composable test (Activity yok).
- `createAndroidComposeRule<Activity>()` — Activity ile.
- Finder: `onNodeWithTag` (locale-bağımsız, tercih edilen) > `onNodeWithText` > `onNodeWithContentDescription`.
- `Modifier.testTag("myTag")` — test için ID.
- Action: `performClick()`, `performTextInput()`, `performScrollToIndex()`.
- Assertion: `assertExists()`, `assertIsDisplayed()`, `assertTextEquals()`.
- Sync: `waitForIdle()`, `waitUntil()`, `waitUntilNodeCount()`.
- **Compose semantic tree** — Layout Inspector’da görülebilir.

### Cum: Screenshot Test ve Snapshot Testing

**Konular:**

- **Paparazzi** (Square) — JVM’de, hızlı, layoutlib kullanır.
- **Roborazzi** — Robolectric tabanlı.
- **Showkase** — design system catalog + screenshot test entegrasyonu.
- Use case: Design system component’ları, theming değişikliklerinde regression.
- Diff threshold, baseline image.
- CI’da fail edince diff image artifact yükle.

### Cmt-Paz: Coverage ve Test Stratejisi

**Konular:**

- **JaCoCo** — coverage report.
- `./gradlew testDebugUnitTestCoverage`.
- Coverage hedefi: %70-80 sağlıklı, %100 saplantı (test for testing).
- Mutasyon testi (PIT): “Coverage var ama testler değer üretiyor mu?” — gelişmiş.
- **Kategoriler:**
  - Domain (UseCase) — JVM unit test, %90+ coverage hedefle.
  - Data (Repository) — integration test, fake DB + fake API.
  - Presentation (ViewModel) — unit test, Turbine.
  - UI (Composable) — UI test + screenshot test.

**Kaynaklar:**

- [Testing in Android](https://developer.android.com/training/testing)
- [Turbine](https://github.com/cashapp/turbine)
- [MockK](https://mockk.io/)
- [Paparazzi](https://github.com/cashapp/paparazzi)

**Mini Proje:** Önceki haberler app’inin:

- Tüm UseCase’lerine unit test (%90+ coverage).
- Repository’ye integration test (Fake API + real Room in-memory).
- ViewModel’lere Turbine test.
- En az 3 ekrana UI test.
- Design system component’larına Paparazzi screenshot test.

-----

## Hafta 14 — Performance Profiling

### Pzt-Sal: Android Studio Profiler

**Konular:**

- **CPU Profiler:**
  - Sample tracing — düşük overhead, periodic.
  - Method tracing — yüksek detay, yüksek overhead.
  - System tracing (Perfetto) — kernel-level, frame-level analiz.
  - Flame chart, top-down, bottom-up view.
- **Memory Profiler:**
  - Heap dump (HPROF analizi).
  - Allocation tracking — kim allocate ediyor?
  - Memory leak tespiti.
  - Native memory profiling.
- **Energy Profiler:** CPU, network, location etkisi.
- **Network Profiler:** OkHttp/Retrofit otomatik tanıma.

### Çar: Memory Leaks

**Konular:**

- Activity leak — Context referansını static field’da tutma.
- Anonymous inner class → outer class implicit referansı.
- Handler/Runnable leak.
- Coroutine leak — `GlobalScope` veya yanlış scope.
- **LeakCanary** — debug-only, automatic detection.
- Static field leak.
- Listener register edip unregister etmeme.

**Pratik:** Bilinçli memory leak yarat (Activity’yi singleton’da tut). LeakCanary ile yakala. Heap dump’ı analyze et.

### Per: Frame Drops ve Jank

**Konular:**

- 60fps = 16.67ms/frame, 120fps = 8.33ms/frame.
- **Choreographer** — VSYNC ile senkron frame trigger.
- **Frame phases:**
1. Input (touch event).
1. Animation.
1. Measure/Layout.
1. Draw.
1. Sync to GPU.
- Skipped frames sebepleri: main thread blocking, complex layout, GPU overdraw.
- **GPU overdraw:** Developer options → Debug GPU overdraw. Renkli görsel, kırmızı = 4x+ overdraw.
- **Profile HWUI rendering:** “Bars” görünümü, kategori bazlı süre.
- **Macrobenchmark:**
  - Cold/warm/hot startup ölçüm.
  - Scrolling jank tespiti.
  - `BaselineProfileRule`, `MacrobenchmarkRule`.
- **Microbenchmark** (Jetpack Benchmark) — function-level mikro saniye ölçüm. Tipik kullanım: parser, encoder.

### Cum: ANR (Application Not Responding)

**Konular:**

- 5 saniye main thread block = ANR.
- Broadcast receiver’da 10 saniye = ANR.
- Service’de 20 saniye = ANR.
- **StrictMode** — debug’da yakala:
  
  ```
  StrictMode.setThreadPolicy(ThreadPolicy.Builder()
    .detectDiskReads().detectDiskWrites().detectNetwork()
    .penaltyLog().penaltyDeath().build())
  ```
- ANR Watchdog — production’da custom detection.
- Play Console → Vitals → ANR rate. <%0.47 = “good”, >%1.09 = “bad”.

### Cmt-Paz: Baseline Profiles ve App Startup

**Konular:**

- **Baseline Profiles** — AOT compilation hint, Cloud Profiles üretildiği için ilk install’da yok.
- Cold start %30’a kadar hızlanma.
- Generation: Macrobenchmark ile critical user journey’leri kayıt.
- `BaselineProfileRule` ile generate, `baseline-prof.txt` üret.
- AGP 8.0+ otomatik baseline profile entegrasyonu.
- **Startup metrics:**
  - Cold start: process yok.
  - Warm start: process var, activity yok.
  - Hot start: activity var, foreground’a alınıyor.
  - TTID (Time to Initial Display), TTFD (Time to Full Display).
- **App Startup library** — ContentProvider startup’ı engelleme. Multiple library’ler tek `Initializer` ile.
- Splash Screen API (Android 12+) — sistem-yönetimli splash.

**Kaynaklar:**

- [Macrobenchmark](https://developer.android.com/topic/performance/benchmarking/macrobenchmark-overview)
- [Baseline Profiles](https://developer.android.com/topic/performance/baselineprofiles)
- [App Startup time](https://developer.android.com/topic/performance/vitals/launch-time)

**Mini Proje:**

1. LazyColumn’lu liste, 1000 item.
1. Layout Inspector ile recomposition tracking.
1. Macrobenchmark ile cold startup ölç.
1. Baseline profile generate et.
1. Cold startup öncesi/sonrası karşılaştır (genelde 10-30% iyileşme).

-----

## Hafta 15 — Güvenlik

### Pzt-Sal: Network Security

**Konular:**

- HTTPS zorunluluğu — `usesCleartextTraffic="false"`.
- `network_security_config.xml`:
  - Domain-specific config.
  - Certificate pinning (alternatif).
  - Trust anchors override (debug için custom CA).
- Certificate Pinning (OkHttp): MITM koruması.
- Cert rotation problemi — backup pin.
- Public Key Pinning vs Cert Pinning.

### Çar: Data Security

**Konular:**

- **Android Keystore:** Hardware-backed key storage (TEE — Trusted Execution Environment, StrongBox).
- **EncryptedSharedPreferences** ve **EncryptedFile** (Jetpack Security crypto library).
  - **NOT:** `androidx.security:security-crypto` deprecated yolda. Alternatif: Tink (Google), DataStore + manual encryption.
- **Tink library:** Google’ın crypto API’si, daha modern ve maintained.
- **Biometric authentication:** `BiometricPrompt`, weak vs strong biometrics.
- **Authentication-bound keys** — kullanıcı her seferinde authenticate, key kullanılabilir.
- **DataStore + EncryptedFile** kombinasyonu — file-level encryption.

**Kaynaklar:**

- [Jetpack Security](https://developer.android.com/topic/security/data)
- [Tink](https://github.com/tink-crypto/tink-java)

### Per: Code Security ve R8

**Konular:**

- **R8 (eski adıyla ProGuard):**
  - `minifyEnabled true` — ne yapar?
    - **Shrinking:** Kullanılmayan kod silme (dead code elimination).
    - **Optimization:** Inline, redundancy elimination.
    - **Obfuscation:** Class/method/field rename — reverse engineering zorlaştırır.
    - **Resource shrinking:** Kullanılmayan kaynaklar.
  - **R8 full mode** (AGP 8.0+ default) — daha agresif, breaking change’lere dikkat.
  - `proguard-rules.pro` — keep rules (reflection kullanan kütüphaneler için).
  - Modern library’lerin çoğu artık `consumer-rules.pro` ile kendi rule’larını taşıyor (Retrofit, Moshi, Hilt).
- **APK analizi:** Android Studio “Analyze APK” — DEX, resource, manifest inceleme.
- **DexGuard** (commercial) — daha agresif obfuscation, anti-tampering.
- **Play Integrity API** — device + app integrity check (Play Console enforced).
- **SafetyNet Attestation** deprecated → Play Integrity.
- **Root detection / Tamper detection** — production app’ler için.

### Cum: Permissions ve Privacy

**Konular:**

- Runtime permissions (Android 6+).
- Permission rationale — `shouldShowRequestPermissionRationale()`.
- **Scoped Storage** (Android 10+) — `MediaStore`, `Storage Access Framework`.
- **Photo Picker** (Android 13+) — privacy-preserving photo selection.
- Foreground service permissions (Android 14+).
- **App permissions auditing:** `AppOps` framework.

### Cmt-Paz: Backup, Privacy Manifest ve Compliance

**Konular:**

- `android:allowBackup` — `false` yap, ya da `data_extraction_rules.xml` (Android 12+) ile granular kontrol.
- Cloud backup vs device transfer ayrımı.
- **Privacy:** Play Console Data Safety form. Kullanıcı verilerini hangi 3rd party’ye gönderiyorsun?
- **GDPR / KVKK** uyumu — explicit consent.
- **Children’s Online Privacy Protection Act (COPPA)** — 13 yaş altı.

**Kaynaklar:**

- [App security best practices](https://developer.android.com/topic/security/best-practices)
- [Play Integrity API](https://developer.android.com/google/play/integrity)
- [R8 documentation](https://developer.android.com/build/shrink-code)

**Mini Proje:**

1. Login feature’ında token’ı EncryptedSharedPreferences (veya Tink ile DataStore) ile sakla.
1. Certificate pinning ekle.
1. Release build’de `minifyEnabled true` aç. Crash olursa ProGuard rule’larını yaz.
1. APK boyutunu öncesi/sonrası ölç.
1. Play Integrity API entegre et (login öncesi attestation).

-----

## Hafta 16 — Observability (Logging, Monitoring, Crashlytics)

> **Mevcut planda bu konu yoktu.** Senior’ın production işiniz olmazsa olmazı.

### Pzt-Sal: Structured Logging

**Konular:**

- `Log.d/i/w/e` — basit ama yapısal değil.
- **Timber** — log facade, plant pattern, production’da farklı tree.
  - `Timber.plant(DebugTree())` debug, `Timber.plant(CrashlyticsTree())` release.
- Log seviyeleri: VERBOSE, DEBUG, INFO, WARN, ERROR, ASSERT.
- Tag stratejisi.
- **Structured logging:** JSON-formatted log, key-value, easier to query.
- PII (Personally Identifiable Information) redaction — kullanıcı email/phone log’lama!
- Production’da log volume kontrolü — sampling.

### Çar: Firebase Crashlytics

**Konular:**

- Crash reporting setup (Firebase plugin + SDK).
- ProGuard mapping upload — obfuscated stack trace’ı çözmek için kritik. AGP otomatik upload.
- **Custom keys:** `FirebaseCrashlytics.setCustomKey("user_tier", "premium")`.
- **Custom logs:** `FirebaseCrashlytics.log("User tapped checkout")` — son 64KB log crash’le birlikte.
- **Non-fatal exceptions:** `FirebaseCrashlytics.recordException(e)` — handled exception’ları rapor et.
- **User identifier:** `setUserId()` — sorun yaşayan kullanıcıyı sup ekibine ileteceksen.
- Crash-free user rate — Play Console Vitals + Firebase.
- Velocity alerts — crash spike notification.

### Per: Sentry, Bugsnag Alternatifleri

**Konular:**

- Sentry — Crashlytics alternatifi, daha zengin breadcrumb, performance monitoring birleşik.
- Bugsnag — benzer.
- Trade-off: Crashlytics ücretsiz, Firebase entegre. Sentry daha kontrollü ama paid tier.
- **Open source seçenekleri:** GlitchTip (self-hosted Sentry).

### Cum: Performance Monitoring

**Konular:**

- **Firebase Performance Monitoring:**
  - Otomatik trace: app start, screen render, HTTP requests.
  - Custom trace: `Trace trace = FirebasePerformance.getInstance().newTrace("checkout_flow")`.
  - Network monitoring — Retrofit/OkHttp otomatik.
- **Custom metrics:** Trace içinde `trace.putMetric("items_loaded", 50)`.
- **Production profiling** — Android Vitals, Firebase Performance.

### Cmt-Paz: Analytics ve Custom Events

**Konular:**

- **Firebase Analytics:**
  - Automatic events (first_open, app_update, screen_view).
  - Custom events: `firebaseAnalytics.logEvent("article_read") { param("article_id", id) }`.
  - User properties — segment için.
  - Audience’lar.
- **GA4 (Google Analytics 4)** — Firebase Analytics altyapısı.
- **Other analytics platforms:** Mixpanel, Amplitude, Segment.
- **Event taxonomy** — naming convention, parameter standartları. Erken kararla.
- Privacy: Analytics opt-in (GDPR), `setAnalyticsCollectionEnabled(false)`.

**Mini Proje:** Önceki haber app’ine:

- Timber + CrashlyticsTree.
- Firebase Crashlytics setup.
- 5 custom analytics event (article_view, article_share, search, settings_changed, sync_success).
- Performance monitoring + custom trace (article_load).
- User properties (subscription_tier, app_version).

**Kaynaklar:**

- [Firebase Crashlytics](https://firebase.google.com/docs/crashlytics)
- [Firebase Performance Monitoring](https://firebase.google.com/docs/perf-mon)
- [Firebase Analytics](https://firebase.google.com/docs/analytics)
- [Timber](https://github.com/JakeWharton/timber)

-----

## Hafta 17 — Background Work ve Sistem Entegrasyonu

### Pzt-Sal: WorkManager

**Konular:**

- `OneTimeWorkRequest`, `PeriodicWorkRequest` (min interval 15 dakika).
- Constraints: `setRequiredNetworkType()`, `setRequiresCharging()`, `setRequiresBatteryNotLow()`, `setRequiresStorageNotLow()`.
- `Worker` (sync) vs `CoroutineWorker` (suspend `doWork()`).
- Chained work: `WorkManager.beginWith(work1).then(work2).enqueue()`.
- Parallel work: `beginWith(listOf(work1, work2))`.
- `setExpedited(OutOfQuotaPolicy.RUN_AS_NON_EXPEDITED_WORK_REQUEST)` (Android 12+) — yüksek öncelik.
- **Hilt + WorkManager:** `@HiltWorker` annotation, `HiltWorkerFactory` setup.
- Unique work: `enqueueUniqueWork(name, ExistingWorkPolicy.KEEP/REPLACE)`.
- Periodic work flex interval.
- **Observability:** `WorkManager.getWorkInfoByIdLiveData()`, Flow versiyonu.

### Çar: Foreground Service

**Konular:**

- Android 14+ kısıtlamaları — `foregroundServiceType` zorunlu (manifest + start).
- Tipler: `dataSync`, `mediaPlayback`, `phoneCall`, `location`, `connectedDevice`, `mediaProjection`, `camera`, `microphone`, `health`, `remoteMessaging`, `shortService`, `specialUse`, `systemExempted`.
- Foreground service notification gerekli (5 sn içinde göster).
- Background service kısıtlamaları (Android 8+).

### Per: Broadcast Receiver, Notification, Deep Linking

**Konular:**

- Broadcast Receiver:
  - Static (manifest) — Android 8+ implicit broadcast’ler kısıtlı.
  - Dynamic (kod) — Activity/Service yaşamı kadar.
  - LocalBroadcastManager deprecated → `LiveData`/`Flow`/`SharedFlow`.
- **Notification:**
  - Channels (Android 8+) — kullanıcı channel-bazlı disable edebilir.
  - Notification permission (Android 13+) — `POST_NOTIFICATIONS` runtime permission.
  - PendingIntent flag’leri: `FLAG_IMMUTABLE` (Android 12+ zorunlu).
  - Big text style, big picture, inbox, messaging style.
  - Action buttons.
  - Direct reply.
  - Foreground service notification.
- **Deep Linking:**
  - Implicit deep link (intent-filter) — herhangi browser açabilir.
  - **App Links (verified)** — `assetlinks.json` ile domain ownership doğrulama, otomatik açar.
  - Compose Navigation deep link.

### Cum: FCM (Push Notifications)

**Konular:**

- **Firebase Cloud Messaging:**
  - Notification message (sistem otomatik notification, app foreground’da değilse).
  - Data message (app handle eder her durumda).
  - Both (data + notification) — risk: app kill edildiğinde data drop olabilir.
- `FirebaseMessagingService` — `onMessageReceived`, `onNewToken`.
- Token registration — backend’e gönder.
- Topic subscription: `subscribeToTopic("news")`.
- Notification channels mapping.
- Server-side (REST API veya Admin SDK).
- HTTP v1 API (eski legacy `/send` deprecated).

### Cmt-Paz: Mini Proje

WorkManager + FCM + Notification:

1. Periodic sync (her 6 saat, sadece WiFi, charging not required).
1. Hilt ile inject.
1. FCM ile silent push → on demand sync trigger.
1. Sync sonucunda yeni haber varsa notification göster.
1. Notification tap → deep link ile direkt detail ekranına.
1. WorkInfo’yu UI’da observe et (sync status indicator).

**Kaynaklar:**

- [WorkManager guide](https://developer.android.com/topic/libraries/architecture/workmanager)
- [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging)
- [App Links](https://developer.android.com/training/app-links)

> **🎯 Faz 3 Çıktısı:** Test pyramid’i kuruyor, %70+ coverage’a sahipsin. Performance profiling yapıp bottleneck tespit ediyorsun. Baseline profile generate ediyorsun. Crashlytics + Performance Monitoring + Analytics ile production observability’n var. WorkManager + FCM + Notifications ile background ekosistemi. Senior bar’ına çok yaklaştın.

# 🚀 FAZ 4 — SENIOR MÜHENDİSLİK (Hafta 18-22)

## Hafta 18-19 — Modularization

### Hafta 18: Mimari ve Modül Tipleri

**Konular:**

- **Neden modülarize?**
  - Build time (parallel compilation, incremental).
  - Separation of concerns, encapsulation.
  - Reusability, team scalability.
  - Dynamic feature delivery.
  - Architecture enforcement (lint rule ile).
- **Modül tipleri (NowInAndroid pattern):**
  - `app` — entry point, sadece DI graph + navigation root.
  - `feature:xxx` — UI + ViewModel + screen-specific.
  - `core:domain` — UseCase, business logic (pure Kotlin/JVM).
  - `core:data` — Repository implementation, DataSource’ları birleştirir.
  - `core:network` — Retrofit, OkHttp config, DTO’lar.
  - `core:database` — Room.
  - `core:datastore` — DataStore.
  - `core:model` — Pure Kotlin domain models.
  - `core:designsystem` — Compose theme, atomic components, icon set.
  - `core:ui` — shared composables (NewsCard, UserAvatar).
  - `core:common` — utilities, dispatchers qualifier.
  - `core:testing` — fake’ler, test util.
  - `core:analytics` — analytics interface (helper’lar).
  - `core:notifications` — FCM, notification builder helper.
  - `sync:work` — WorkManager worker’lar.
- **Bağımlılık yönü kuralları:**
  - `feature → core` ✅
  - `feature → feature` ❌ — navigator pattern ile çöz (sealed Route + nav graph).
  - `core:data → core:network, core:database` ✅
  - `core:domain → core:data` ❌ — domain interface tanımlar, data implement eder (DIP).
  - Lint rule ile enforce: yanlış import → CI fail.
- **`api` vs `implementation` modüller arası:**
  - Public API ihtiyacı: model class, public interface.
  - Internal: Retrofit/Room — `implementation` (transitive görünmesin).
  - Kural: minimum exposure. Şüpheliysen `implementation`.

**Kaynaklar:**

- [NowInAndroid ModularizationLearningJourney](https://github.com/android/nowinandroid/blob/main/docs/ModularizationLearningJourney.md) — **MUTLAKA satır satır oku**
- [Modularization guide](https://developer.android.com/topic/modularization)
- [Modularization patterns](https://developer.android.com/topic/modularization/patterns)

### Hafta 19: Convention Plugins ve Inter-Module Navigation

**Konular:**

- `build-logic/` composite build:
  - `convention` subproject.
  - Convention plugin’ler: `myapp.android.application`, `myapp.android.library`, `myapp.android.library.compose`, `myapp.android.feature`, `myapp.android.hilt`, `myapp.android.test`, `myapp.android.firebase`, `myapp.jvm.library`.
- Bir modül `build.gradle.kts` artık **5-10 satır**.
- **Inter-module navigation:**
  - **Type-safe routes** Compose Navigation 2.8+: `@Serializable data class NewsDetail(val id: String)`.
  - Feature module’ler kendi route’unu export eder, navigation graph extension function’la.
  - `navController.navigate(NewsDetail("id-1"))`.
- **Feature flag based modularization** — bir feature’ı disable etmek için sadece `app/build.gradle`’dan dependency çıkar.
- **Dynamic feature module:** `:dynamic-feature:premium`. On-demand download.
- **Asset Pack:** Büyük asset’ler (game level, video) — install-time, fast-follow, on-demand.

**Kaynaklar:**

- [Compose Navigation type-safe](https://developer.android.com/guide/navigation/design/type-safety)
- [Play Feature Delivery](https://developer.android.com/guide/playcore/feature-delivery)

### Pratik (2 hafta)

Mevcut bir tek-modül projeni modularize et. Adım adım:

1. `core:model` ve `core:network` ayrıştır.
1. `core:database`, `core:datastore`, `core:data` ekle.
1. `core:designsystem`, `core:ui` çıkar — Compose theme + atomic components.
1. Feature’ları `feature:xxx`’a böl.
1. `app` sadece DI + nav root.
1. Convention plugin’leri yaz.
1. Custom Lint rule yaz: `feature → feature` import’unu yasaklasın.
1. Build time’ı önce/sonra ölç (`--profile`).

Hedef: NowInAndroid’in modül yapısı seviyesi.

-----

## Hafta 20 — Build Optimization, App Size, Release

### Pzt: Build Performans

**Konular:**

- **Configuration Cache** — `org.gradle.configuration-cache=true`. Reusable configuration.
- **Build Cache** — local + remote (Gradle Enterprise / Develocity).
- **Parallel execution** — `org.gradle.parallel=true`, `org.gradle.workers.max=N`.
- **JVM tuning** — `org.gradle.jvmargs=-Xmx6g -XX:+UseG1GC -XX:MaxMetaspaceSize=1g`.
- **Kotlin daemon** — incremental compilation.
- **KSP > KAPT** — neden? KAPT JVM stub generate, 3-5x yavaş.
- **`dependencyAnalysis` plugin** — gereksiz dependency tespiti.
- **`module-graph-assert`** — modül grafiği constraint’leri (cycle yok, direct dep limit).

### Sal: App Size Optimization

**Konular:**

- **Android App Bundle (AAB)** — Play Store ABI/density/language split.
- APK vs AAB — AAB Play Store zorunlu (Aug 2021+).
- **R8 full mode** — `android.enableR8.fullMode=true` — daha agresif obfuscation, type info kaldırma.
- **Resource shrinking** — `shrinkResources true`.
- **Image optimization:**
  - WebP > PNG (lossless).
  - VectorDrawable > PNG (icon’lar).
  - AVIF (Android 12+) — yeni nesil.
  - **`android.enableNewResourceShrinker=true`** (default modern).
- **Native libraries:** `abiFilters` ile sadece gerekli ABI’lar (arm64-v8a, armeabi-v7a, x86_64).
- **Asset Pack** — büyük asset’leri AAB dışına.
- **APK Analyzer** — DEX dosyaları, resources, manifest.
- **App Size impact:** her dependency ne kadar ekliyor — `dependencyAnalysis` + APK Analyzer.
- **Dynamic feature** — opsiyonel feature on-demand.
- **Play App Signing** — Google upload key + signing key ayrımı.

**Kaynaklar:**

- [Reduce app size](https://developer.android.com/topic/performance/reduce-apk-size)
- [App Bundle](https://developer.android.com/guide/app-bundle)
- [Optimize your build](https://developer.android.com/build/optimize-your-build)

### Çar: CI/CD Pipeline

**Konular:**

- **GitHub Actions / GitLab CI / Bitrise / CircleCI** — temel kavramlar aynı.
- Workflow dosyası: trigger (push, PR, tag).
- **Gradle cache** — `actions/cache` veya Gradle’s official cache action.
- **Matrix build** — multiple API levels (instrumentation test).
- **Steps:**
  - Checkout → Setup JDK → Gradle cache → Build → Test → Lint → Upload artifact.
- **Signing in CI:**
  - Keystore’u base64 encode → secret olarak sakla.
  - CI’da decode → `keystore.jks`’a yaz → Gradle env var.
- **Play Publisher API:**
  - `gradle-play-publisher` plugin.
  - Service account JSON, Play Console API enable.
  - `publishBundle`, `promoteArtifact` task’ları.
- **Fastlane** alternative — Ruby-based, supply (Play upload), screenshot generation, beta dağıtım.
- **Firebase App Distribution** — internal beta dağıtımı.

### Per: Code Quality Otomasyonu

**Konular:**

- **Detekt** — static analysis, custom rules.
  - `detekt-formatting` — ktlint wrap.
  - `detekt-compose` rules.
  - Custom rule yazma — architecture enforcement.
- **ktlint** — formatting standalone.
- **Spotless** — multi-tool formatter (ktlint + google-java-format).
- **Android Lint** — built-in. Custom Lint check yazma.
- **Pre-commit hook** — `husky` veya `lefthook`.
- **PR checks** — fail PR if lint/test fail.
- **Codecov** — coverage tracking, PR comment.

### Cum-Cmt: Release Süreci ve Play Store

**Konular:**

- **Play Console:**
  - Internal testing (100 tester’a kadar, hızlı review).
  - Closed testing (testers email/Google Group).
  - Open testing (public link).
  - Production.
- **Staged rollout** — %1 → %10 → %50 → %100. Crash spike → halt.
- **In-app updates:**
  - `app-update` library.
  - Flexible update (background download) vs Immediate update (full screen).
- **In-app review:**
  - `review` library — Play Store review prompt.
  - Quota’ya dikkat — sık triggerlama.
- **Versioning:**
  - `versionCode` (integer, monotonik artan).
  - `versionName` (semver).
  - Otomatik artırma — git commit count, tag.
- **Release notes:** otomatik changelog (conventional commits + script).
- **Crashlytics velocity alerts** — release sonrası izle.

### Paz: Tekrar

**Mini Proje (büyük):**

- Multi-module projende:
  - GitHub Actions: build, test, lint, screenshot test, coverage upload.
  - Detekt + ktlint pre-commit.
  - Custom Lint rule (en az 1).
  - Renovate bot.
  - Signing CI’da.
  - `gradle-play-publisher` config.
  - Fastlane veya GitHub Action ile Firebase App Distribution.
  - Staged rollout config.

**Tekrar:**

1. AAB ile APK arasındaki kullanıcı için fark?
1. R8 full mode’un en büyük riski ve nasıl korursun?
1. Staged rollout sırasında crash spike → ne yaparsın?

-----

## Hafta 21 — İleri Compose (Custom, Performans, Compiler)

### Pzt-Sal: Custom Layout ve Modifier

**Konular:**

- **`Layout(measurePolicy)` API** — sıfırdan custom layout. Measure → Place.
- `Constraints` — incoming, child’a geçen.
- Intrinsic measurements — `IntrinsicMeasurePolicy`.
- **`SubcomposeLayout`** — bağımlı ölçüm (Scaffold, BoxWithConstraints, LazyColumn).
- **`LookaheadLayout` / `LookaheadScope`** — animation için “gelecekteki” layout.
- **Modifier sistemi internals:**
  - Compose 1.4 öncesi `composed { }` — recomposition heavy.
  - **Modifier.Node API** (1.6+) — performans odaklı, instance reuse.
  - `ModifierNodeElement<T>` — implementation pattern.
- **Custom Modifier yazma:**
  - Eski stil: `Modifier.composed { ... }`.
  - Yeni stil: `class MyNode : Modifier.Node, DrawModifierNode`.

**Kaynaklar:**

- [Custom layouts](https://developer.android.com/develop/ui/compose/layouts/custom)
- [Modifier.Node API](https://developer.android.com/develop/ui/compose/custom-modifiers)
- [Composing custom layouts - Chris Banes](https://medium.com/androiddevelopers/custom-layouts-with-jetpack-compose-bca7c2691baf)

**Pratik:** `FlowRow`‘u sıfırdan yaz (item’lar yatay, taşınca alt satır). `Layout` API ile.

### Çar: Compose Compiler Internals ve Stability

**Konular:**

- **Compose Compiler nasıl çalışır?**
  - `@Composable` function’lara implicit `Composer` parametresi enjekte.
  - Recomposition için “key” generation.
  - `remember { }` slot table.
- **Stability:**
  - **Stable types:** primitives, String, function types, `@Stable`/`@Immutable` annotated.
  - **Unstable:** `var`, `List<T>` (interface), generic types.
  - **`@Stable`** — equality consistent + state observable.
  - **`@Immutable`** — `@Stable` + state never changes.
  - **`kotlinx.collections.immutable`** — `ImmutableList<T>` neden? `List<T>` interface unstable.
- **Strong Skipping Mode** (Compose Compiler 1.5.4+):
  - Unstable type’ları da skip eder (eğer instance equality varsa).
  - Lambda memoization değişikliği.
  - Default’a 1.7’de açıldı.
- **Compose Compiler Reports:**
  
  ```
  composeCompiler {
    reportsDestination = layout.buildDirectory.dir("compose_reports")
    metricsDestination = layout.buildDirectory.dir("compose_metrics")
  }
  ```
  - `<module>-classes.txt` — stable/unstable.
  - `<module>-composables.txt` — skippable/restartable.
  - **Hangi composable skip etmiyor?** Reports’tan oku, fix.

### Per: Recomposition Tracing ve Tuning

**Konular:**

- **Layout Inspector** — recomposition counts, skip counts.
- **`Modifier.recomposeHighlighter()`** (debug) — recompose olan border flash.
- **Common pitfalls:**
  - Lambda her recomposition’da yeniden allocate → child recompose.
  - Stable olmayan parametre → child skip etmiyor.
  - `mutableStateOf`’ı `remember` olmadan yazma → her recompose reset.
- **`derivedStateOf`** — sık tetiklenen state’ten az değişen türetirken.
  - `derivedStateOf { listState.firstVisibleItemIndex > 0 }`.
  - Yanlış kullanım: simple derive için (overhead).
- **`@SnapshotFlow`** — Compose state’i Flow’a çevirme (`snapshotFlow { }`).
- **Lazy list optimization:**
  - `key = { it.id }` — composition reuse.
  - `contentType = { ... }` — heterogen liste recycling.

### Cum-Cmt: Animation API

**Konular:**

- `animateXxxAsState` — basit value animation.
- `Animatable` — full control (cancel, snap, target update).
- `Transition` ve `updateTransition` — multi-state animation.
- `AnimatedContent` — content transition (slide, fade).
- `AnimatedVisibility`.
- **Spring physics** — `spring(dampingRatio, stiffness)` vs `tween`.
- **`LookaheadScope` + `Modifier.animateBounds`** — automatic shared element transition (1.7+).
- **Compose Multiplatform Animation** — KMP’ye uyumlu.

**Pratik:** Bir bottom sheet animasyonu yaz. State değişince content slide. Drag gesture support.

### Paz: Tekrar

**Pratik (final week project parçası):** Mevcut Compose ekranlarını compose compiler reports ile analiz et. Skippable olmayan composable’ları fix. Önce/sonra recomposition count karşılaştır.

**Tekrar:**

1. `derivedStateOf` ne zaman gerekli, ne zaman gereksiz overhead?
1. `List<T>` neden unstable, `ImmutableList<T>` neden stable?
1. Strong Skipping mode açtığında dikkat etmen gereken nedir?

-----

## Hafta 22 — Now in Android Klonlamaya Başla (1. Yarı)

> **Bu hafta gerçek senior projendir.** NowInAndroid’in `:app`, `:core:network`, `:core:database`, `:core:data`, `:core:domain`, `:core:model`, `:core:designsystem`, `:core:ui` modüllerini sıfırdan kur.

### Hedefler

- Convention plugin’lerle multi-module setup (Faz 4’ten devam).
- API: önce mock JSON server (json-server, MSW, ya da local file).
- Repository + UseCase + ViewModel akışı.
- Compose theme (Material 3, dynamic color).
- DesignSystem komponentleri: NiaButton, NiaTextField, NiaTopAppBar, NiaIconToggleButton.
- 2 feature: `feature:foryou` (basit), `feature:bookmarks` (basit).

### Adım Adım

1. Pzt: Modül iskeleti, convention plugin’ler.
1. Sal: `core:network` + Retrofit + Moshi + interceptor’lar.
1. Çar: `core:database` Room + entity’ler + DAO + Flow query.
1. Per: `core:data` repository + sync logic stub.
1. Cum: `core:designsystem` theme + atomic components + Paparazzi screenshot test.
1. Cmt: `feature:foryou` UI + ViewModel + state.
1. Paz: Test’ler + lint pass.

> **🎯 Faz 4 Çıktısı:** Multi-module mimari kuruyorsun, convention plugin’lerle modüller minimal. App size optimization, R8 full mode, AAB workflow var. CI/CD pipeline + Play Publisher + Fastlane biliyorsun. İleri Compose: custom layout, Modifier.Node, compiler reports analizi yapabiliyorsun. NowInAndroid klonu yarısı bitti.

-----

# 🌟 FAZ 5 — İLERİ KONULAR (Hafta 23-27)

## Hafta 23 — Firebase Ekosistemi Derinlemesine

> **Mevcut planda eksikti.** Senior’ın günlük araç kutusu.

### Pzt-Sal: Firebase Foundation

**Konular:**

- **Firebase BoM** — `platform("com.google.firebase:firebase-bom:X.Y.Z")` — version uyumu otomatik.
- `google-services.json` setup, `com.google.gms.google-services` plugin.
- **Build flavor başına Firebase project** — dev/staging/prod ayrımı.
- Multi-environment config: `app/src/dev/google-services.json`.

### Çar: Crashlytics Mastery (Hafta 16’nın üstüne)

**Konular:**

- Mapping file upload otomatik (`firebase-crashlytics-gradle`).
- `firebaseCrashlytics { mappingFileUploadEnabled = true }`.
- `recordException(e)` ile context: `setCustomKey("screen", "checkout")`.
- **Crashlytics Issues** organization — Velocity Alerts, Regression detection.
- **NDK crashes** — `firebase-crashlytics-ndk`.
- Privacy: `setCrashlyticsCollectionEnabled(false)` — GDPR opt-out.
- BigQuery export — kendi analytics ile join.

### Per: Performance Monitoring Detaylı

**Konular:**

- Otomatik trace’ler — startup, screen render, network.
- **Custom trace:**
  
  ```
  val trace = Firebase.performance.newTrace("checkout")
  trace.start()
  trace.putAttribute("payment_method", "credit_card")
  trace.incrementMetric("items_count", 5)
  // ...
  trace.stop()
  ```
- HTTP metric otomatik (OkHttp interceptor — Firebase ekler).
- Threshold ayarı, trace sampling.
- **Web vitals** karşılığı: TTID, TTFD reporting.

### Cum: Remote Config + Feature Flags

**Konular:**

- `Firebase.remoteConfig` — key-value, default + remote.
- `setDefaultsAsync(R.xml.remote_config_defaults)`.
- `fetchAndActivate()` — minimum fetch interval (default 12h, dev’de 0).
- **Use cases:**
  - Feature flag (yeni feature kapalı, %5 rollout).
  - Kill switch (sorunlu feature’ı remote disable).
  - A/B test config.
  - Server-driven UI parametreleri (string, color, layout).
- **Personalization** — kullanıcı bazlı config.
- `RealtimeRemoteConfig` (Android) — push update, polling değil.
- **A/B Testing** — Firebase Console + Remote Config.

### Cmt: Analytics ve Audiences

**Konular:**

- `FirebaseAnalytics` event’ler — built-in (screen_view, login, purchase) + custom.
- `logEvent(name, bundle)` — naming convention (snake_case, max 40 char).
- **User properties** — `setUserProperty()`. Audience tanımı için.
- **DebugView** — real-time event’ler (debug build).
- **BigQuery export** — raw event’lere SQL.
- **Conversion events** — goal tracking.
- **Predictions** — churn, LTV (BigQuery + ML model).

### Paz: Cloud Messaging + App Distribution + Test Lab

**Konular:**

- **FCM** (Hafta 17’nin üstüne):
  - Topic vs token-based.
  - Conditional messaging (topic + topic).
  - REST API’den gönderme (server-side).
- **App Distribution** — internal beta, group-based.
- **Firebase Test Lab** — gerçek cihazlarda test (Robo, instrumented).
- **Authentication** — eğer kullanırsan: email/password, Google, phone, anonymous.
- **Cloud Functions** — backend’e bağlı senaryolar.

**Mini Proje:**

- Crashlytics ile staged rollout izleme.
- Remote Config ile feature flag (yeni UI %10 user’a açık).
- Custom analytics events ile checkout funnel.
- Performance custom trace.
- A/B test setup (Remote Config + Analytics).

**Tekrar:**

1. Remote Config minimum fetch interval’i development’ta nasıl 0’a çevrilir, neden?
1. Crashlytics velocity alert hangi metric üzerinden tetiklenir?
1. BigQuery export ne zaman gerekli?

-----

## Hafta 24 — Accessibility (a11y)

> **Google için kritik. Mevcut planda yoktu. WCAG compliance senior’ın sorumluluğu.**

### Pzt: a11y Temelleri

**Konular:**

- **WCAG 2.1 AA standardı** — Google bar.
- **TalkBack** — screen reader, gözden engelli kullanıcılar.
- **Switch Access** — motor engelli kullanıcılar.
- **Live Region** — dinamik içerik anonsu.
- **Touch target size:** minimum 48x48dp.
- **Color contrast:** 4.5:1 (normal text), 3:1 (large text).
- **Font scaling** — system font size 200%’e kadar destek.

### Sal: Compose Accessibility

**Konular:**

- **`Modifier.semantics { }`** — semantic tree manipulation.
- `contentDescription` — image, icon button.
- `Modifier.clearAndSetSemantics { }` — child semantics override.
- `Modifier.clickable(onClickLabel = "...")` — TalkBack action label.
- **`Role`** — `Role.Button`, `Role.Checkbox`, `Role.Image`.
- **`stateDescription`** — toggle state (“açık”, “kapalı”).
- **Heading hierarchy** — `Modifier.semantics { heading() }`.
- **`mergeDescendants = true`** — alt elementleri tek node yap.
- **Custom action** — `customActions = listOf(CustomAccessibilityAction(...))`.

### Çar: TalkBack Test ve Automated Tests

**Konular:**

- TalkBack’i aç, app’i sadece TalkBack ile kullanmaya çalış. (Acıdır ama öğreticidir.)
- **Accessibility Scanner** (Google) — app store, otomatik tarama.
- **Espresso accessibility checks:** `AccessibilityChecks.enable()` — instrumented testte otomatik fail.
- **Compose UI Test:**
  - `onNodeWithContentDescription`.
  - `assertContentDescriptionEquals`.
- **Lint check** — `MissingAccessibilityLabel`.

### Per: Focus, Motion, ve Diğer

**Konular:**

- **Focus management:** `Modifier.focusable`, `FocusRequester`.
- **Keyboard navigation** — Compose tab order.
- **Reduce motion** — `Settings.Global.TRANSITION_ANIMATION_SCALE` kontrolü.
- **High contrast mode** detection.
- **Dynamic text size** support — `sp` kullan, `dp` değil text için.
- **Color-blind safe palette** — sadece renge dayanma (icon + text birlikte).

### Cum-Cmt: Pratik

NowInAndroid klonunda full accessibility pass:

1. Tüm icon button’lara contentDescription.
1. Tüm Card’lara mergeDescendants.
1. State değişen component’lere stateDescription.
1. Touch target size audit.
1. TalkBack ile manuel test.
1. Accessibility Scanner taraması.

### Paz: Tekrar

1. `Modifier.semantics` vs `Modifier.clearAndSetSemantics` farkı?
1. `mergeDescendants = true` ne zaman zararlı?
1. Color contrast nasıl test edilir, hangi araçlar?

-----

## Hafta 25 — Internationalization (i18n) ve Localization (l10n)

> **Mevcut planda yoktu.**

### Pzt-Sal: String Resource Yönetimi

**Konular:**

- `strings.xml` — `values/`, `values-tr/`, `values-de/`.
- **Plural rules** — `<plurals>` tag.
- **String formatting:**
  - `getString(R.string.greeting, name)`.
  - **Compose:** `stringResource(R.string.greeting, name)`.
- **Translatable=false** — hassas string’ler.
- **`xliff:g`** — translator için placeholder hint.
- **String externalization** — hardcoded string’ler için Lint.
- **Locale-aware sorting** — `Collator.getInstance(locale)`.

### Çar: RTL Support

**Konular:**

- **Right-to-Left** diller (Arapça, İbranice, Farsça).
- `android:supportsRtl="true"` (manifest).
- `start`/`end` kullan, `left`/`right` değil:
  - `paddingStart` / `paddingEnd`.
  - `Compose: Modifier.padding(start = 16.dp)`.
- **`LayoutDirection.Rtl`** — Compose içinde detect.
- **Mirroring** — bazı icon’lar mirror olmalı (back arrow), bazıları olmamalı (logo).
- `android:autoMirrored="true"` (vector drawable).
- **Test:** Settings → Developer options → “Force RTL layout direction”.

### Per: Date/Time/Number/Currency Formatting

**Konular:**

- **`java.time`** (Android 8+, ya da `core-library-desugaring` ile aşağı): `LocalDate`, `LocalDateTime`, `ZonedDateTime`.
- **Locale-aware formatting:**
  - `DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM).withLocale(...)`.
  - `NumberFormat.getCurrencyInstance(Locale.GERMANY)` — €1.234,56 vs $1,234.56.
- **`DateUtils.getRelativeTimeSpanString`** — “5 dakika önce” lokalize.
- **ICU MessageFormat** — karmaşık plural/gender/select.

### Cum: Pseudo-Locale ve Test

**Konular:**

- **Pseudo-locale** (en-XA, ar-XB) — translation simülasyonu.
- `developerOptions → Language → English (XA)`.
- Görsel test: text overflow, RTL break, special character render.
- **Lint check** — `HardcodedText`.
- **Translation management:**
  - **Lokalise**, **Crowdin**, **Phrase** — translation management platforms.
  - In-app translator hint: `Translator's note`.

### Cmt-Paz: Pratik + Tekrar

NowInAndroid klonunu 3 dilde lokalize et: TR, AR (RTL), DE.

- Plural’lar düzgün.
- RTL test pass.
- Pseudo-locale ile UI hasarı yok.

**Tekrar:**

1. `start/end` ile `left/right` arasındaki fark — neden ilki tercih?
1. Plural’lar Türkçe’de nasıl, İngilizce’den farkı?
1. ICU MessageFormat ne zaman gerekli?

-----

## Hafta 26 — İleri Coroutines (Canavar Olma Haftası)

> **Daha fazla derinlik.** Hafta 4-5’in üzerine.

### Pzt-Sal: Flow Internals

**Konular:**

- `Flow<T>` interface’inin implementation’ı — `AbstractFlow`, `SafeFlow`.
- `collect` zinciri — operator’lar nasıl chain ediliyor?
- **`SafeCollector`** — `flow { }` builder’ında collect context check’i.
- **`ChannelFlow`** — `channelFlow`, `callbackFlow`, `flowOn` ortak parent.
- **`SharedFlow` source code** — replay buffer, slot-based subscribers.
- **`StateFlow` source code** — `MutableStateFlowImpl`, equality check.

**Pratik:** kotlinx-coroutines source’undan `StateFlow.kt` ve `SharedFlow.kt`’yi oku. `setValue` operasyonu nasıl thread-safe?

### Çar: Custom Coroutine Builders ve Scope Management

**Konular:**

- Custom CoroutineScope yazma — feature-scoped scope.
- **`CoroutineScope.cancel()` semantic** — scope cancel’da Job ne olur?
- **Memory leak risk:** `CoroutineScope(SupervisorJob())` ile başlat, hiç cancel etme — leak.
- `viewModelScope` source code — `ViewModel.viewModelScope` extension.
- `lifecycleScope` source code.
- Custom scope ile feature-level cancellation.

### Per: Backpressure Stratejileri

**Konular:**

- **Hot producer + slow consumer** problemi.
- `buffer(capacity, BufferOverflow.SUSPEND/DROP_OLDEST/DROP_LATEST)`.
- `conflate()` — `buffer(0, DROP_OLDEST)`.
- `collectLatest { }` — yeni emission iç işi cancel eder.
- **`Channel.RENDEZVOUS`** vs `Channel.BUFFERED` semantic.
- **Real-world:** sensor data (accelerometer 100Hz, UI 60Hz).

### Cum: Coroutine Debugging ve Testing İleri

**Konular:**

- `-Dkotlinx.coroutines.debug` — JVM flag, coroutine name’i exception’larda.
- **IntelliJ Coroutine Debugger** — coroutine view, suspend point step.
- `kotlinx.coroutines.debug.DebugProbes.install()`.
- **Test patterns:**
  - `runTest`’te `currentTime` virtual time.
  - `pauseDispatcher()`, `resumeDispatcher()` (deprecated → `StandardTestDispatcher`).
  - Multiple dispatcher injection — `@TestDispatcher`, `@IoDispatcher`.

### Cmt-Paz: Mutex, Semaphore, Atomic

**Konular:**

- `Mutex` — coroutine-aware lock. `mutex.withLock { }`.
- `Semaphore` — `Semaphore(permits=10)` — max 10 paralel.
- **Atomic operations** — `kotlinx.atomicfu` (multiplatform), `AtomicInteger` (JVM).
- **Race conditions** — paralel coroutine’de shared state.
- **`@Volatile`** — JVM specific, multiplatform için atomicfu.

**Pratik:** Rate-limited API client yaz. `Semaphore(3)` ile max 3 concurrent. Test et — 4. çağrı bekler.

**Tekrar:**

1. `Mutex` vs Java `synchronized` — coroutine context’te neden ilki?
1. `flowOn` operatörü zincirin neresine etki eder, neden?
1. Hot producer slow consumer’ı nasıl çözersin (3 strateji)?

-----

## Hafta 27 — Diğer İleri Konular ve Now in Android (2. Yarı)

### Pzt: Pagination 3 Derinlemesine

**Konular (Hafta 12’nin üstüne):**

- `RemoteMediator` ile network + cache.
- `LoadType.REFRESH/PREPEND/APPEND` semantic.
- `mediatorResult.endOfPaginationReached`.
- Database-backed paging — single source of truth.
- `cachedIn(viewModelScope)` — config change survival.
- Compose `LazyPagingItems.refresh()`, `retry()`.

### Sal: WebSocket, SSE, gRPC Pratik

**Konular:**

- **OkHttp WebSocket:** `WebSocket.Listener`, `Flow` ile sarma.
- **SSE (Server-Sent Events):** OkHttp + custom parser, `Flow` adapter.
- **Apollo GraphQL:** schema-first, codegen, subscriptions over WebSocket.
- **gRPC Kotlin:** `proto3`, `kroto-plus` ya da `protobuf-kotlin`. Streaming RPC.

**Pratik:** Real-time chat: WebSocket connection → `Flow<Message>` → ViewModel → Compose UI.

### Çar: Media (Media3, ExoPlayer)

**Konular:**

- **Media3** — ExoPlayer’ın yeni nesli.
- `MediaSession`, `MediaController`.
- HLS, DASH, MP4, MP3 playback.
- `MediaSource` chain.
- **Background playback** — Media3 service.
- **Cast support** — Google Cast SDK.

### Per: CameraX ve ML Kit

**Konular:**

- **CameraX** — `ProcessCameraProvider`, `Preview`, `ImageCapture`, `ImageAnalysis`.
- Use case binding, lifecycle-aware.
- **ML Kit:**
  - On-device: barcode scan, face detection, text recognition.
  - Cloud: more accurate, more expensive.
- Compose entegrasyonu — `AndroidView` ile PreviewView.

### Cum-Paz: Now in Android Klonu (2. Yarı)

- `feature:topic`, `feature:interests`, `feature:settings` ekle.
- `core:notifications` — Hafta 17’den FCM entegre et.
- `core:analytics` — Hafta 23’ten Firebase Analytics.
- `sync:work` — WorkManager ile periodic sync.
- Authentication akışı (Firebase Auth ya da custom).
- **Tüm test piramidi:** unit + integration + UI + screenshot.
- **Baseline Profile** generate.
- **R8 + minify** release build.
- **Macrobenchmark** — startup + scroll.
- **Detekt + ktlint + custom Lint rule** pass.
- **GitHub Actions** CI tam pass.
- **README** + **ADR** (architecture decision records).

> **🎯 Faz 5 Çıktısı:** Firebase ekosistemini production’da kullanıyorsun. Accessibility WCAG 2.1 AA pass. i18n + RTL + locale-aware tam. Coroutines’in source code seviyesinde anlaşı var. Now in Android klonu **bitti** — bu senin portföy projen.

-----

# 🏆 FAZ 6 — SENIOR+ TAMAMLAYICI (Hafta 28-32)

## Hafta 28 — Mobile System Design

> **Senior interview’larında %50 ağırlık.**

### Pzt-Sal: Framework

**Konular:**

- **Adımlar:**
1. **Functional requirements** — feature liste, kullanıcı senaryoları.
1. **Non-functional** — latency, throughput, offline, battery, data, scale.
1. **High-level architecture** — katmanlar, modüller.
1. **Data model** — entity’ler, ilişkiler, indexler.
1. **API design** — endpoint’ler, pagination, polling/push.
1. **Caching** — memory (LRU), disk (Room), network (HTTP cache).
1. **Sync** — pull (refresh, polling), push (FCM, WebSocket), event-based.
1. **Edge cases** — network kayıp, low battery, low memory, large data.
1. **Security** — auth, encryption, key management.
1. **Observability** — crash, performance, analytics, custom metrics.

### Çar-Per-Cum: Senaryolar

Her gün 1 senaryo, 90 dakika çiz + dokümante et:

1. **Twitter timeline** — infinite scroll, refresh, write API.
1. **WhatsApp** — real-time messaging, encrypted, offline queue, multi-device.
1. **Spotify** — streaming, offline download, recommendation.
1. **Uber** — real-time location, matching, payment, driver/rider sync.
1. **Instagram** — image upload, feed, stories (24h TTL).

### Cmt-Paz: Mock Interview Pratik

- Bir kişiye senaryo anlatır gibi yaz/anlat.
- Her senaryoyu 45 dakikada bitirebilmen lazım.
- Sınırlı zamanda öncelik sıralama: must-have / nice-to-have.
- Trade-off açıkla — neden Room değil, neden polling değil.

**Kaynaklar:**

- [“System Design Interview” - Alex Xu, Vol 2 mobile bölümleri](https://www.amazon.com/dp/B08CMF2CQF)
- [Exponent - Mobile System Design](https://www.tryexponent.com/courses/system-design-mobile)
- [Engineering blogs:** Uber, Pinterest, Airbnb mobile architecture](https://eng.uber.com/)

-----

## Hafta 29 — Kotlin Multiplatform (KMP)

### Pzt: KMP Felsefesi ve Setup

**Konular:**

- KMP nedir? Code sharing — business logic, network, persistence. UI opsiyonel.
- **`expect`/`actual`** mekanizması — common’da `expect`, platform’da `actual`.
- Source set hiyerarşisi: `commonMain`, `androidMain`, `iosMain`, `jvmMain`, `jsMain`.
- Hierarchical project structure (Kotlin 1.9.20+ default).
- **Gradle setup:** `kotlin { androidTarget(); iosX64(); iosArm64(); iosSimulatorArm64() }`.
- **K2 + Compose Multiplatform** — JetBrains tarafından maintain.

### Sal-Çar: KMP Ekosistemi

**Konular:**

- **Ktor Client** — multiplatform HTTP. Engine: OkHttp (Android), Darwin (iOS), CIO (JVM).
- **SQLDelight** — multiplatform SQL, type-safe, codegen.
- **kotlinx.coroutines** — multiplatform.
- **kotlinx.serialization** — multiplatform JSON.
- **kotlinx-datetime** — multiplatform date/time.
- **Koin** — multiplatform DI (Hilt sadece Android, KMP için Koin ya da Kodein).
- **Compose Multiplatform** — UI sharing (Android, desktop, iOS, web).

### Per: iOS Interop

**Konular:**

- Swift’ten Kotlin çağırma — `KotlinNativeFramework`.
- Suspend → callback (iOS Swift’te suspend yok).
- `flowOn(Dispatchers.Main.immediate)` iOS UI’a göre.
- **SKIE** (Touchlab) — Swift-friendly Kotlin Native API’leri.
- **Limitations:** Reflection sınırlı, Java library yok.

### Cum-Paz: Pratik KMP Projesi

Mini hava durumu uygulaması:

- `commonMain`: Repository, Network (Ktor), Models, ViewModel.
- `androidMain`: Compose UI.
- `iosMain`: SwiftUI (Mac yoksa atla, ama iosMain main code’u yaz).
- Test’leri commonTest’te yaz.

**Kaynaklar:**

- [KMP docs](https://kotlinlang.org/docs/multiplatform.html)
- [KaMPKit (Touchlab)](https://github.com/touchlab/KaMPKit) — production-grade KMP template
- [Compose Multiplatform](https://www.jetbrains.com/compose-multiplatform/)

-----

## Hafta 30 — Code Review, Mentorship, Documentation

### Pzt-Sal: Code Review Felsefesi

**Konular:**

- **Ne yorumlanır:**
  - Mimari uyumsuzluk.
  - Niyet — “bu satır neyi başarmak istiyor?”.
  - Edge case eksikliği.
  - Test coverage / test kalitesi.
  - Naming — anlaşılır mı?
  - Performance / leak risk.
- **Ne yorumlanmaz (otomasyona bırak):**
  - Style — ktlint/Detekt yapsın.
  - Tipo — `// nit:` ile geçiştirme.
- **Ton:** sorgulayıcı, dayatıcı değil. “Bu yaklaşım yerine X düşündün mü?” > “Yanlış yapmışsın”.
- **Senior asks why** — junior copy-paste eder, senior gerekçe ister.

**Kaynaklar:**

- [Google Code Review Developer Guide](https://google.github.io/eng-practices/review/)
- [Conventional Comments](https://conventionalcomments.org/)

### Çar: Yaygın Anti-Pattern’ler

- **God Object** — her şeyi yapan ViewModel/Repository.
- **Service Locator** Singleton — DI yerine.
- **Boolean flag patlaması** — `if (isLoading && !isError && data != null)`. Sealed class kullan.
- **Nested callback hell** — suspend/Flow’a çevir.
- **String’lerle navigation** — type-safe route.
- **`!!` operatörü** — Kotlin’in en kötü düşmanı, refactor zorunluluğu.
- **Magic number** — `if (count > 5)` → `MAX_RETRIES`.
- **Deeply nested if/else** — early return, sealed when.
- **God activity** — Activity her şeyi bilir. ViewModel’e böl.
- **Logging her yere** — structured logging stratejisi.

### Per: Documentation Standartları

**Konular:**

- **KDoc syntax** — `@param`, `@return`, `@throws`, `@sample`, `@see`.
- Public API’lerde `@VisibleForTesting`.
- `@Deprecated(message, replaceWith = ReplaceWith("..."))`.
- **README anatomy:** badge’ler, kurulum, mimari diyagram, contribution guide.
- **ADR (Architecture Decision Records):** her büyük kararın kaydı.
  - Context, Decision, Consequences.
  - `docs/adr/0001-use-compose-for-ui.md`.
- **Module README** — her modülün kendi README’si.
- **Mermaid diagrams** — markdown içi flowchart, sequence.

### Cum: Mentorship Pratikleri

- Junior’a bilmediği bir şey gösterirken kullanılan dil.
- “Pair programming” oturumu — junior driver, sen navigator.
- Code review ile öğretme — soru sorarak yönlendir.
- Architecture explanation — beyaz tahta egzersizi.

### Cmt-Paz: Pratik

GitHub’da aktif bir Android projesinin (NowInAndroid, Tivi) son 20 PR’ını incele:

- Hangi yorumlar mimari?
- Hangi yorumlar style?
- Senior reviewer’ların yorum stilini analiz et.

ADR yazma pratiği: NowInAndroid klonun için 5 ADR yaz.

-----

## Hafta 31 — Edge Konular ve Boşluk Doldurma

### Pzt: Custom Lint Rules

**Konular:**

- Lint API: `Detector`, `IssueRegistry`.
- Architecture enforcement — “feature module’lerden direkt data layer çağrısı yasak”.
- “TODO comment release build’de yasak” rule.
- “Hardcoded color usage’a uyarı”.
- Lint check’leri Gradle plugin olarak paketleme.

### Sal: Custom Detekt Rules

**Konular:**

- `Rule` extend, AST traversal.
- Use case: `runBlocking` kullanımına uyarı, GlobalScope yasakla.

### Çar: App Startup Library, Initializer Pattern

**Konular:**

- `androidx.startup` — ContentProvider auto-init.
- `Initializer<T>` interface — dependency declaration.
- Order: `dependencies()` graph.
- WorkManager, FCM gibi init’leri merkezde topla.

### Per: Layout Inspector, Compose Debug Tools

**Konular:**

- Layout Inspector → recomposition counts, skipped count.
- `Compose.Layout` dump.
- **Live Edit** — Compose hot reload.
- **Compose Preview** — interactive, multi-config.

### Cum: A/B Testing ve Experimentation

**Konular:**

- Firebase A/B Testing.
- Custom experimentation framework.
- Statistical significance — variant’lar.
- Feature flag → experiment workflow.

### Cmt: Privacy Sandbox ve Modern Android

**Konular:**

- **Privacy Sandbox on Android** (2024+) — SDK Runtime, attribution reporting.
- **Health Connect** — health data API.
- **Predictive Back gesture** — Android 13+.
- **Per-app language preferences** — Android 13+.
- **Photo Picker** — Android 13+.
- **Foreground Service Types** — Android 14 zorunlulukları.

### Paz: Kapanış ve Refleksiyon

- 32 hafta öncesi seninle şimdiki seni kıyasla.
- Hangi konular hâlâ “ezbere” hissediyor?
- Spaced repetition planın nasıl?

-----

## Hafta 32 — Final Hazırlık ve Sürekli Öğrenme Planı

### Pzt-Sal: Portföy Cilası

- Now in Android klon repository’sini polish:
  - README mükemmel.
  - GIF/screenshot.
  - Architecture diagram (mermaid).
  - ADR’ler.
  - CONTRIBUTING.md.
  - GitHub Actions badge’leri.
- LinkedIn / personal site update.

### Çar: Interview Hazırlık

**Konular:**

- **Coding round** — LeetCode (Kotlin’de). Easy/Medium array, string, hash map. Hard nadiren.
- **Mobile system design** — Hafta 28 senaryolarını gözden geçir.
- **Behavioral** — STAR formatında 8-10 hikaye hazırla:
  - Conflict resolution, technical decision, mentorship, failure.
- **Technical deep dive** — Coroutines, Flow, Compose, Architecture sorular.

### Per: Mock Interviews

- Pramp, Exponent, peer ile mock.
- Feedback al, zayıf noktayı 1 hafta çalış.

### Cum: Sürekli Öğrenme Planı

- **Haftalık:** Android Weekly, Kotlin Weekly newsletter.
- **Aylık:** 1 yeni library/tool deep dive.
- **Çeyreklik:** 1 side project (KMP, Compose Desktop, Compose for Web).
- **Yıllık:** Kotlin Conf / Droidcon talk seyret.
- **Sürekli:** GitHub’da NowInAndroid, Tivi, Sky, Reply gibi projeleri izle.

### Cmt-Paz: Kutlama ve İlk Senior Görevin

32 hafta bitti. **Tebrikler.** Artık:

- Now in Android’i sıfırdan yazabilirsin.
- AI’ın kodunu satır satır kritik edebilirsin.
- Mimari karar verebilirsin.
- Junior’a mentor’luk edebilirsin.
- Senior interview’larında bar’ı geçecek seviyedesin.

-----

- [ ] Macrobenchmark + Baseline Profile workflow’u var, cold startup optimize ettim.
- [ ] LeakCanary ile memory leak tespit edip fix edebiliyorum.
- [ ] Firebase Crashlytics + Performance + Analytics + Remote Config kurdum.
- [ ] Güvenlik: EncryptedSharedPreferences, certificate pinning, R8 full mode, Play Integrity.
- [ ] WorkManager + FCM + deep link + foreground service production-grade.

## Faz 4 sonu — “Senior seviyesindeyim.”

- [ ] 10+ modüllü multi-module mimari kurabiliyorum.
- [ ] Convention plugin’lerle her modül 5-10 satır `build.gradle.kts`.
- [ ] Custom Lint rule ile architecture enforcement.
- [ ] App Bundle + R8 full mode + resource shrinking ile app size optimize.
- [ ] GitHub Actions CI/CD: build, test, lint, signing, Play Publisher.
- [ ] Fastlane / Firebase App Distribution ile beta dağıtım.
- [ ] Compose custom layout (FlowRow, sıfırdan Layout API).
- [ ] Modifier.Node API ile custom modifier.
- [ ] Compose Compiler reports → skippable olmayan composable’lar fix.
- [ ] Now in Android klonunun ilk yarısı bitti.

## Faz 5 sonu — “Senior+. Production ekosistemi tam.”

- [ ] Firebase ekosistemi: Crashlytics, Performance, Remote Config, Analytics, A/B Testing.
- [ ] Accessibility WCAG 2.1 AA pass: semantics, TalkBack, touch target, contrast.
- [ ] i18n + RTL + plural + locale-aware formatting tam.
- [ ] Coroutines source code seviyesinde: Mutex, Semaphore, SharedFlow internals.
- [ ] Now in Android klonu tamamlandı, portföyde.
- [ ] KMP’ye giriş yaptım, commonMain’de business logic yazabiliyorum.

## Faz 6 sonu — “Senior+. Interview ve mentorship hazırım.”

- [ ] Mobile system design soruları: 5 farklı senaryoyu 45 dakikada anlatabilirim.
- [ ] Code review yapabiliyorum, mimari odaklı, otomasyon dışı.
- [ ] ADR yazabiliyorum.
- [ ] Mock interview’dan geçebiliyorum.
- [ ] Portföy projem public, README + diyagram + ADR eksiksiz.
- [ ] Sürekli öğrenme planım var.

-----

# 🎓 SON SÖZ

Bu plan **32 hafta** ama gerçekçi olmak gerekirse kişiye göre **8-12 ay** sürebilir. Acele etme. Senior olmak hız değil **derinlik** meselesidir.

**Her hafta sonunda kendine 3 soru sor:**

1. Bu hafta gerçekten neyi anladım (ezbere değil, kaynak göstererek)?
1. Hangi konuyu hâlâ “sihir gibi çalışıyor” diye geçiştiriyorum?
1. Önümüzdeki haftaya hangi soruyla giriyorum?

**Bilgi unutulur, anlayış kalır. Anlayış anlatıldıkça pekişir. Pekişen anlayış senior’ı üretir.**

İyi şanslar. 🚀