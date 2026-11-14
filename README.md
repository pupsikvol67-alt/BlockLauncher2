# BLOCK LAUNCHER

Неофициальный лаунчер для Minecraft Bedrock Edition (Android). Kotlin · Jetpack Compose · Material 3 · Storage Access Framework.
minSdk 26 (Android 8.0) · targetSdk 35 (Android 15).

## Требования
* JDK 17+ (в Android Studio встроен), Android Studio Ladybug (2024.2) или новее.
* Android SDK: платформа **API 35** (`compileSdk = 35`, `targetSdk = 35`, `minSdk = 26`) и Build-Tools — Studio предложит установить их сама (SDK Manager).
* Gradle **8.9** (подходит к AGP 8.7.3 и Kotlin 2.0.21) — скачивается автоматически по `gradle/wrapper/gradle-wrapper.properties`.
* `local.properties` с `sdk.dir` создаёт Android Studio; вручную: `sdk.dir=/путь/к/Android/Sdk` (или переменная `ANDROID_HOME`). Файл в архив не включён намеренно.

## Сборка APK
1. Откройте папку в Android Studio и дождитесь Gradle Sync.
2. `Build → Build Bundle(s) / APK(s) → Build APK(s)` либо в терминале: `./gradlew assembleDebug` (Windows: `gradlew.bat assembleDebug`)
   → `app/build/outputs/apk/debug/app-debug.apk`.
3. Release-APK подписывается debug-ключом для удобства. Для публикации укажите свой keystore в `app/build.gradle.kts`.

### Если нет `gradle/wrapper/gradle-wrapper.jar`
Бинарный JAR не входит в архив (его нельзя честно получить без доступа к сети). Android Studio работает и без него.
Для командной строки создайте его один раз, имея установленный Gradle 8.9+: `gradle wrapper --gradle-version 8.9`
(команда также заменит `gradlew`/`gradlew.bat` официальными версиями).

## Что реально работает
| Функция | Как |
|---|---|
| ИГРАТЬ | окно: версия игры, «Запустить» (`getLaunchIntentForPackage`), проверка обновлений в Google Play, бета-тест, резервные копии всех миров |
| Версия Minecraft | `PackageManager` (`<queries>` в манифесте) |
| Импорт .mcaddon/.mcpack/.mcworld | выбор файла через SAF → копия в библиотеку лаунчера, разбор manifest.json, иконки, описания |
| Установка в игру | `ACTION_VIEW` + `FileProvider` → Minecraft сам показывает импорт (единственный легальный способ на Android) |
| Моды/текстуры | список, поиск, категории, описание, обложка (pack_icon), предпросмотр, экспорт, удаление из лаунчера |
| Миры | папка `games/com.mojang` через `ACTION_OPEN_DOCUMENT_TREE`: список, обложки, экспорт в .mcworld, бэкап, удаление, распаковка мира |
| Сборки | набор модов/текстур, название, описание, обложка; экспорт/импорт `.blockbuild`; резервные копии |
| Настройки | тема (тёмная/светлая/системная), язык RU/EN, анимации, очистка кеша, «О лаунчере» |

## Ограничения Android (честно)
* Лаунчер **не может** сам включать/выключать паки в мире или удалять установленное из Minecraft — это делается внутри игры.
* Android 11+ закрывает `Android/data` для SAF на многих устройствах. Если папку миров выбрать нельзя, раздел «Миры из папки игры»
  недоступен, но импорт в игру через «В Minecraft» работает всегда.
* Разрешения хранилища не нужны (SAF), поэтому приложение их не запрашивает.
* Новости на главной — встроенные заметки о лаунчере (сетевой ленты нет, `INTERNET` не запрашивается).
* Лицензирование и защита игры не затрагиваются.

## Структура
```
app/src/main/java/com/blocklauncher/app/
  MainActivity.kt
  data/  Models, PackParser, LibraryRepository, BuildsRepository, WorldsRepository, Settings, Minecraft
  ui/    AppViewModel, Theme, Common, App (нижняя навигация),
         HomeScreen, ModsScreen, WorldsScreen, BuildsScreen, SettingsScreen
```

## Облачная сборка без Android Studio
В проект добавлен GitHub Actions workflow `.github/workflows/build-apk.yml`. Инструкция для загрузки проекта в GitHub с телефона и скачивания APK находится в `CLOUD_BUILD_GUIDE_RU.md`.
