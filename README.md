# Flutter-Localization-Sample

This example demonstrates how to use localization in flutter application.

## Add required library under dependencies section in pubspec file

```gradle
flutter_localizations:
    sdk: flutter
```

## Create Json Files For Supported Languages

en.json For English

```json
{
  "player_name": "Player Name",
  "club_name": "Club Name",
  "country": "Country"
}
```

es.json For Spanish

```json
{
  "player_name": "Nombre del jugador",
  "club_name": "Nombre del club",
  "country": "País"
}
```

fr.json For French

```json
{
  "player_name": "Nom de joueur",
  "club_name": "Nom du club",
  "country": "Pays"
}
```

## Set Json Files Paths in pubspec file

```yaml
assets:
  - language/en.json
  - language/es.json
  - language/fr.json
```

## Create Classes For Localization

```dart
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
import 'dart:convert';

class AppLocalizations {
  final Locale locale;

  AppLocalizations(this.locale);

  static AppLocalizations? of(BuildContext context) {
    return Localizations.of<AppLocalizations>(context, AppLocalizations);
  }

  // This [delegate] will be called from MaterialApp
  static const LocalizationsDelegate<AppLocalizations> delegate =
      _AppLocalizationsDelegate();

  Map<String, String>? _localizedString;

  // This method will load the required JSON file according to locale
  Future<bool> load() async {
    String jsonStr =
        await rootBundle.loadString("assets/language/${locale.languageCode}.json");

    Map<String, dynamic> jsonMap = jsonDecode(jsonStr);

    _localizedString =
        jsonMap.map((key, value) => MapEntry(key, value.toString()));

    return true;
  }

  // This method will return the localized string for given key
  String? translate(String key) {
    print("localised: ${_localizedString?[key]}");
    return _localizedString?[key] ?? "";
  }
}

class _AppLocalizationsDelegate
    extends LocalizationsDelegate<AppLocalizations> {
  const _AppLocalizationsDelegate();

  @override
  bool isSupported(Locale locale) {
    // All supported languages
    return ['en', 'es', 'fr'].contains(locale.languageCode);
  }

  @override
  Future<AppLocalizations> load(Locale locale) async {
    AppLocalizations appLocalizations = AppLocalizations(locale);

    // The [load] method from AppLocalizations class runs here
    await appLocalizations.load();
    return appLocalizations;
  }

  @override
  bool shouldReload(covariant LocalizationsDelegate<AppLocalizations> old) {
    return false;
  }
}
```

```dart
import 'package:flutter/material.dart';
import 'package:flutterlocalizationsample/application_localizations.dart';

class ApplicationLocalizationsDelegate extends LocalizationsDelegate<AppLocalizations> {
  // This delegate instance will never change (it doesn't even have fields!)
  // It can provide a constant constructor.
  const ApplicationLocalizationsDelegate();

  @override
  bool isSupported(Locale locale) {
    // Include all of your supported language codes here
    return ['en', 'es', 'fr'].contains(locale.languageCode);
  }

  @override
  Future<AppLocalizations> load(Locale locale) async {
    // AppLocalizations class is where the JSON loading actually runs
    AppLocalizations localizations = new AppLocalizations(locale);
    await localizations.load();
    return localizations;
  }

  @override
  bool shouldReload(ApplicationLocalizationsDelegate old) => false;
}
```

## Integrate Localization in main.dart file

```dart
      supportedLocales: [
        Locale( 'en' , 'US' ),
        Locale( 'es' , 'ES' ),
        Locale( 'fr' , 'FR' ),
      ],

      localizationsDelegates: [
        AppLocalizations.delegate,
        GlobalMaterialLocalizations.delegate,
        GlobalWidgetsLocalizations.delegate,
        GlobalCupertinoLocalizations.delegate,
      ],
      locale: Locale( 'fr' , 'FR' ), // set locale for reflecting changes on app
      localeResolutionCallback: (locale, supportedLocales) {
        for (var supportedLocaleLanguage in supportedLocales) {
          if (supportedLocaleLanguage.languageCode == locale.languageCode &&
              supportedLocaleLanguage.countryCode == locale.countryCode) {
            return supportedLocaleLanguage;
          }
        }
        return supportedLocales.first;
      },
```

## Display Localized Text

```dart
Text(ApplicationLocalizations.of(context).translate("player_name"));
```
