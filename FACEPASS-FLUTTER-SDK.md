## Suporte

| Android | iOS |
| :-----: | :-: |
|   ✅    | ✅  |

## Pré-requisitos

- Flutter >= 3.22.0
- Dart >= 3.4.0 <4.0.0
- iOS >= 15.5
- Java 17
- Android Gradle Plugin >=8.1.0
- Gradle wrapper >= 8.3-all
- Android ndkVersion = 25.1.8937393
- Android `compileSDK` >= 35
- Android minSdk >= 24
- Kotlin version >= 1.9.0
#### Exemplo de como informar a versão do Kotlin  
`id "org.jetbrains.kotlin.android" version "1.9.0" apply false`
Ou `'ext.kotlin_version = '1.6.10'`

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

## Permissões Android
### Adicione as permissões abaixo no aquivo android/app/src/main/AndroidManifest.xml
```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```
## Permissões iOS
### Adicione as permissões abaixo no aquivo ios/Runner/Info.plist

```xml
<key>NSCameraUsageDescription</key>
<string>Premissão de camera para validação facial</string>
<key>NSMicrophoneUsageDescription</key>
<string>Usado para camera na validação facial</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Permissão para uso do GPS para validação facial</string>
```

## Instalação

```yaml
dependencies:
  facepass_flutter_sdk:
    git: 
      url: https://<TOKEN_FACEPASS>@github.com/Facepass-Brasil/fp-flutter-vision-suite.git
      ref: <LAST_TAG>
      path: ./facepass_flutter_sdk
```
#### `TOKEN_FACEPASS deve ser solicitado para a equipe Facepass` (NÃO DEVE SER COMPARTILHADO)

## Como usar MOBILE
### Inicialização do SDK
```dart
void main() {
  WidgetsFlutterBinding.ensureInitialized(); // O WidgetsFlutterBinding deve ser iniciado antes do início do SDK
  FacepassFlutterSdk.I.init(
    mobileApiKey: <API_KEY_MOBILE_FACEPASS>,
  );
  runApp(const MyApp());
}
```
#### `API_KEY_MOBILE_FACEPASS deve ser solicitado para a equipe Facepass` (NÃO DEVE SER COMPARTILHADO)
**Recomendamos fortemente use o parametro de build --dart-define-from-file para guardar chave de apiKey em de maneira segura**
**Não suba dados críticos em repositórios git**

### Validação liveness
```dart
  try {
    final UserValidationResponse? result =
        await FacepassFlutterSdk.I.liveness(context);
    //Caso o result retorne null é por que o usuário somente abriu e fechou a tela
    // Codifique os próximos passos a partir daqui
   
   // validationId será usado para a validação executada
   // pelo back-end de domínio de você cliente.
   // Como fazer será explicado mais a baixo.
    print("ValidationId: ${result?.validationId}"); 
    print("Número de tentativas: ${result?.attempts}");
  } catch (e) {
    //Tratamento de failures
    /**
     * FAILURES MAPEADAS
     * MobileFailure
     * ServerFailure
     * NoNetworkFailure
     * FaceNotFoundFailure
     * UserNotFoundFailure
     * LivenessNotValidFailure
     * DeepfakeNotValidFailure
     * InvalidMetadataValidFailure
     * InvalidApiKeyValidFailure
     * DeviceNotApprocedFailure
     */
    // CONTUDO PODE SER QUALQUER OUTRO TIPO DE EXCEPTION NÃO MAPEADA. NÃO TRATE SOMENTE OS ERROS MAPEADOS
  }
```

### Parametros do metodo FacepassFlutterSdk.I.init
| Nome do Parâmetro | Tipo          | Obrigatório | Descrição                         | Valores Possíveis                     |
|-------------------|---------------|-------------|-----------------------------------|---------------------------------------|
| `texts`           | `FacepassFlutteSdkTexts`| Não         | Texto para traduções              | -                                     |
| `debug`           | `bool`        | Não         | Habilita logs aprimorados         | true/false `default false`            |
| `apiKey`          | `String`      | Sim         | Chave de acesso (Pedir ao facepass)| -                                    |
| `sandbox`         | `bool`        | Não         | Ambiente de uso                   | true/false `default false`            |

### Parametros do metodo FacepassFlutterSdk.I.liveness
| Nome do Parâmetro | Tipo          | Obrigatório | Descrição                         | Valores Possíveis                     |
|-------------------|---------------|-------------|-----------------------------------|---------------------------------------|
| `context`         | `BuildContext`| Sim         | Contexto da aplicação             | -                                     |
| `validnessAccuracy`| `FpFvCameraValidnessAccuracy`| Não         | Nível de critério para a validação  | low/medium/high/veryhigh `default medium`|
| `cameraType`      | `FpCameraType`| Não         | Camera de início                  | front/back `default front`            |
| `showFlashOption` | `bool`        | Não         | Habilita opção para que o usuário possa habilitar o flash. `"Só irá aparecer a opção quando for a câmera traseira"` | true/false `default true`|
| `showFlipCameraOption`| `bool`    | Não         | Habilita opção para que o usuário possa trocar entre a câmera traseira ou dianteira | true/false `default false`|
| `minFaceApproachPercent`| `double`| Não         | Distância mínima que o usuário deve estar do device  | Min: 10 Max: 80 `default 40`            |


## Como usar BACKEND
Após a parte mobile concluir todas as etapas necessárias de validação, será retornado um objeto `UserValidationResponse` em caso de sucesso. Esse objeto conterá um atributo chamado `validationId`, que será utilizado para obter as informações referentes à validação facial realizada.

Do lado do backend do domínio do cliente, será necessário realizar uma requisição ao serviço do **Facepass** para receber os dados previamente validados.

### Exemplo de request
```curl
curl --request GET \
  --url <URL_BACKEND_FACEPASS>/v1/external/users/validate/8d9cd4a1-089a-40ce-bd01-ad0377c5bg74 \
  --header 'User-Agent: insomnia/9.3.0' \
  --header 'x-api-key: <API_KEY_BACKEND_FACEPASS>'
```
#### `API_KEY_BACKEND_FACEPASS deve ser solicitado para a equipe Facepass` (NÃO DEVE SER COMPARTILHADO)

### Exemplo de response
```json
{
    "externalId": "<ID_EXTERNO_DO_CLIENTE>",
    "name": "<NOME_DO_CLIENTE>",
    "documentValue": "<CPF_DO_CLIENTE>",
    "success": true, /*Quando true a validação ocorreu com sucesso. Sempre valide se está true.*/
    "errorMsg": null
}
```

