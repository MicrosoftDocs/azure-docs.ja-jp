## <a name="create-an-iot-hub"></a>IoT Hub の作成
シミュレーション対象デバイス アプリの接続先となる IoT Hub を作成します。 次の手順では、この作業を Azure ポータルで行う方法を示しています。

1. [Azure Portal][lnk-portal] にサインインします。
1. ジャンプバーで **[新規]** > **[モノのインターネット (IoT)]** > **[IoT Hub]** の順にクリックします。
   
    ![Azure portal Jumpbar][1]
1. **[IoT Hub]** ウィンドウで、IoT ハブの構成を選択します。
   
    ![[IoT Hub] ウィンドウ][2]
   
   1. **[名前]** ボックスに IoT Hub の名前を入力します。 その**名前**が有効で利用できる場合、**[名前]** ボックスに緑色のチェック マークが表示されます。
    [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]
   
   1. [[pricing and scale tier (価格とスケール レベル)]][lnk-pricing] を選択します。 このチュートリアルでは特定のレベルは必要ありません。 このチュートリアルでは、無料の F1 レベルを使用します。
   1. **[リソース グループ]** で、リソース グループを作成するか、既存のリソース グループを選択します。 詳細については、[リソース グループを使用した Azure リソースの管理][lnk-resource-groups]に関するページを参照してください。
   1. **[場所]**で、IoT Hub をホストする場所を選択します。 このチュートリアルでは、最も近い場所を選択します。
1. 必要な IoT Hub 構成オプションを選択したら、 **[作成]**をクリックします。  Azure が IoT Hub を作成するまでに数分かかる場合があります。 状態を確認するには、スタート画面または通知パネルで進行状況を監視してください。
   
1. IoT ハブが正常に作成されたら、Azure Portal で IoT ハブの新しいタイルをクリックし、新しい IoT ハブのプロパティ ウィンドウを開きます。 **ホスト名**をメモして、**[共有アクセス ポリシー]** をクリックします。
   
    ![新しい [IoT Hub] ウィンドウ][4]
1. **[共有アクセス ポリシー]** で、**[iothubowner]** ポリシーをクリックし、**[iothubowner]** ウィンドウで IoT Hub 接続文字列をコピーしてメモします。 詳細については、IoT Hub 開発者ガイドの[アクセス制御][lnk-access-control]に関するページを参照してください。
   
    ![共有アクセス ポリシー][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
