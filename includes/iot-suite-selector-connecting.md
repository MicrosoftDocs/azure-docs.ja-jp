> [!div class="op_single_selector"]
> * [Windows 上の C](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [Linux 上の C](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [mbed 上の C](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
> * [Node.JS](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> 
> 

## <a name="scenario-overview"></a>シナリオの概要
このシナリオでは、次のテレメトリをリモート監視[構成済みソリューション][lnk-what-are-preconfig-solutions]に送信するデバイスを作成します。

* 外部温度
* 内部温度
* 湿度

わかりやすくするために、デバイス上のコードではサンプル値を生成しますが、デバイスに実際のセンサーを接続し、実際のテレメトリを送信して、サンプルを拡張することをお勧めします。

このデバイスでは、ソリューション ダッシュボードから呼び出されたメソッドと、ソリューション ダッシュボードで設定されている必要なプロパティ値に応答することもできます。

このチュートリアルを完了するには、アクティブな Azure アカウントが必要になります。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、[Azure の無料試用版サイト][lnk-free-trial]をご覧ください。

## <a name="before-you-start"></a>開始する前に
デバイス用のコードを作成する前に、リモート監視構成済みソリューションをプロビジョニングし、そのソリューションに新しいカスタム デバイスをプロビジョニングする必要があります。

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>リモート監視構成済みソリューションをプロビジョニングする
このチュートリアルで作成するデバイスは、[リモート監視][lnk-remote-monitoring]構成済みソリューションのインスタンスにデータを送信します。 リモート監視構成済みソリューションを Azure アカウントにまだプロビジョニングしていない場合は、次の手順を使用します。

1. <https://www.azureiotsuite.com/> ページで、**+** をクリックしてソリューションを作成します。
2. **[リモート監視]** パネルで **[選択]** をクリックして、ソリューションを作成します。
3. **[Create Remote monitoring solution (リモート監視ソリューションの作成)]** ページで任意の**ソリューション名**を入力し、デプロイ先の**リージョン**を選択したら、使用する Azure サブスクリプションを選択します。 その後、 **[ソリューションの作成]**をクリックします。
4. プロビジョニング プロセスが完了するまで待機します。

> [!WARNING]
> 構成済みソリューションでは、課金対象の Azure サービスを使用します。 不必要な課金を避けるために、使用が済んだら、必ずサブスクリプションから構成済みソリューションを削除してください。 <https://www.azureiotsuite.com/> ページで、構成済みのソリューションをサブスクリプションから完全に削除できます。
> 
> 

リモート監視ソリューションのプロビジョニング プロセスが完了したら、 **[起動]** をクリックしてブラウザーでソリューション ダッシュボードを開きます。

![ソリューションのダッシュボード][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>リモート監視ソリューションでデバイスをプロビジョニングする
> [!NOTE]
> ソリューションにデバイスを既にプロビジョニングしている場合は、この手順を省略して構いません。 クライアント アプリケーションを作成するときに、デバイスの資格情報が必要です。
> 
> 

デバイスが構成済みソリューションに接続するには、有効な資格情報を使用して IoT Hub に対してデバイス自身の ID を証明する必要があります。 デバイスの資格情報は、ソリューション ダッシュボードから取得できます。 このチュートリアルの後半で、クライアント アプリケーションにデバイスの資格情報を含めます。

デバイスをリモート監視ソリューションに追加するには、ソリューション ダッシュボードで次の手順を実行します。

1. ダッシュボードの左下隅にある **[デバイスの追加]**をクリックします。
   
   ![デバイスを追加する][1]
2. **[カスタム デバイス]** パネルで、**[新規追加]** をクリックします。
   
   ![カスタム デバイスを追加する][2]
3. **[デバイス ID を自分で定義する]** を選択します。 **mydevice** などのデバイス ID を入力します。**[ID の確認]** をクリックして、その名前がまだ使用されていないことを確認し、**[作成]** をクリックしてデバイスをプロビジョニングします。
   
   ![デバイス ID を追加する][3]
4. デバイスの資格情報 (デバイス ID、IoT Hub ホスト名、デバイス キー) を書き留めておきます。 クライアント アプリケーションがリモート監視ソリューションに接続する際に、この値が必要になります。 次に、 **[Done]**をクリックします。
   
    ![デバイスの資格情報を表示する][4]
5. ソリューション ダッシュボードのデバイスの一覧でデバイスを選択します。 次に、**[デバイスの詳細]** パネルで、**[デバイスの有効化]** をクリックします。 現在、デバイスの状態は **[実行中]** です。 リモート監視ソリューションはデバイスからテレメトリを受信し、デバイス上でメソッドを呼び出すことができます。

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/