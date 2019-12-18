---
title: チュートリアル - シミュレートされた X.509 デバイスを Java と登録グループを使用して Azure IoT Hub にプロビジョニングする
description: このチュートリアルでは、シミュレートされた X.509 デバイスを IoT Hub Device Provisioning Service (DPS) 対応の Java device and service SDK と登録グループを使用して作成、プロビジョニングします。
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: d6cb3af134ff272d79cfc440047a3d90733ee9e8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976810"
---
# <a name="tutorial-create-and-provision-a-simulated-x509-device-using-java-device-and-service-sdk-and-group-enrollments-for-iot-hub-device-provisioning-service"></a>チュートリアル:シミュレートされた X.509 デバイスを IoT Hub Device Provisioning Service 対応の Java device and service SDK と登録グループを使用して作成、プロビジョニングする

以下の手順では、Windows OS を実行する開発マシン上で X.509 デバイスをシミュレートすると共に、コード サンプルを使って、そのシミュレートされたデバイスを Device Provisioning Service および IoT Hub に登録グループを使って接続する方法について説明します。 

事前に、[Azure Portal での IoT Hub Device Provisioning Service の設定](./quick-setup-auto-provision.md)に関するページの手順を済ませておいてください。


## <a name="prepare-the-environment"></a>環境の準備 

1. [Java SE Development Kit 8](https://aka.ms/azure-jdks) がマシンにインストールされていることを確認します。

1. [Maven](https://maven.apache.org/install.html) をダウンロードし、インストールします。

1. マシンに `git` がインストールされ、コマンド ウィンドウからアクセスできる環境変数に追加されていることを確認します。 **Git Bash** (ローカル Git リポジトリと対話する際に使用するコマンドライン アプリ) など、インストールする各種 `git` ツールの最新バージョンについては、[Software Freedom Conservancy の Git クライアント ツール](https://git-scm.com/download/)に関するページを参照してください。 

1. こちらの[証明書の概要](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)に従ってテスト証明書を作成します。

    > [!NOTE]
    > この手順には [OpenSSL](https://www.openssl.org/) が必要です。OpenSSL をソースからビルドしてインストールするか、または[サード パーティ](https://wiki.openssl.org/index.php/Binaries) ([例](https://sourceforge.net/projects/openssl/)) からダウンロードしてインストールしてください。 "_ルート証明書_"、"_中間証明機関の証明書_"、"_デバイス証明書_" が既にある場合は、この手順を省略してかまいません。
    >

    1. 最初の 2 つの手順の作業を行って、"_ルート証明書_" と "_中間証明機関の証明書_" を作成します。

    1. Azure Portal にサインインし、左側のメニューにある **[すべてのリソース]** ボタンをクリックしてプロビジョニング サービスを開きます。

        1. Device Provisioning Service の概要ブレードで **[証明書]** を選択し、上部の **[追加]** ボタンをクリックします。

        1. **[証明書の追加]** に次の情報を入力します。
            - 一意の証明書名を入力します。
            - 作成した **_RootCA.pem_** ファイルを選択します。
            - 作業が完了したら、 **[保存]** をクリックします。

           ![証明書を追加する](./media/tutorial-group-enrollments/add-certificate.png)

        1. 新しく作成された証明書を選択します。
            - **[確認コードを生成します]** をクリックします。 生成されたコードをコピーします。
            - 確認の手順を実行します。 "_確認コード_" を入力するか、または右クリックして実行中の PowerShell ウィンドウに貼り付けます。  **Enter** キーを押します。
            - 新しく作成した **_verifyCert4.pem_** ファイルを Azure Portal で選択します。 **[確認]** をクリックします。

              ![証明書を検証する](./media/tutorial-group-enrollments/validate-certificate.png)

    1. 最後に、デバイス証明書を作成し、リソースをクリーンアップする手順を実行します。

       > [!NOTE]
       > デバイス証明書を作成するとき、デバイス名には必ず小文字の英数字とハイフンだけを使ってください。
       >


## <a name="create-a-device-enrollment-entry"></a>デバイス登録エントリを作成する

1. コマンド プロンプトを開きます。 Java SDK コード サンプルの GitHub リポジトリを複製します。

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. ダウンロードしたソース コードで、サンプル フォルダー **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** に移動します。 任意のエディターでファイル **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** を開き、次の詳細を追加します。

    1. 次のように、ポータルからプロビジョニング サービスの `[Provisioning Connection String]` を追加します。

        1. [Azure Portal](https://portal.azure.com) でプロビジョニング サービスに移動します。

        1. **[共有アクセス ポリシー]** を開き、*EnrollmentWrite* アクセス許可を持つポリシーを選択します。

        1. **主キーの接続文字列**をコピーします。

            ![ポータルからプロビジョニング接続文字列を取得する](./media/tutorial-group-enrollments/provisioning-string.png)  

        1. サンプル コード ファイル **_ServiceEnrollmentGroupSample.java_** の `[Provisioning Connection String]` を**主キーの接続文字列**に置き換えます。

            ```java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    1. テキスト エディターで、中間署名証明書ファイルを開きます。 `PUBLIC_KEY_CERTIFICATE_STRING` 値を中間署名証明書の値で更新します。

        Bash シェルを使用してデバイス証明書を生成した場合は、 *./certs/azure-iot-test-only.intermediate.cert.pem* に中間証明書キーが含まれています。 証明書が PowerShell で生成された場合は、 *./Intermediate1.pem* が中間証明書ファイルになります。

        ```java
        private static final String PUBLIC_KEY_CERTIFICATE_STRING =
                "-----BEGIN CERTIFICATE-----\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "-----END CERTIFICATE-----\n";
        ```

    1. [Azure Portal](https://portal.azure.com) でプロビジョニング サービスにリンクされている IoT Hub に移動します。 ハブの **[概要]** タブを開き、 **[ホスト名]** をコピーします。 この **[ホスト名]** を *IOTHUB_HOST_NAME* パラメーターに割り当てます。

        ```java
        private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
        ```

    1. サンプル コードの内容を確認します。 このコードは、X.509 デバイスのグループ登録を作成、更新、照会、および削除します。 ポータルで登録が成功したことを確認するには、_ServiceEnrollmentGroupSample.java_ ファイルの末尾にある次のコード行を一時的にコメント アウトします。

        ```java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    1. ファイル _ServiceEnrollmentGroupSample.java_ を保存します。

1. コマンド ウィンドウを開き、フォルダー **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** に移動します。

1. 次のコマンドを使用して、サンプル コードをビルドします。

    ```cmd\sh
    mvn install -DskipTests
    ```

1. コマンド ウィンドウで次のコマンドを使用してサンプルを実行します。

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

1. 出力ウィンドウで、登録が正常に完了したことを確認します。

    ![登録が正常に完了](./media/tutorial-group-enrollments/enrollment.png) 

1. Azure Portal でプロビジョニング サービスに移動します。 **[登録を管理します]** をクリックします。 X.509 デバイスのグループは、自動生成された *GROUP NAME* という名前で **[登録グループ]** タブに表示されます。

## <a name="simulate-the-device"></a>デバイスをシミュレートする

1. Device Provisioning Service の概要ブレードで、 **[概要]** を選択し、"_ID スコープ_" と "_プロビジョニング サービス グローバル エンドポイント_" をメモします。

    ![サービス情報](./media/tutorial-group-enrollments/extract-dps-endpoints.png)

1. コマンド プロンプトを開きます。 サンプル プロジェクト フォルダーに移動します。

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` を編集して、前に書き留めておいた "_ID スコープ_" と "_プロビジョニング サービス グローバル エンドポイント_" を含めます。

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    private static final int MAX_TIME_TO_WAIT_FOR_REGISTRATION = 10000; // in milli seconds
    private static final String leafPublicPem = "<Your Public PEM Certificate here>";
    private static final String leafPrivateKey = "<Your Private PEM Key here>";
    ```

1. パブリックおよびプライベートのデバイス証明書を使用して、`leafPublicPem` および `leafPrivateKey` 変数を更新します。

    PowerShell を使用してデバイス証明書を生成した場合、ファイル mydevice * にはそのデバイスの公開キー、秘密キー、および PFX が含まれています。

    Bash シェルを使用してデバイス証明書を生成した場合は、./certs/new-device.cert.pem に公開キーが含まれています。 デバイスの秘密キーは、./private/new-device.key.pem ファイルにあります。

    公開キー ファイルを開き、その値で `leafPublicPem` 変数を更新します。 _----- BEGIN PRIVATE KEY -----_ から _----- END PRIVATE KEY -----_ までのテキストをコピーします。

    ```java
    private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

    秘密キー ファイルを開き、その値で `leafPrivatePem` 変数を更新します。 _----- BEGIN RSA PRIVATE KEY -----_ から _----- END RSA PRIVATE KEY -----_ までのテキストをコピーします。

    ```java
    private static final String leafPrivateKey = "-----BEGIN RSA PRIVATE KEY-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END RSA PRIVATE KEY-----\n";
    ```

1. 中間証明書の `leafPrivateKey` のすぐ下に、新しい変数を追加します。 この新しい変数に、`intermediateKey` という名前を付けます。 それに、中間署名証明書の値を設定します。

    Bash シェルを使用してデバイス証明書を生成した場合は、 *./certs/azure-iot-test-only.intermediate.cert.pem* に中間証明書キーが含まれています。 証明書が PowerShell で生成された場合は、 *./Intermediate1.pem* が中間証明書ファイルになります。

    ```java
    private static final String intermediateKey = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

1. `main` 関数で、`securityProviderX509` の初期化の前に、`intermediateKey` を `signerCertificates` コレクションに追加します。

    ```java
    public static void main(String[] args) throws Exception
    {
        ...

        try
        {
            ProvisioningStatus provisioningStatus = new ProvisioningStatus();

            // Add intermediate certificate as part of the certificate key chain.
            signerCertificates.add(intermediateKey);

            SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(leafPublicPem, leafPrivateKey, signerCertificates);
    ```

1. 変更内容を保存し、サンプルをビルドします。 ターゲット フォルダーに移動し、作成した jar ファイルを実行します。

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

    ![成功した登録](./media/tutorial-group-enrollments/registration.png)

1. ポータルで、ご利用のプロビジョニング サービスにリンクされている IoT Hub に移動し、 **[Device Explorer]** ブレードを開きます。 シミュレートされた X.509 デバイスをハブにプロビジョニングすると、そのデバイス ID が**有効**な "*状態*" として **[Device Explorer]** ブレードに表示されます。 サンプル デバイス アプリケーションを実行する前に既にブレードが開いていた場合は、必要に応じて一番上にある **[最新の情報に更新]** ボタンをクリックしてください。 

    ![IoT ハブに登録されたデバイス](./media/tutorial-group-enrollments/hub-registration.png) 


## <a name="clean-up-resources"></a>リソースのクリーンアップ

引き続きデバイス クライアント サンプルを使用する場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 使用する予定がない場合は、次の手順を使用して、このクイックスタートで作成したすべてのリソースを削除してください。

1. マシンに表示されているデバイス クライアント サンプルの出力ウィンドウを閉じます。
1. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、Device Provisioning サービスを選択します。 サービスの **[登録を管理します]** ブレードを開き、 **[個々の登録]** タブをクリックします。このクイックスタートで登録したデバイスの*登録 ID* を選択し、上部の **[削除]** ボタンをクリックします。 
1. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、IoT ハブを選択します。 ハブの **[IoT Devices]\(IoT デバイス\)** ブレードを開き、このクイックスタートで登録したデバイスの "*デバイス ID*" を選択し、一番上の **[削除]** ボタンをクリックします。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、シミュレートされた X.509 デバイスを Windows コンピューター上に作成し、Azure IoT Hub Device Provisioning Service と登録グループを使用して IoT Hub にプロビジョニングしました。 X.509 デバイスの詳細については、引き続きデバイスの概念に進んでください。 

> [!div class="nextstepaction"]
> [IoT Hub Device Provisioning Service のデバイスの概念](concepts-device.md)
