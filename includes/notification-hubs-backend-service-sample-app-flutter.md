---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 2f8cd5e554980225850780cc474106c3dc69288a
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156339"
---
### <a name="create-the-flutter-solution"></a>Flutter ソリューションを作成する

1. **Visual Studio Code** の新しいインスタンスを開きます。

1. **コマンド パレット**を開きます (**Shift** + **Command** + **P**)。

1. **[Flutter: 新しいプロジェクト]** コマンドを選択し、**Enter** キーを押します。

1. **[プロジェクト名]** に「*push_demo*」と入力して、 **[プロジェクトの場所]** を選択します。

1. メッセージが表示されたら、 **[パッケージの取得]** を選択します。

1. **Ctrl** キーを押しながら **kotlin** フォルダー (**app** > **src** > **main** 内) を**クリック**し、 **[Finder で表示します]** を選択します。 次に、子フォルダー (**kotlin** フォルダー内) の名前を、それぞれ ```com```、```<your_organization>```、および ```pushdemo``` に変更します。

    > [!NOTE]
    > **Visual Studio Code** テンプレートを使用した場合、これらのフォルダーは既定で **com**、**example**、 **<project_name>** になります。 **組織**で **mobcat** が使用されていると仮定すると、フォルダー構造は次のように表示されるはずです。
    >
    > - kotlin
    >     - com
    >         - mobcat
    >             - pushdemo

1. **Visual Studio Code** に戻り、**android** > **app** > **build.gradle** の **applicationId** の値を `com.<your_organization>.pushdemo` に更新します。

    > [!NOTE]
    > *<your_organization>* プレースホルダーには、自分の組織名を使用する必要があります。 たとえば、組織として **mobcat** を使用すると、**パッケージ名**の値は *com.mobcat.pushdemo* になります。

1. **src** > **debug**、**src** > **main**、および **src** > **profile** のそれぞれで、**AndroidManifest.xml** ファイルの **package** 属性を更新します。 値を、前の手順で使用した **applicationId** と一致させます。

    ```xml
    <manifest
        xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.<your_organization>.pushdemo>">
        ...
    </manifest>
    ```

1. **src** > **main** 内の **AndroidManifest.xml** ファイルの ```android:label``` 属性を *PushDemo* に更新します。 次に、```android:allowBackup``` 属性を ```android:label``` の直下に追加し、その値を **false** に設定します。

    ```xml
    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="PushDemo"
        android:allowBackup="false"
        android:icon="@mipmap/ic_launcher">
        ...
    </application>
    ```

1. アプリレベルの **build.gradle** ファイル (**android** > **app** > **build.gradle**) を開き、*compileSdkVersion* (**android** セクションから) を、API **29** を使用するように更新します。 次に、*minSdkVersion* と *targetSdkVersion* の値 (**defaultConfig** セクションから) をそれぞれ **26** と **29** に更新します。

    > [!NOTE]
    > このチュートリアルでは、**API レベル 26 以上**が実行されているデバイスのみがサポートされていますが、より前のバージョンを実行しているデバイスをサポートするように拡張できます。

1. **Ctrl** キーを押しながら **ios** フォルダーを**クリック**し、 **[Xcode で開く]** を選択します。

1. **Xcode** で**ランナー** (フォルダーではなく、上部の **xcodeproj**) をクリックします。 次に、**ランナー**のターゲットを選択し、 **[General]\(全般\)** タブを選択します。**すべての**ビルド構成を選択した状態で、 **[Bundle Identifier]\(バンドル識別子\)** を `com.<your_organization>.PushDemo` に更新します。

    > [!NOTE]
    > *<your_organization>* プレースホルダーには、自分の組織名を使用する必要があります。 たとえば、組織として **mobcat** を使用すると、 **[Bundle Identifier]\(バンドル識別子\)** の値は *com.mobcat.PushDemo* になります。

1. **[Info.plist]** をクリックし、 **[Bundle name]\(バンドル名\)** の値を **PushDemo** に更新します

1. **Xcode** を閉じ、**Visual Studio Code** に戻ります。

1. **Visual Studio Code** に戻り、**pubspec. yaml** を開いて、[http](https://pub.dev/packages/http) と [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) **Dart パッケージ**を依存関係として追加します。 次に、ファイルを保存し、メッセージが表示されたら **[パッケージの取得]** をクリックします。

    ```yaml
    dependencies:
      flutter:
        sdk: flutter

      http: ^0.12.1
      flutter_secure_storage: ^3.3.3
    ```

1. **ターミナル**で、ディレクトリを **ios** フォルダー (Flutter プロジェクト用) に変更します。 次に、[**pod install**](https://guides.cocoapods.org/using/getting-started.html#installation) コマンドを実行して、新しいポッドをインストールします ([flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) パッケージで必要)。

1. **Ctrl** キーを押しながら **lib** フォルダーを**クリック**し、メニューの **[新しいファイル]** を選択します。ファイル名は、*main_page.dart* にします。 次に、以下のコードを追加します。

    ```kotlin
    import 'package:flutter/material.dart';

    class MainPage extends StatefulWidget {
      @override
      _MainPageState createState() => _MainPageState();
    }

    class _MainPageState extends State<MainPage> {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          body: SafeArea(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.stretch,
              children: <Widget>[],
            )
          )
        );
      }
    }
    ```

1. **main.dart** で、テンプレートによるコードを次のものに置き換えます。

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/main_page.dart';

    final navigatorKey = GlobalKey<NavigatorState>();

    void main() => runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey));
    ```

1. **ターミナル**で、各ターゲット プラットフォームのアプリをビルドして実行し、テンプレートによるアプリがデバイスで実行されることをテストします。 サポートされているデバイスが接続されていることを確認してください。

    ```bash
    flutter run
    ```

### <a name="implement-the-cross-platform-components"></a>クロスプラットフォーム コンポーネントを実装する

1. **Ctrl** キーを押しながら **lib** フォルダーを**クリック**し、メニューの **[新しいフォルダー]** を選択します。 **[フォルダー名]** は、*models* にします。

1. **Ctrl** キーを押しながら **models** フォルダーを**クリック**し、メニューの **[新しいファイル]** を選択します。ファイル名は、*device_installation.dart* にします。 次に、以下のコードを追加します。

    ```dart
    class DeviceInstallation {
        final String deviceId;
        final String platform;
        final String token;
        final List<String> tags;

        DeviceInstallation(this.deviceId, this.platform, this.token, this.tags);

        DeviceInstallation.fromJson(Map<String, dynamic> json)
          : deviceId = json['installationId'],
            platform = json['platform'],
            token = json['pushChannel'],
            tags = json['tags'];

        Map<String, dynamic> toJson() =>
        {
          'installationId': deviceId,
          'platform': platform,
          'pushChannel': token,
          'tags': tags,
        };
    }
    ```

1. *push_demo_action.dart* という名前の新しいファイルを **models** フォルダーに追加します。このファイルでは、この例でサポートされているアクションを列挙したものが定義されています。

    ```dart
    enum PushDemoAction {
      actionA,
      actionB,
    }
    ```

1. **services** という名前の新しいフォルダーをプロジェクトに追加し、そのフォルダーに *device_installation_service.dart* という名前の新しいファイルを追加して、次のように実装します。

    ```dart
    import 'package:flutter/services.dart';

    class DeviceInstallationService {
      static const deviceInstallation = const MethodChannel('com.<your_organization>.pushdemo/deviceinstallation');
      static const String getDeviceIdChannelMethod = "getDeviceId";
      static const String getDeviceTokenChannelMethod = "getDeviceToken";
      static const String getDevicePlatformChannelMethod = "getDevicePlatform";

      Future<String> getDeviceId() {
        return deviceInstallation.invokeMethod(getDeviceIdChannelMethod);
      }

      Future<String> getDeviceToken() {
        return deviceInstallation.invokeMethod(getDeviceTokenChannelMethod);
      }

      Future<String> getDevicePlatform() {
        return deviceInstallation.invokeMethod(getDevicePlatformChannelMethod);
      }
    }
    ```

    > [!NOTE]
    > *<your_organization>* プレースホルダーには、自分の組織名を使用する必要があります。 たとえば、組織として **mobcat** を使用すると、[MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) の名前は *com.mobcat.pushdemo/deviceinstallation* になります。
    >
    > このクラスは、必要なデバイス インストールの詳細を取得するための、基になるネイティブ プラットフォームとの連携をカプセル化します。 [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) を使用すると、基になるネイティブ プラットフォームとの双方向非同期通信が容易になります。 このチャンネルのプラットフォーム固有の対応物は、後の手順で作成されます。

1. そのフォルダーに、*notification_action_service.dart* という名前のもう 1 つのファイルを追加して、次のように実装します。

    ```dart
    import 'package:flutter/services.dart';
    import 'dart:async';
    import 'package:push_demo/models/push_demo_action.dart';

    class NotificationActionService {
      static const notificationAction =
          const MethodChannel('com.<your_organization>.pushdemo/notificationaction');
      static const String triggerActionChannelMethod = "triggerAction";
      static const String getLaunchActionChannelMethod = "getLaunchAction";

      final actionMappings = {
        'action_a' : PushDemoAction.actionA,
        'action_b' : PushDemoAction.actionB
      };

      final actionTriggeredController = StreamController.broadcast();

      NotificationActionService() {
        notificationAction
            .setMethodCallHandler(handleNotificationActionCall);
      }

      Stream get actionTriggered => actionTriggeredController.stream;

      Future<void> triggerAction({action: String}) async {

        if (!actionMappings.containsKey(action)) {
          return;
        }

        actionTriggeredController.add(actionMappings[action]);
      }

      Future<void> checkLaunchAction() async {
        final launchAction = await notificationAction.invokeMethod(getLaunchActionChannelMethod) as String;

        if (launchAction != null) {
          triggerAction(action: launchAction);
        }
      }

      Future<void> handleNotificationActionCall(MethodCall call) async {
        switch (call.method) {
          case triggerActionChannelMethod:
            return triggerAction(action: call.arguments as String);
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > これは、通知アクションの処理を一元化する簡単なメカニズムとして使用されます。これにより、通知アクションは、厳密に型指定された列挙を使用して、クロスプラットフォーム方式で処理できます。 このサービスによって、基になるネイティブ プラットフォームは、通知ペイロードでアクションが指定されている場合に、そのアクションをトリガーできます。 また、Flutter がアクションを処理できるようになってから、アプリケーションの起動中にアクションが指定されたかどうかを、共通のコードでさかのぼって確認できるようになります。 たとえば、通知センターからの通知をタップすることによってアプリが起動される場合です。

1. **services** フォルダーに、*notification_registration_service.dart* という名前の新しいファイルを追加して、次のように実装します。

    ```dart
    import 'dart:convert';
    import 'package:flutter/services.dart';
    import 'package:http/http.dart' as http;
    import 'package:push_demo/services/device_installation_service.dart';
    import 'package:push_demo/models/device_installation.dart';
    import 'package:flutter_secure_storage/flutter_secure_storage.dart';

    class NotificationRegistrationService {
      static const notificationRegistration =
          const MethodChannel('com.<your_organization>.pushdemo/notificationregistration');

      static const String refreshRegistrationChannelMethod = "refreshRegistration";
      static const String installationsEndpoint = "api/notifications/installations";
      static const String cachedDeviceTokenKey = "cached_device_token";
      static const String cachedTagsKey = "cached_tags";

      final deviceInstallationService = DeviceInstallationService();
      final secureStorage = FlutterSecureStorage();

      String baseApiUrl;
      String apikey;

      NotificationRegistrationService(this.baseApiUrl, this.apikey) {
        notificationRegistration
            .setMethodCallHandler(handleNotificationRegistrationCall);
      }

      String get installationsUrl => "$baseApiUrl$installationsEndpoint";

      Future<void> deregisterDevice() async {
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (cachedToken == null || serializedTags == null) {
          return;
        }

        var deviceId = await deviceInstallationService.getDeviceId();

        if (deviceId.isEmpty) {
          throw "Unable to resolve an ID for the device.";
        }

        var response = await http
            .delete("$installationsUrl/$deviceId", headers: {"apikey": apikey});

        if (response.statusCode != 200) {
          throw "Deregister request failed: ${response.reasonPhrase}";
        }

        await secureStorage.delete(key: cachedDeviceTokenKey);
        await secureStorage.delete(key: cachedTagsKey);
      }

      Future<void> registerDevice(List<String> tags) async {
        try {
          final deviceId = await deviceInstallationService.getDeviceId();
          final platform = await deviceInstallationService.getDevicePlatform();
          final token = await deviceInstallationService.getDeviceToken();

          final deviceInstallation =
              DeviceInstallation(deviceId, platform, token, tags);

          final response = await http.put(installationsUrl,
              body: jsonEncode(deviceInstallation),
              headers: {"apikey": apikey, "Content-Type": "application/json"});

          if (response.statusCode != 200) {
            throw "Register request failed: ${response.reasonPhrase}";
          }

          final serializedTags = jsonEncode(tags);

          await secureStorage.write(key: cachedDeviceTokenKey, value: token);
          await secureStorage.write(key: cachedTagsKey, value: serializedTags);
        } on PlatformException catch (e) {
          throw e.message;
        } catch (e) {
          throw "Unable to register device: $e";
        }
      }

      Future<void> refreshRegistration() async {
        final currentToken = await deviceInstallationService.getDeviceToken();
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (currentToken == null ||
            cachedToken == null ||
            serializedTags == null ||
            currentToken == cachedToken) {
          return;
        }

        final tags = jsonDecode(serializedTags);

        return registerDevice(tags);
      }

      Future<void> handleNotificationRegistrationCall(MethodCall call) async {
        switch (call.method) {
          case refreshRegistrationChannelMethod:
            return refreshRegistration();
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > このクラスは、**DeviceInstallationService** の使用と、必要な登録アクションの登録、登録解除、および更新を行うためのバックエンド サービスへの要求をカプセル化します。 **apiKey** 引数が必要なのは、「[API キーを使用してクライアントを認証する](#authenticate-clients-using-an-api-key-optional)」セクションを完了した場合のみです。

1. **lib** フォルダーに、*config.dart* という名前の新しいファイルを追加して、次のように実装します。

    ```dart
    class Config {
      static String apiKey = "API_KEY";
      static String backendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
    }
    ```

    > [!NOTE]
    > これは、アプリのシークレットを定義するための簡単な方法として使用されます。 プレースホルダーの値は、実際のものに置き換えます。 バックエンド サービスを構築したときに、これらについてのメモを作成したはずです。 **API アプリの URL** は `https://<api_app_name>.azurewebsites.net/` である必要があります。 **apiKey** メンバーが必要なのは、「[API キーを使用してクライアントを認証する](#authenticate-clients-using-an-api-key-optional)」セクションを完了した場合のみです。
    >
    > これらのシークレットがソース管理にコミットされないように、gitignore ファイルに必ずこれを追加してください。

### <a name="implement-the-cross-platform-ui"></a>クロスプラットフォーム UI を実装する

1. **main_page.dart** で、**build** 関数を次のものに置き換えます。

    ```dart
    @override
    Widget build(BuildContext context) {
    return Scaffold(
        body: Padding(
          padding: const EdgeInsets.symmetric(horizontal: 20.0, vertical: 40.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.end,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: <Widget>[
              FlatButton(
                child: Text("Register"),
                onPressed: registerButtonClicked,
              ),
              FlatButton(
                child: Text("Deregister"),
                onPressed: deregisterButtonClicked,
              ),
            ],
          ),
        ),
      );
    }
    ```

1. **main_page.dart** ファイルの先頭に、必要なインポートを追加します。

    ```dart
    import 'package:push_demo/services/notification_registration_service.dart';
    import 'config.dart';
    ```

1. **_MainPageState** クラスにフィールドを追加し、**NotificationRegistrationService** への参照を格納します。

    ```dart
    final notificationRegistrationService = NotificationRegistrationService(Config.backendServiceEndpoint, Config.apiKey);
    ```

1. **_MainPageState** クラスで、 **[登録]** および **[登録解除]** ボタンの **onPressed** イベント用に、イベント ハンドラーを実装します。 対応する **Register**/**Deregister** メソッドを呼び出し、結果を示すアラートを表示します。

    ```dart
    void registerButtonClicked() async {
        try {
          await notificationRegistrationService.registerDevice(List<String>());
          await showAlert(message: "Device registered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      void deregisterButtonClicked() async {
        try {
          await notificationRegistrationService.deregisterDevice();
          await showAlert(message: "Device deregistered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      Future<void> showAlert({ message: String }) async {
        return showDialog<void>(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) {
            return AlertDialog(
              title: Text('PushDemo'),
              content: SingleChildScrollView(
                child: ListBody(
                  children: <Widget>[
                    Text(message),
                  ],
                ),
              ),
              actions: <Widget>[
                FlatButton(
                  child: Text('OK'),
                  onPressed: () {
                    Navigator.of(context).pop();
                  },
                ),
              ],
            );
          },
        );
      }
    ```

1. この時点で、**main.dart** で、ファイルの先頭に以下のインポートがあることを確認します。

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/models/push_demo_action.dart';
    import 'package:push_demo/services/notification_action_service.dart';
    import 'package:push_demo/main_page.dart';
    ```

1. **NotificationActionService** のインスタンスへの参照を格納する変数を宣言し、初期化します。

    ```dart
    final notificationActionService = NotificationActionService();
    ```

1. アクションがトリガーされたときにアラートの表示を処理する関数を追加します。

    ```dart
    void notificationActionTriggered(PushDemoAction action) {
      showActionAlert(message: "${action.toString().split(".")[1]} action received");
    }

    Future<void> showActionAlert({ message: String }) async {
      return showDialog<void>(
        context: navigatorKey.currentState.overlay.context,
        barrierDismissible: false,
        builder: (BuildContext context) {
          return AlertDialog(
            title: Text('PushDemo'),
            content: SingleChildScrollView(
              child: ListBody(
                children: <Widget>[
                  Text(message),
                ],
              ),
            ),
            actions: <Widget>[
              FlatButton(
                child: Text('OK'),
                onPressed: () {
                  Navigator.of(context).pop();
                },
              ),
            ],
          );
        },
      );
    }
    ```

1. **NotificationActionService** **actionTriggered** ストリームを観察し、アプリの起動中にキャプチャされるすべてのアクションを確認するように、**main** 関数を更新します。

    ```dart
    void main() async {
      runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey,));
      notificationActionService.actionTriggered.listen((event) { notificationActionTriggered(event as PushDemoAction); });
      await notificationActionService.checkLaunchAction();
    }
    ```

    > [!NOTE]
    > これは単に、プッシュ通知アクションの受信と伝達を示すためのものです。 通常、これらは、この例のようにアラートを表示するのではなく、暗黙のうちに処理されます。たとえば、特定のビューへの移動や、一部のデータの更新などです。
