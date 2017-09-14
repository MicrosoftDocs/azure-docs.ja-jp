## <a name="associate-an-azure-storage-account-to-iot-hub"></a>IoT Hub への Azure Storage アカウントの関連付け

シミュレート対象デバイス アプリによって BLOB にファイルがアップロードされるため、IoT Hub に関連付けられている [Azure Storage](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) アカウントが必要です。 Azure Storage アカウントを IoT ハブに関連付けると、IoT ハブによって SAS URI が生成されます。 デバイスは、この SAS URI を使って安全にファイルを BLOB コンテナーにアップロードすることができます。 SAS URI を生成し、デバイスでファイルのアップロードに使用できるようにするプロセスは、IoT Hub サービスとデバイス SDK によって調整されます。

「[Azure Portal を使用してファイルのアップロードを構成する](../articles/iot-hub/iot-hub-configure-file-upload.md)」の手順に従って、Azure ストレージ アカウントを IoT ハブに関連付けます。 ご利用の IoT ハブに BLOB コンテナーが関連付けられていること、またファイル通知が有効になっていることを確認します。

![ポータルでファイル通知を有効にする](media/iot-hub-associate-storage/enable-file-notifications.png)