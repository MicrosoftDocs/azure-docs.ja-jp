## <a name="view-device-telemetry-in-the-dashboard"></a>デバイスのテレメトリをダッシュボードに表示する
リモート監視ソリューションのダッシュボードでは、デバイスが IoT Hub に送信するテレメトリを表示できます。

1. ブラウザーでリモート監視ソリューションのダッシュボードに戻り、左側のパネルの **[デバイス]** をクリックし、**[デバイスの一覧]** に移動します。
2. **[デバイスの一覧]** で、デバイスの状態が **[実行中]** であることを確認してください。 実行中でない場合は、**[デバイスの詳細]** パネルで **[デバイスの有効化]** をクリックします。
   
    ![デバイスの状態を表示する][18]
3. **[ダッシュボード]** をクリックしてダッシュボードに戻り、**[表示するデバイス]** ボックスの一覧からテレメトリを表示するデバイスを選択します。 サンプル アプリケーションから送信されるテレメトリは、内部温度では 50 単位、外部温度では 55 単位、湿度では 50 単位です。
   
    ![デバイス テレメトリを表示する][img-telemetry]

## <a name="invoke-a-method-on-your-device"></a>デバイスでメソッドを呼び出す
リモート監視ソリューションのダッシュボードでは、IoT Hub を介してデバイスでメソッドを呼び出すことができます。 たとえば、リモート監視ソリューションで、デバイスの再起動をシミュレートするメソッドを呼び出すことができます。

1. リモート監視ソリューションのダッシュボードで、左側のパネルの **[デバイス]** をクリックし、**[デバイスの一覧]** に移動します。
2. **[デバイスの一覧]** で、デバイスの **[デバイス ID]** をクリックします。
3. **[デバイスの詳細]** パネルで、**[メソッド]** をクリックします。
   
    ![デバイスのメソッド][13]
4. **[メソッド]** ドロップダウンで **[InitiateFirmwareUpdate]** を選択し、**[FWPACKAGEURI]** にダミーの URL を入力します。 **[メソッドの呼び出し]** をクリックして、デバイスでメソッドを呼び出します。
   
    ![デバイスのメソッドを呼び出す][14]
   

5. デバイスがメソッドを処理しているとき、デバイス コードを実行しているコンソールにメッセージが表示されます。 メソッドの結果は、ソリューション ポータルの履歴に追加されます。

    ![メソッドの履歴を表示する][img-method-history]

## <a name="next-steps"></a>次のステップ
「[構成済みソリューションのカスタマイズ][lnk-customize]」では、このサンプルを拡張する方法をいくつか確認できます。 可能な拡張には、実際のセンサーの使用やその他のコマンドの実装などがあります。

[azureiotsuite.com サイトでのアクセス許可][lnk-permissions]の詳細について確認できます。

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[img-method-history]: ./media/iot-suite-visualize-connecting/history.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-permissions.md
