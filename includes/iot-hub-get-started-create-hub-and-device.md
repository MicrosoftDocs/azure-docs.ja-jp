## <a name="create-an-iot-hub"></a>IoT Hub の作成

1. [Azure Portal](https://portal.azure.com/) で **[新規]** > **[モノのインターネット (IoT)]** > **[IoT Hub]** の順にクリックします。

   ![Azure Portal で IoT ハブを作成する](../articles/iot-hub/media/iot-hub-create-hub-and-device/1_create-azure-iot-hub-portal.png)
2. **[IoT Hub]** ウィンドウで、IoT Hub のために以下の情報を入力します。

     **[名前]**: IoT ハブの名前を入力します。 入力した名前が有効である場合は、緑色のチェック マークが表示されます。

     **[価格とスケールティア]**: **[F1 - Free]** レベルを選択します。 このデモでは、このオプションで十分です。 詳細については、[料金とスケール レベル](https://azure.microsoft.com/pricing/details/iot-hub/)に関するページを参照してください。

     **[リソース グループ]**: IoT Hub をホストするリソース グループを作成するか、既存のリソース グループを使用します。 詳細については、[リソース グループを使用した Azure リソースの管理](../articles/azure-resource-manager/resource-group-portal.md)に関するページを参照してください。

     **[場所]**: IoT Hub が作成される場所に最も近い場所を選択します。

     **[ダッシュボードにピン留めする]**: ダッシュボードから IoT Hub に簡単にアクセスできるようにするには、このオプションをオンにします。

   ![情報を入力して IoT ハブを作成する](../articles/iot-hub/media/iot-hub-create-hub-and-device/2_fill-in-fields-for-azure-iot-hub-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

3. ページの下部にある **[Create]**」を参照してください。 IoT ハブの作成には数分かかることがあります。 **[通知]** ウィンドウで進行状況を確認できます。

   ![IoT ハブの進捗状況の通知を見る](../articles/iot-hub/media/iot-hub-create-hub-and-device/3_notification-azure-iot-hub-creation-progress-portal.png)

4. IoT ハブが作成されたら、ダッシュボードでその IoT ハブをクリックします。 **ホスト名**をメモして、**[共有アクセス ポリシー]** をクリックします。

   ![IoT Hub のホスト名を取得する](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

5. **[共有アクセス ポリシー]** ウィンドウで、**[iothubowner]** ポリシーをクリックし、IoT Hub の **[接続文字列]** をコピーしてメモしておきます。 詳細については、「[IoT Hub へのアクセスの制御](../articles/iot-hub/iot-hub-devguide-security.md)」を参照してください。

> [!NOTE] 
このセットアップ チュートリアルでは、この iothubowner 接続文字列は必要ありません。 ただし、このセットアップの完了後、異なる IoT シナリオの一部のチュートリアルでは必要になる場合があります。

   ![IoT Hub の接続文字列を取得する](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>デバイスの IoT ハブにデバイスを登録する

1. [Azure Portal](https://portal.azure.com/) で、IoT ハブを開きます。

2. **デバイス エクスプローラー**をクリックします。
3. デバイス エクスプローラー ウィンドウで **[追加]** をクリックして、デバイスを IoT Hub に追加します。 次に、次を実行します。

   **[デバイス ID]**: 新しいデバイスの ID を入力します。 デバイス ID には大文字と小文字の区別があります。

   **[認証の種類]**: **[対称キー]** を選択します。

   **[キーの自動生成]**: このチェック ボックスをオンにします。

   **[Connect device to IoT Hub (デバイスを IoT Hub に接続する)]**: **[有効]** をクリックします。

   ![IoT ハブの Device Explorer でデバイスを追加する](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. [ **Save**] をクリックします。
5. デバイスが作成された後、**デバイス エクスプローラー** ウィンドウでデバイスを開きます。
6. 接続文字列の主キーをメモします。

   ![デバイスの接続文字列を取得する](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)
