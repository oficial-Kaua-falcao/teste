# Presença QR — Android nativo

Aplicativo Android nativo em Kotlin + Jetpack Compose. Não há HTML, WebView, navegador ou servidor web. A sincronização entre dispositivos usa Firebase Authentication anônima e Cloud Firestore; QR Codes são gerados localmente com ZXing; a leitura usa CameraX + ML Kit.

## Configuração do Firebase

O projeto já contém `app/google-services.json` do aplicativo Android `com.example.presencaqr`. No Firebase Console, habilite **Authentication > Sign-in method > Anonymous** e crie um banco **Cloud Firestore**. Publique as regras de `firestore.rules` no banco. Sem essas etapas, o cadastro e a sincronização serão rejeitados pelo Firebase.

## Compilar

1. Abra esta pasta no Android Studio recente (Hedgehog ou superior).
2. Aguarde a sincronização do Gradle.
3. Execute `./gradlew assembleDebug` ou use **Build > Build APK(s)**.
4. O APK ficará em `app/build/outputs/apk/debug/app-debug.apk`.

O ambiente de entrega não possuía SDK Android/Gradle pré-instalados no momento da criação, portanto o APK deve ser gerado no Android Studio ou em uma máquina com SDK configurado. Não declarar testes de dispositivo como executados sem executá-los.

## Uso entre dispositivos

Na primeira abertura, cada aparelho pede o nome do usuário e cria uma autenticação anônima persistente. Depois:

1. Um organizador cria um evento.
2. No detalhe, toca em **Criar convite QR do evento** e mostra ou compartilha a imagem.
3. Em outro celular, a pessoa toca em **Ler convite QR** ou usa **Entrar por código**.
4. O aparelho entra no mesmo evento e os dados são sincronizados pelo Firestore.
5. A pessoa toca em **Confirmar minha presença**. A gravação usa o ID anônimo do usuário e não permite duplicidade.

O QR individual local permanece disponível no código Room legado. Para o fluxo multi-dispositivo, use o convite QR do evento e a confirmação autenticada no Firestore, sem exigir contato direto entre os aparelhos.

## Sincronização entre dispositivos Android

O componente `LocalSyncServer` continua disponível como alternativa LAN, mas o fluxo principal agora usa Firestore, permitindo dispositivos em redes diferentes. A confirmação só é mostrada após a transação do Firestore concluir.

## Limitações explícitas

O APK deve ser recompilado após a integração do Firebase. As regras incluídas limitam escrita a usuários autenticados, permitem que cada usuário altere apenas seu próprio perfil e tornam a presença imutável depois do primeiro registro.
