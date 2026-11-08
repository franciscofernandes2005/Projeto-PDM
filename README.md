# GeoChallenge

GeoChallenge é uma aplicação Android inspirada no GeoGuessr. O jogador observa
uma localização e tenta identificá-la colocando um palpite no mapa. A aplicação
inclui modos com Google Street View, panoramas guardados localmente e locais
famosos.

## Requisitos

- Android Studio
- JDK 25 para executar o Gradle (a versão está definida em
  `gradle/gradle-daemon-jvm.properties`)
- Android SDK Platform 37 para compilar a configuração atual
- Um dispositivo ou emulador com Android 10 (API 29) ou superior
- Ligação à Internet e uma chave válida do Google Maps para os mapas e o Street
  View. Os panoramas dos modos Offline e Locais Famosos estão incluídos na app;
  os mapas de palpites desses modos continuam a precisar de Internet.

O projeto define `minSdk = 29`, `compileSdk = 37` e `targetSdk = 37`.
O código Java é compilado com compatibilidade Java 11; isso não altera a versão
de JDK necessária para executar o Gradle.

## Executar

1. Abrir a pasta do projeto no Android Studio e aguardar a sincronização do
   Gradle.
2. Configurar a chave do Google Maps no recurso `google_maps_key`, em
   `app/src/main/res/values/maps_api.xml`. Usar uma chave própria, com a Maps
   SDK for Android ativada no Google Cloud e a chave restringida ao nome do
   pacote, certificado de assinatura e APIs necessárias. Não publicar a chave
   num repositório público.
3. Selecionar um emulador ou dispositivo com Android 10 ou superior e executar
   a configuração `app`.

Também é possível compilar e executar os testes unitários a partir da raiz:

```powershell
.\gradlew.bat :app:assembleDebug :app:testDebugUnitTest
```

O APK de depuração é gerado em
`app/build/outputs/apk/debug/app-debug.apk`. Pode ser copiado para um
dispositivo Android 10 (API 29) ou superior e instalado. Para instalar por ADB:

```powershell
adb install -r app\build\outputs\apk\debug\app-debug.apk
```

O APK não necessita do Android Studio no telemóvel. Os modos Online e os mapas
de palpites precisam de Internet, Google Play Services e uma chave Google Maps
válida e restringida para o pacote e o certificado usados para assinar esse
APK. Os panoramas dos modos Offline e Locais Famosos estão incluídos na app;
sem Internet, é possível ver os panoramas, mas não usar o mapa para palpitar.

## Modos de jogo

- **Mundo Online:** utiliza o Google Street View e Google Maps. Requer Internet.
- **Mundo Offline:** apresenta panoramas locais e usa o Google Maps para os
  palpites. O panorama funciona sem Internet; o mapa requer Internet.
- **Locais Famosos:** apresenta panoramas de 21 locais distintos e usa o Google
  Maps para os palpites. O panorama funciona sem Internet; o mapa requer
  Internet. Cada partida tem cinco rondas.

O histórico separa partidas concluídas e em curso. As partidas em curso podem
ser retomadas ou eliminadas. As concluídas guardam a data, o modo, a pontuação
e os detalhes das rondas.

## Dados e persistência

Os resultados e as partidas em curso são guardados localmente numa base de
dados SQLite, através de `SQLiteOpenHelper` (`GameDatabaseHelper`). Cada registo
tem um identificador gerado pela base de dados e uma data, além dos dados da
partida. O histórico permite consultar os detalhes e eliminar registos após
confirmação.

Uma partida concluída é tratada como um resultado histórico: a pontuação, a data
e os palpites são derivados do jogo e não são editáveis, para preservar a
integridade do resultado. Uma partida em curso pode ser retomada.

Os locais e as imagens panorâmicas incluídas na aplicação são recursos em
`app/src/main/assets/`. Os créditos e as licenças das imagens 360° estão em
`app/src/main/assets/famous_360/CREDITS.txt` e `ATRIBUICOES_360.txt`.

## Organização principal

- `MainActivity` e `ModeActivity`: entrada da aplicação e seleção do modo.
- `OnlineGameActivity` e `OnlineGuessActivity`: Street View online e mapa de
  palpites.
- `OfflineGameActivity` e `OfflineGuessActivity`: panoramas locais e mapa de
  palpites dos modos Offline e Locais Famosos.
- `HistoryActivity`: consulta, retoma e eliminação de partidas.
- `GameDatabaseHelper`: criação e acesso à base de dados SQLite.
- `OfflinePlacesRepository` e `OfflinePanoramaRenderer`: leitura dos locais e
  apresentação dos panoramas locais.

## Bibliotecas externas

- **AndroidX AppCompat e Activity:** compatibilidade e componentes Android
  utilizados pelas Activities.
- **Material Components:** componentes e estilos da interface.
- **Google Play Services Maps SDK:** Google Maps e Google Street View nos modos
  que dependem do serviço online.
- **JUnit e AndroidX Test/Espresso:** dependências de teste.

As versões das dependências estão declaradas em `gradle/libs.versions.toml` e
`app/build.gradle.kts`.
