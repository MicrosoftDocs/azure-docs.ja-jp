---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c8271cc4de558ec5c97f41d6a070f71a9fc49cd2
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060433"
---
### <a name="create-the-react-native-solution"></a>React Native ソリューションを作成する

1. `Terminal` で、次のコマンドを使用して、React Native を使用するために必要な環境ツールを更新します。

    ```bash
    # install node
    brew install node
    # or update
    brew update node
    # install watchman
    brew install watchman
    # or update
    brew upgrade watchman
    # install cocoapods
    sudo gem install cocoapods
    ```

1. `React Native` CLI がインストールされている場合は、`Terminal` で次のコマンドを実行してアンインストールします。 `npx` を使用すると、利用可能な最新バージョンの React Native CLI に自動的にアクセスできます。

    ```bash
    npm uninstall -g react-native-cli
    ```

    > [!NOTE]
    > React Native には、組み込みのコマンド ライン インターフェイスがあります。 CLI の特定のバージョンをグローバルにインストールして管理するのではなく、Node.js に付属の `npx` を使用して、実行時に現在のバージョンにアクセスすることをお勧めします。 `npx react-native <command>` を使用すると、コマンドの実行時に、CLI の現在の安定バージョンがダウンロードされて実行されます。

1. 新しいアプリケーションを作成するプロジェクト フォルダーに移動します。 `--template` パラメーターを指定して、TypeScript ベースのテンプレートを使用します。

    ```bash
    # init new project with npx
    npx react-native init PushDemo --template react-native-template-typescript
    ```

1. JavaScript バンドルの構築と、コードの更新を監視してバンドルのリアルタイムでの更新を行う Metro サーバーを実行します。

    ```bash
    cd PushDemo
    npx react-native start
    ```

1. iOS アプリを実行して、セットアップを確認します。 次のコマンドを実行する前に、iOS シミュレーターを開始済みであること、または iOS デバイスを接続済みであることを確認してください。

    ```bash
    npx react-native run-ios
    ```

1. Android アプリを実行して、セットアップを確認します。 Android エミュレーターまたはデバイスが React Native Metro サーバーにアクセスできるように構成するには、いくつかの追加の手順が必要です。 次のコマンドは、Android 用の初期の JavaScript バンドルを生成し、それを assets フォルダーに配置します。

    ```bash
    # create assets folder for the bundle
    mkdir android/app/scr/main/assets
    # build the bundle
    npx react-native bundle --platform android --dev true --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
    # enable ability for sim to access the localhost
    adb reverse tcp:8081 tcp:8081
    ```

    このスクリプトは、アプリの初期バージョンで事前にデプロイされます。 デプロイが完了したら、Metro サーバーの IP アドレスを指定して、サーバーにアクセスするようにエミュレーターまたはデバイスを構成します。 次のコマンドを実行して、Android アプリケーションをビルドして実行します。

    ```bash
    npx react-native run-android
    ```

    アプリ内で、`CMD+M` (エミュレーター) を押すか、デバイスをシェイクして開発者設定を入力し、[`Settings`] > [`Change Bundle Location`] に移動して、既定のポートを使用して Metro サーバーの IP アドレスを指定します: `<metro-server-ip-address>:8081`。

1. `App.tsx` ファイルで、ページ レイアウトに変更を適用して保存し、iOS と Android の両方のアプリに変更を自動的に反映させます。

    > [!NOTE]
    > 詳細な開発環境の設定ガイドについては、[公式ドキュメント](https://reactnative.dev/docs/environment-setup)を参照してください

### <a name="install-required-packages"></a>必要なパッケージをインストールする

このサンプルを動作させるには、次の 3 つのパッケージが必要です。

1. [iOS 用 React Native プッシュ通知](https://www.npmjs.com/package/@react-native-community/push-notification-ios) - [プロジェクト GitHub](https://github.com/react-native-community/push-notification-ios)

    このパッケージは、PushNotificationIOS が React Native のコアから分割されたときに作成されました。 このパッケージでは、iOS 用のプッシュ通知をネイティブに実装し、それにアクセスするための React Native インターフェイスを提供します。 次のコマンドを実行して、パッケージをインストールします。

    ```bash
    yarn add @react-native-community/push-notification-ios
    ```

1. [クロスプラットフォーム用 React Native プッシュ通知](https://www.npmjs.com/package/react-native-push-notification)

    このパッケージは、クロスプラットフォーム方式で iOS と Android 上でローカルおよびリモートの通知を実装します。 次のコマンドを実行して、パッケージをインストールします。

    ```bash
    yarn add react-native-push-notification
    ```

1. [デバイス情報パッケージ](https://www.npmjs.com/package/react-native-device-info) このパッケージは、実行時にデバイスに関する情報を提供します。 これを使用して、プッシュ通知の登録に使用されるデバイス ID を定義します。 次のコマンドを実行して、パッケージをインストールします。

    ```bash
    yarn add react-native-device-info
    ```

### <a name="implement-the-cross-platform-components"></a>クロスプラットフォーム コンポーネントを実装する

1. `DemoNotificationHandler` を作成して実装します。

    ```typescript
    import PushNotification from 'react-native-push-notification';

    class DemoNotificationHandler {
      private _onRegister: any;
      private _onNotification: any;

      onNotification(notification: any) {
        console.log('NotificationHandler:', notification);

        if (typeof this._onNotification === 'function') {
          this._onNotification(notification);
        }
      }

      onRegister(token: any) {
        console.log('NotificationHandler:', token);

        if (typeof this._onRegister === 'function') {
          this._onRegister(token);
        }
      }

      attachTokenReceived(handler: any) {
        this._onRegister = handler;
      }

      attachNotificationReceived(handler: any) {
        this._onNotification = handler;
      }
    }

    const handler = new DemoNotificationHandler();

    PushNotification.configure({
      onRegister: handler.onRegister.bind(handler),
      onNotification: handler.onNotification.bind(handler),
      permissions: {
        alert: true,
        badge: true,
        sound: true,
      },
      popInitialNotification: true,
      requestPermissions: true,
    });

    export default handler;
    ```

1. `DemoNotificationService` を作成して実装します。

    ```typescript
    import PushNotification from 'react-native-push-notification';
    import DemoNotificationHandler from './DemoNotificationHandler';

    export default class DemoNotificationService {
      constructor(onTokenReceived: any, onNotificationReceived: any) {
        DemoNotificationHandler.attachTokenReceived(onTokenReceived);
        DemoNotificationHandler.attachNotificationReceived(onNotificationReceived);
        PushNotification.getApplicationIconBadgeNumber(function(number: number) {
          if(number > 0) {
            PushNotification.setApplicationIconBadgeNumber(0);
          }
        });
      }

      checkPermissions(cbk: any) {
        return PushNotification.checkPermissions(cbk);
      }

      requestPermissions() {
        return PushNotification.requestPermissions();
      }

      cancelNotifications() {
        PushNotification.cancelLocalNotifications();
      }

      cancelAll() {
        PushNotification.cancelAllLocalNotifications();
      }

      abandonPermissions() {
        PushNotification.abandonPermissions();
      }
    }
    ```

1. `DemoNotificationRegistrationService` を作成して実装します。

    ```typescript
    export default class DemoNotificationService {
        constructor(
            readonly apiUrl: string,
            readonly apiKey: string) {
        }

    async registerAsync(request: any): Promise<Response> {
            const method = 'PUT';
            const registerApiUrl = `${this.apiUrl}/notifications/installations`;
            const result = await fetch(registerApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                },
                body: JSON.stringify(request)
            });

            this.validateResponse(registerApiUrl, method, request, result);
            return result;
        }

        async deregisterAsync(deviceId: string): Promise<Response> {
            const method = 'DELETE';
            const deregisterApiUrl = `${this.apiUrl}/notifications/installations/${deviceId}`;
            const result = await fetch(deregisterApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                }
            });

            this.validateResponse(deregisterApiUrl, method, null, result);
            return result;
        }

        private validateResponse(requestUrl: string, method: string, requestPayload: any, response: Response) {
            console.log(`Request: ${method} ${requestUrl} => ${JSON.stringify(requestPayload)}\nResponse: ${response.status}`);
            if (!response || response.status != 200) {
                throw `HTTP error ${response.status}: ${response.statusText}`;
            }
        }
    }
    ```

1. アプリを構成します。 `package.json` を開き、次のスクリプト定義を追加します。

    ```json
    "configure": "cp .app.config.tsx src/config/AppConfig.tsx"
    ```

    次に、このスクリプトを実行します。これにより、既定の構成が `config` フォルダーにコピーされます。

    ```bash
    yarn configure
    ```

    最後の手順では、前の手順でコピーした構成ファイルを API アクセス情報で更新します。 `apiKey` および `apiUrl` パラメーターを指定します。

    ```typescript
    module.exports = {
        appName: "PushDemo",
        env: "production",
        apiUrl: "https://<azure-push-notifications-api-url>/api/",
        apiKey: "<api-auth-key>",
    };
    ```

### <a name="implement-the-cross-platform-ui"></a>クロスプラットフォーム UI を実装する

1. ページレイアウトを定義します

    ```typescript
    <View style={styles.container}>
      {this.state.isBusy &&
        <ActivityIndicator></ActivityIndicator>
      }
      <View style={styles.button}>
        <Button title="Register" onPress={this.onRegisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
      <View style={styles.button}>
        <Button title="Deregister" onPress={this.onDeregisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
    </View>
    ```

1. スタイルを適用します

    ```css
    const styles = StyleSheet.create({
      container: {
        flex: 1,
        alignItems: "center",
        justifyContent: 'flex-end',
        margin: 50,
      },
      button: {
        margin: 5,
        width: "100%",
      }
    });
    ```

1. ページ コンポーネントを初期化します

    ```typescript
      state: IState;
      notificationService: DemoNotificationService;
      notificationRegistrationService: DemoNotificationRegistrationService;
      deviceId: string;

      constructor(props: any) {
        super(props);
        this.deviceId = DeviceInfo.getUniqueId();
        this.state = {
          status: "Push notifications registration status is unknown",
          registeredOS: "",
          registeredToken: "",
          isRegistered: false,
          isBusy: false,
        };

        this.notificationService = new DemoNotificationService(
          this.onTokenReceived.bind(this),
          this.onNotificationReceived.bind(this),
        );

        this.notificationRegistrationService = new DemoNotificationRegistrationService(
          Config.apiUrl,
          Config.apiKey,
        );
      }
    ```

1. ボタン クリック ハンドラーを定義します

    ```typescript
      async onRegisterButtonPress() {
        if (!this.state.registeredToken || !this.state.registeredOS) {
          Alert.alert("The push notifications token wasn't received.");
          return;
        }

        let status: string = "Registering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          const pnPlatform = this.state.registeredOS == "ios" ? "apns" : "fcm";
          const pnToken = this.state.registeredToken;
          const request = {
            installationId: this.deviceId,
            platform: pnPlatform,
            pushChannel: pnToken,
            tags: []
          };
          const response = await this.notificationRegistrationService.registerAsync(request);
          status = `Registered for ${this.state.registeredOS} push notifications`;
          isRegistered = true;
        } catch (e) {
          status = `Registration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }

      async onDeregisterButtonPress() {
        if (!this.notificationService)
          return;

        let status: string = "Deregistering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          await this.notificationRegistrationService.deregisterAsync(this.deviceId);
          status = "Deregistered from push notifications";
          isRegistered = false;
        } catch (e) {
          status = `Deregistration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }
    ```

1. 受信したトークンの登録とプッシュ通知を処理します

    ```typescript
      onTokenReceived(token: any) {
        console.log(`Received a notification token on ${token.os}`);
        this.setState({ registeredToken: token.token, registeredOS: token.os, status: `The push notifications token has been received.` });

        if (this.state.isRegistered && this.state.registeredToken && this.state.registeredOS) {
          this.onRegisterButtonPress();
        }
      }

      onNotificationReceived(notification: any) {
        console.log(`Received a push notification on ${this.state.registeredOS}`);
        this.setState({ status: `Received a push notification...` });

        if (notification.data.message) {
          Alert.alert(AppConfig.appName, `${notification.data.action} action received`);
        }
      }
    };
    ```