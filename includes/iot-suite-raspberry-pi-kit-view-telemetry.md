## <a name="view-the-telemetry"></a>テレメトリを表示する

これで、Raspberry Pi からリモート監視ソリューションにテレメトリが送信されるようになりました。 テレメトリはソリューション ダッシュボードで確認できます。 また、ソリューション ダッシュボードから Raspberry Pi にメッセージを送信することもできます。

- ソリューション ダッシュボードに移動します。
- **[表示するデバイス:]** ボックスの一覧からデバイスを選択します。
- Raspberry Pi からのテレメトリがダッシュボードに表示されます。

![Raspberry Pi からのテレメトリを表示する][img-telemetry-display]

## <a name="act-on-the-device"></a>デバイスを操作する

ソリューション ダッシュボードから、Raspberry Pi でメソッドを呼び出すことができます。 Raspberry Pi は、リモート監視ソリューションに接続したときに、サポートしているメソッドに関する情報を送信します。

- ソリューション ダッシュボードで、**[デバイス]** をクリックし、**[デバイス]** ページにアクセスします。 **[デバイス一覧]** で Raspberry Pi を選択します。 次に、**[方法]** を選択します。

    ![ダッシュボードでデバイスを一覧表示する][img-list-devices]

- **[メソッドの呼び出し]** ページで、**[方法]** ボックスの一覧から **[LightBlink]** を選択します。

- **InvokeMethod** を選択します。 Raspberry Pi に接続された LED が数回点滅します。 Raspberry Pi 上のアプリから、ソリューション ダッシュボードに受信確認が送信されます。

    ![メソッドの履歴を表示する][img-method-history]

- **ChangeLightStatus** メソッドを使用して、LED のオンとオフを切り替えることができます。この際、**LightStatusValue** はオンの場合は **1** に設定され、オフの場合は **0** に設定されています。

> [!WARNING]
> Azure アカウントでリモート監視ソリューションを実行したままにしておくと、実行時間分が課金されます。 リモート監視ソリューション実行中の使用料金を削減する方法の詳細については、「[Configuring Azure IoT Suite preconfigured solutions for demo purposes (デモの目的で Azure IoT Suite 構成済みソリューションを構成する)][lnk-demo-config]」をご覧ください。 ソリューションの使用を終了するときに、Azure アカウントから事前構成済みソリューションを削除してください。


[img-telemetry-display]: media/iot-suite-raspberry-pi-kit-view-telemetry/telemetry.png
[img-list-devices]: media/iot-suite-raspberry-pi-kit-view-telemetry/listdevices.png
[img-method-history]: media/iot-suite-raspberry-pi-kit-view-telemetry/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md