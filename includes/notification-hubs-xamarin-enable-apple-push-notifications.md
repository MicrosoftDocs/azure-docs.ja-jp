
Apple Push Notification Service (APNS) を介してアプリ用にプッシュ通知を登録するには、プッシュ証明書、アプリケーション ID、プロジェクトのプロビジョニング プロファイルを Apple の開発者ポータルで作成する必要があります。

アプリ ID には、アプリでプッシュ通知を送受信できるようにする構成設定が含まれます。 これらの設定には、アプリがプッシュ通知を送受信する際に APNS の認証で必要になるプッシュ通知証明書が含まれます。

これらの概念の詳細については、[Apple Push Notification Service](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1) の公式ドキュメントを参照してください。

## <a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>プッシュ証明書用に証明書の署名要求ファイルを生成する
次の手順では、証明書の署名要求を作成するプロセスについて説明します。この署名要求によって、APNS で使用するプッシュ証明書が生成されます。

1. Mac で、キーチェーン アクセス ツールを実行します。 これは、Launchpad の **[Utilities]** フォルダーまたは **[Other]** フォルダーから開くことができます。

2. **[キーチェーン アクセス]** を選択し、**[証明書アシスタント]** を展開して、**[認証局に証明書を要求...]** を選択します。

      ![証明書の要求](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. **[ユーザーのメール アドレス]** と **[通称]** を選択します。

4. 必ず **[ディスクに保存]** を選択してから、**[続ける]** を選択します。 必要ではないため、**[CA のメール アドレス]** フィールドを空白のままにします。

      ![証明書情報](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. 証明書の署名要求 (CSR) ファイルの名前を **[名前]** ボックスに入力します。
5. **[場所]** ボックスで保存先を選択し、**[保存]** を選択します。

      ![証明書の署名要求の保存](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

      これにより、選択した場所に CSR ファイルが保存されます。 (既定の場所はデスクトップです。)このファイル用に選択した場所を忘れないでください。

## <a name="register-your-app-for-push-notifications"></a>アプリケーションをプッシュ通知に登録する
Apple でアプリケーション用の新しい明示的なアプリ ID を作成し、それをプッシュ通知用に構成します。  

1. Apple Developer Center の [iOS プロビジョニング ポータル](http://go.microsoft.com/fwlink/p/?LinkId=272456)に移動して、Apple ID でサインインします。

2. **[Identifiers (識別子)]** を選択してから、**[App IDs (アプリ ID)]** を選択します。

3. **[+]** 記号を選択して、新しいアプリを登録します。

      ![新しいアプリの登録](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

4. 新しいアプリの次の 3 つのフィールドを更新し、**[Continue (続行)]** を選択します。

   * **[Name (名前)]**: **[App ID Description (アプリ ID の説明)]** セクションで、アプリのわかりやすい名前を入力します。

   * **[Bundle Identifier (バンドル ID)]**: **[Explicit App ID (明示的なアプリ ID)]** セクションに、[アプリ ディストリビューション ガイド](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8)で説明されているように `<Organization Identifier>.<Product Name>` の形式で**バンドル ID** を入力します。 これは、アプリの XCode、Xamarin、または Cordova プロジェクトで使用されているものと一致する必要があります。

   * **[Push Notifications (プッシュ通知)]**: **[App Services (アプリ サービス)]** セクションの **[Push Notifications (プッシュ通知)]** オプションを選択します。

     ![アプリ ID の登録](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

5. **[Confirm your App ID (アプリ ID の確認)]** 画面で設定を確認します。 正しいことを確認したら **[Register (登録)]** を選択します。

6. 新しいアプリ ID を送信すると、**[Registration complete (登録完了)]** 画面が表示されます。 **[完了]**を選択します。

7. Developer Center で、**[App ID (アプリ ID)]** の一覧から、作成したアプリ ID を見つけ、その行を選択します。 アプリ ID の行を選択すると、アプリの詳細が表示されます。 次に、画面の下部にある **[Edit (編集)]** を選択します。

8. 画面の下部までスクロールし、**[Development SSL Certificate (開発 SSL 証明書)]** セクションの **[Create Certificate... (証明書の作成...)]** ボタンを選択します。

      ![開発プッシュ SSL 証明書](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

 これで、[Add iOS Certificate] アシスタントが表示されます。

   > [!NOTE]
   > このチュートリアルでは開発証明書を使用します。 運用証明書を登録する場合も同じ処理を行います。 通知を送信する際と同じ証明書の種類を使用するようにします。
   >

9. **[Choose File (ファイルの選択)]** を選択して、プッシュ証明書用に CSR を保存した場所に移動します。 次に、**[Generate (生成)]** を選択します。

      ![証明書の生成](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

10. ポータルで証明書が作成されたら **[Download (ダウンロード)]** ボタンを選択します。

      ![証明書の準備の完了](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

       これによって、署名証明書がダウンロードされ、コンピューターの Downloads フォルダーに保存されます。

      ![ダウンロード フォルダー](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

   > [!NOTE]
   > 既定では、ダウンロードした開発証明書ファイルの名前は **aps_development.cer** になっています。
   >
   >
11. ダウンロードしたプッシュ証明書 **aps_development.cer** をダブルクリックします。 次のスクリーンショットのように、新しい証明書がキーチェーンにインストールされます。

       ![キーチェーン アクセス](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

   > [!NOTE]
   > 証明書の名前は異なることがありますが、名前の前に **Apple Development iOS Push Services** が付きます。
   >
   >
12. **キーチェーン アクセス**の **[証明書]** カテゴリで、作成した新しいプッシュ証明書を選択します。 **[書き出す]** を選択し、ファイルに名前を付けて、**.p12** 形式を選択します。次に、**[保存]** を選択します。

    エクスポートした .p12 証明書のファイル名と場所を記録します。 これは、Azure クラシック ポータルにアップロードして APNS による認証を有効にするために使用します。 **.p12** 形式のオプションが利用できない場合には、キーチェーン アクセス ツールの再起動が必要になることがあります。

## <a name="create-a-provisioning-profile-for-the-app"></a>アプリケーションのプロビジョニング プロファイルを作成する
1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS プロビジョニング ポータル</a>に戻って **[Provisioning Profiles (プロビジョニング プロファイル)]**、**[All (すべて)]**、**+** ボタンの順に選択して、プロファイルを作成します。 これにより、**iOS プロビジョニング プロファイルの追加**ツールが起動します。

      ![プロビジョニング プロファイル](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. **[Development (開発)]** でプロビジョニング プロファイルの種類として **[iOS App Development (iOS アプリ開発)]** を選択し、**[Continue (続行)]** を選択します。

3. **[App ID (アプリ ID)]** ボックスの一覧で、作成したアプリ ID を選択し、**[Continue (続行)]** を選択します。

      ![アプリ ID の選択](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

4. **[Select certificates (証明書の選択)]** 画面で、コードの署名に使用する開発証明書を選択して、**[Continue (続行)]** を選択します。 これは署名証明書であり、作成したプッシュ証明書ではありません。

       ![Signing certificate](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

5. テストに使用する**デバイス**を選択し、**[Continue (続行)]** を選択します。

     ![プロビジョニング ファイルの追加](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

6. 最後に、**[Profile Name (プロファイル名)]** でプロファイルの名前を選択し、**[Generate (生成)]** を選択します。

      ![プロファイルの名前](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
