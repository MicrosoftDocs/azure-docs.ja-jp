---
title: チュートリアル - Azure IoT Hub Device Provisioning Service (.NET) を使用してデバイスをプロビジョニングする
description: このチュートリアルでは、Azure IoT Hub Device Provisioning Service (DPS) と .NET を使用して 1 つの IoT ハブにデバイスをプロビジョニングする方法を説明します。
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 9d5b1511ffb48f587d4ee5c5a7d2b0ee9216018f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976776"
---
# <a name="tutorial-enroll-the-device-to-an-iot-hub-using-the-azure-iot-hub-provisioning-service-client-net"></a>チュートリアル:Azure IoT Hub Device Provisioning Service Client (.NET) を使用して IoT ハブにデバイスを登録する

前のチュートリアルでは、Device Provisioning Service に接続するデバイスを設定する方法を説明しました。 このチュートリアルでは、このサービスで " **_個別の登録_** " と " **_登録グループ_** " の両方を使用して、1 つの IoT ハブにデバイスをプロビジョニングする方法について説明します。 このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * デバイスを登録する
> * デバイスを起動する
> * デバイスが登録されていることを確認する

## <a name="prerequisites"></a>前提条件

以降の手順に進む前に、チュートリアル「[Azure IoT Hub Device Provisioning Service を使用してプロビジョニングするデバイスの設定](./tutorial-set-up-device.md)」の説明に従って、デバイスと "*ハードウェア セキュリティ モジュール*" を構成してください。

* Visual Studio

> [!NOTE]
> Visual Studio は必須ではありません。 [.NET](https://www.microsoft.com/net) のインストールだけが必要であり、開発者は Windows または Linux で任意のエディターを使用することができます。  

このチュートリアルでは、デバイス情報がプロビジョニング サービスに追加される、ハードウェアの製造プロセス中またはその直後の期間をシミュレートします。 このコードは、通常、.NET コードを実行できる PC またはファクトリ デバイス上で実行します。デバイス自体には追加しないでください。


## <a name="enroll-the-device"></a>デバイスを登録する

この手順では、Device Provisioning Service にデバイスの一意のセキュリティ アーティファクトを追加する必要があります。 追加するセキュリティ アーティファクトは次のとおりです。

- TPM ベースのデバイスの場合:
    - 各 TPM チップまたはシミュレーションに固有の "*保証キー*"。 詳細については、「[TPM 保証キーとは](https://technet.microsoft.com/library/cc770443.aspx)」をご覧ください。
    - 名前空間/スコープ内でデバイスを一意に識別するために使用する "*登録 ID*"。 これは、デバイス ID と同じである場合もあれば、異なる場合もあります。 この ID はすべてのデバイスで必須です。 TPM ベースのデバイスでは、登録 ID が TPM 自体から派生している場合があります (TPM 保証キーの SHA-256 ハッシュなど)。

- X.509 ベースのデバイスの場合:
    - *.pem* ファイルまたは *.cer* ファイルの形式で[デバイスに発行された X.509 証明書](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx)。 個別登録では、X.509 システムの "*リーフ証明書*" を使用する必要があります。登録グループでは、"*ルート証明書*" または同等の "*署名者証明書*" を使用する必要があります。
    - 名前空間/スコープ内でデバイスを一意に識別するために使用する "*登録 ID*"。 これは、デバイス ID と同じである場合もあれば、異なる場合もあります。 この ID はすべてのデバイスで必須です。 X.509 ベースのデバイスの場合、登録 ID は証明書の共通名 (CN) から派生します。 これらの要件の詳細については、[デバイスの概念](https://docs.microsoft.com/azure/iot-dps/concepts-device)に関するページを参照してください。

デバイスを Device Provisioning Service に登録するには、次の 2 つの方法があります。

- **Individual Enrollments\(個別登録\)** : Device Provisioning Service に登録できる 1 つのデバイスのエントリを表します。 個別加入では、構成証明メカニズムとして X.509 証明書または (実際の TPM または仮想 TPM の) SAS トークンを使用できます。 固有の初期構成を必要とするデバイスや、TPM を介した SAS トークンのみを構成証明メカニズムとして使用できるデバイスには、個別登録を使用することをお勧めします。 個別登録では、必要な IoT ハブ デバイス ID が指定されている場合があります。

- **Enrollment Groups\(登録グループ\)** : これは、特定の構成証明メカニズムを共有するデバイスのグループを表します。 必要な初期構成を共有する多数のデバイスがある場合や、すべてのデバイスが同じテナントに配置される場合は、登録グループを使用することをお勧めします。 登録グループは X.509 のみであり、すべてが X.509 証明書チェーンで署名証明書を共有します。

### <a name="enroll-the-device-using-individual-enrollments"></a>個別登録を使用してデバイスを登録する

1. Visual Studio で**コンソール アプリケーション** プロジェクト テンプレートを使用して、Visual C# コンソール アプリケーション プロジェクトを作成します。 プロジェクトに **DeviceProvisioning** という名前を付けます。
    
1. ソリューション エクスプローラーで **[DeviceProvisioning]** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** をクリックします。

1. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**microsoft.azure.devices.provisioning.service** を検索します。 エントリを選択し、 **[インストール]** をクリックして **Microsoft.Azure.Devices.Provisioning.Service** パッケージをインストールして、使用条件に同意します。 この手順により、[Azure IoT Device Provisioning Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet パッケージがダウンロードおよびインストールされ、パッケージへの参照とパッケージの依存関係が追加されます。

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
   
    ```csharp
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

1. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値は、前のセクションで書き留めておいた Device Provisioning Service 接続文字列に置き換えてください。
   
    ```csharp
    static readonly string ServiceConnectionString = "{Device Provisioning Service connection string}";

    private const string SampleRegistrationId = "sample-individual-csharp";
    private const string SampleTpmEndorsementKey =
            "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
            "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
            "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
            "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
            "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

1. デバイスの登録を実装するために以下を追加します。

    ```csharp
    static async Task SetRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetRegistrationData");

        Attestation attestation = new TpmAttestation(SampleTpmEndorsementKey);

        IndividualEnrollment individualEnrollment = new IndividualEnrollment(SampleRegistrationId, attestation);

        individualEnrollment.DeviceId = OptionalDeviceId;
        individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;

        Console.WriteLine("\nAdding new individualEnrollment...");
        var serviceClient = ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString);

        IndividualEnrollment individualEnrollmentResult =
            await serviceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);

        Console.WriteLine("\nIndividualEnrollment created with success.");
        Console.WriteLine(individualEnrollmentResult);
    }
    ```

1. 最後に、次のコードを **Main** メソッドに追加して、IoT ハブへの接続を開き、登録を開始します。
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Provisioning example");

        SetRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.ReadLine();
    ```
        
1. Visual Studio のソリューション エクスプローラーでソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]** をクリックします。 **[シングル スタートアップ プロジェクト]** を選択し、ドロップダウン メニューから **[DeviceProvisioning]** プロジェクトを選択します。  

1. .NET デバイス アプリ **DeviceProvisiong** を実行します。 これによって、デバイスのプロビジョニングがセットアップされます。 

    ![個別登録を実行する](./media/tutorial-net-provision-device-to-hub/individual.png)

デバイスが正常に登録されると、ポータルに次のように表示されます。

   ![ポータルで正常に完了した登録](./media/tutorial-net-provision-device-to-hub/individual-portal.png)

### <a name="enroll-the-device-using-enrollment-groups"></a>登録グループを使用してデバイスを登録する

> [!NOTE]
> 登録グループのサンプルには、X.509 証明書が必要です。

1. Visual Studio のソリューション エクスプローラーで、前に作成した **DeviceProvisioning** プロジェクトを開きます。 

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
    
    ```csharp
    using System.Security.Cryptography.X509Certificates;
    ```

1. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値を X509 証明書の場所に置き換えます。
   
    ```csharp
    private const string X509RootCertPathVar = "{X509 Certificate Location}";
    private const string SampleEnrollmentGroupId = "sample-group-csharp";
    ```

1. グループの登録を実装するために以下を **Program.cs** に追加します。

    ```csharp
    public static async Task SetGroupRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetGroupRegistrationData");

        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString))
        {
            Console.WriteLine("\nCreating a new enrollmentGroup...");

            var certificate = new X509Certificate2(X509RootCertPathVar);

            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);

            EnrollmentGroup enrollmentGroup = new EnrollmentGroup(SampleEnrollmentGroupId, attestation);

            Console.WriteLine(enrollmentGroup);
            Console.WriteLine("\nAdding new enrollmentGroup...");

            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);

            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
        }
    }
    ```

1. 最後に、次のコードを **Main** メソッドで置換して、IoT ハブへの接続を開き、グループ登録を開始します。
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Group Provisioning example");

        SetGroupRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. .NET デバイス アプリ **DeviceProvisiong** を実行します。 これによって、デバイスのグループ プロビジョニングがセットアップされます。 

    ![グループ登録を実行する](./media/tutorial-net-provision-device-to-hub/group.png)

    デバイス グループが正常に登録されると、ポータルに次のように表示されます。

   ![ポータルで正常に完了したグループ登録](./media/tutorial-net-provision-device-to-hub/group-portal.png)


## <a name="start-the-device"></a>デバイスを起動する

この時点で、次のようにデバイスの登録の準備が整っています。

1. デバイスまたはデバイス グループが Device Provisioning Service に登録されている。 
2. デバイスの準備ができ、セキュリティが構成されて、Device Provisioning Service Client SDK を使用するアプリケーションを通じてアクセスできる。

デバイスを起動して、クライアント アプリケーションが Device Provisioning Service への登録を開始できるようにします。  


## <a name="verify-the-device-is-registered"></a>デバイスが登録されていることを確認する

デバイスが起動すると、次のアクションが実行されます。 詳細については、[プロビジョニング デバイス クライアントのサンプル](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device)に関するページを参照してください。 

1. デバイスが Device Provisioning Service に登録要求を送信します。
2. TPM デバイスの場合、Device Provisioning Service が登録チャレンジを送信し、デバイスがこれに応答します。 
3. 登録が成功すると、Device Provisioning Service は、IoT ハブの URI、デバイス ID、暗号化されたキーをデバイスに送信します。 
4. デバイス上の IoT Hub クライアント アプリケーションがハブに接続します。 
5. ハブに正常に接続されると、IoT ハブの **Device Explorer** にデバイスが表示されます。 

    ![ポータルに表示されたハブへの成功した接続](./media/tutorial-net-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>次の手順
このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * デバイスを登録する
> * デバイスを起動する
> * デバイスが登録されていることを確認する

次のチュートリアルに進み、負荷分散されたハブに複数のデバイスをプロビジョニングする方法を学習してください。 

> [!div class="nextstepaction"]
> [負荷分散された IoT ハブにデバイスをプロビジョニングする](./tutorial-provision-multiple-hubs.md)
