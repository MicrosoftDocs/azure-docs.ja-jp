## <a name="create-an-iot-hub"></a>IoT Hub の作成
シミュレーション対象デバイス アプリの接続先となる IoT Hub を作成します。 次の手順では、この作業を Azure ポータルで行う方法を示しています。

1. [Azure Portal][lnk-portal] にサインインします。
1. ジャンプバーで **[新規]** > **[モノのインターネット (IoT)]** > **[IoT Hub]** の順にクリックします。
   
    ![Azure portal Jumpbar][1]
1. **[IoT Hub]** ブレードで、IoT Hub の構成を選択します。
   
    ![IoT hub blade][2]
   
   1. **[名前]** ボックスに IoT Hub の名前を入力します。 その**名前**が有効で利用できる場合、**[名前]** ボックスに緑色のチェック マークが表示されます。
   1. [[pricing and scale tier (価格とスケール レベル)]][lnk-pricing] を選択します。 このチュートリアルでは特定のレベルは必要ありません。 このチュートリアルでは、無料の F1 レベルを使用します。
   1. **[リソース グループ]** で、リソース グループを作成するか、既存のリソース グループを選択します。 詳細については、[リソース グループを使用した Azure リソースの管理][lnk-resource-groups]に関するページを参照してください。
   1. **[場所]**で、IoT Hub をホストする場所を選択します。 このチュートリアルでは、最も近い場所を選択します。
1. 必要な IoT Hub 構成オプションを選択したら、 **[作成]**をクリックします。  Azure が IoT Hub を作成するまでに数分かかる場合があります。 状態を確認するには、スタート画面または通知パネルで進行状況を監視してください。
   
    ![New IoT hub status][3]
1. IoT Hub が正常に作成されたら、Azure Portal で IoT Hub の新しいタイルをクリックし、新しい IoT Hub のブレードを開きます。 **ホスト名**をメモして、**[共有アクセス ポリシー]** をクリックします。
   
    ![New IoT hub blade][4]
1. **[共有アクセス ポリシー]** ブレードで、**[iothubowner]** ポリシーをクリックし、**[iothubowner]** ブレードで IoT Hub 接続文字列をコピーしてメモします。 詳細については、IoT Hub 開発者ガイドの[アクセス制御][lnk-access-control]に関するページを参照してください。
   
    ![Shared access policies blade][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
