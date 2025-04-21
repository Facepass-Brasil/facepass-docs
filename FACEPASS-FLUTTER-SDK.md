## Suporte

| Android | iOS |
| :-----: | :-: |
|   ✅    | ✅  |

## Pré-requisitos

- Flutter >= 3.22.0
- Dart >= 3.4.0 <4.0.0
- iOS >= 15.5
- Android `compileSDK` 34
- Java 17
- Android Gradle Plugin >=8.1.0
- Gradle wrapper >= 8.3-all
- ndkVersion >= 25.1.8937393
- Android compileSdk >= 35
- Android minSdk >= 24

## Configurar o java 17 no flutter
### No arquivo android/app/build.graddle adicione:
```groovy
...
compileOptions {
    sourceCompatibility = JavaVersion.VERSION_17
    targetCompatibility = JavaVersion.VERSION_17
}

kotlinOptions {
    jvmTarget = JavaVersion.VERSION_17
}
...
```

## Retro compatibilidade com o java 11
### No arquivo android/app/build.graddle adicione:
```groovy
...
compileOptions {
    coreLibraryDesugaringEnabled true //Habilite o Desugaring adicionando esse parametro 
    sourceCompatibility = JavaVersion.VERSION_1_8
    targetCompatibility = JavaVersion.VERSION_1_8
}

kotlinOptions {
    jvmTarget = JavaVersion.VERSION_1_8
}
...
//Adicione também a depêndencia abaixo:
dependencies {
  coreLibraryDesugaring 'com.android.tools:desugar_jdk_libs:1.2.2'
}

```
