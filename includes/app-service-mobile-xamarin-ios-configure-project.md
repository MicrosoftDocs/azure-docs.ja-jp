#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Xamarin Studio での iOS プロジェクトの構成
1. Xamarin.Studio で **Info.plist** を開き、新しい App ID で前に作成した Bundle ID で **[Bundle Identifier (バンドル識別子)]** を更新します。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. 下にスクロールして **[Background Modes (バックグラウンド モード)]** に移動します。 **[Enable Background Modes (バックグラウンド モードを有効にする)]** と **[Remote notifications (リモート通知)]** の各チェック ボックスをオンにします。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. ソリューション パネルでプロジェクトをダブルクリックし、**[Project Options (プロジェクト オプション)]** を開きます。
4. **[Build (ビルド)]** で **[iOS Bundle Signing (iOS バンドル署名)]** を選択し、対応する ID とこのプロジェクトに対して設定したプロビジョニング プロファイルを選択します。

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   これで、プロジェクトではコード署名のために新しいプロファイルを使用するようになります。 公式の Xamarin デバイス プロビジョニングのドキュメントについては、 [Xamarin デバイス プロビジョニング]に関するページを参照してください。

#### <a name="configure-the-ios-project-in-visual-studio"></a>Visual Studio での iOS プロジェクトの構成
1. Visual Studio で、プロジェクトを右クリックし、 **[プロパティ]**をクリックします。
2. [プロパティ] ページで、**[iOS アプリケーション]** タブをクリックし、前に作成した ID で **[識別子]** を更新します。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. **[iOS Bundle Signing (iOS バンドル署名)]** タブで、対応する ID とこのプロジェクトに対して設定したプロビジョニング プロファイルを選択します。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    これで、プロジェクトではコード署名のために新しいプロファイルを使用するようになります。 公式の Xamarin デバイス プロビジョニングのドキュメントについては、 [Xamarin デバイス プロビジョニング]に関するページを参照してください。
4. Info.plist をダブルクリックして開き、**[Background Modes (バックグラウンド モード)]** の **RemoteNotifications** を有効にします。

[Xamarin デバイス プロビジョニング]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


<!--HONumber=Dec16_HO2-->


