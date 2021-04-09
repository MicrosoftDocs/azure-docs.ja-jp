---
title: テンプレートを使用した Azure IoT Hub の作成 (.NET) | Microsoft Docs
description: Azure Resource Manager テンプレートを使用して C# プログラムから IoT ハブを作成する方法。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: db4b676e65d36a9476fd72b66cc8ccfa38af4d85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92144490"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Azure Resource Manager テンプレートを使用した IoT ハブの作成 (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Azure リソース マネージャーを使って、Azure IoT ハブをプログラムを使用して作成、管理できます。 このチュートリアルでは、Azure Resource Manager テンプレートを使用して C# プログラムから IoT Hub を作成する方法を説明します。

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Azure Resource Manager とクラシックの](../azure-resource-manager/management/deployment-models.md) 2 種類のデプロイメント モデルがあります。  この記事では、Azure Resource Manager デプロイ モデルの使用について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このチュートリアルを完了するには、以下が必要です。

* 見ることができます。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 <br/>アカウントがない場合は、 [無料アカウント][lnk-free-trial] を数分で作成することができます。
* Azure Resource Manager テンプレート ファイルを格納できる [Azure Storage アカウント][lnk-storage-account]。
* [Azure PowerShell 1.0][lnk-powershell-install] 以降。

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Visual Studio プロジェクトの準備

1. Visual Studio で、**[Console App (.NET Framework)]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを作成します。 プロジェクトに **CreateIoTHub** という名前を付けます。

2. ソリューション エクスプローラーで、プロジェクトを右クリックし、 **[NuGet パッケージの管理]** をクリックします。

3. NuGet パッケージ マネージャーで **[プレリリースを含める]** をオンにし、**[参照]** ページで **Microsoft.Azure.Management.ResourceManager** を検索します。 パッケージを選択して **[インストール]** をクリックし、**[変更の確認]** で、**[OK]**、**[同意する]** の順にクリックしてライセンス条項に同意します。

4. NuGet パッケージ マネージャーで、**Microsoft.IdentityModel.Clients.ActiveDirectory** を検索します。  **[インストール]** をクリックし、**[変更の確認]** で、**[OK]**、**[同意する]** の順にクリックしてライセンス条項に同意します。

5. Program.cs で、既存の **using** ステートメントを以下のコードに置き換えます。

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. Program.cs で、次の静的変数を追加して、プレースホルダーの値を置き換えます。 このチュートリアルの前の手順で **ApplicationId**、**SubscriptionId**、**TenantId**、および **パスワード** を書き留めています。 **Azure Storage アカウント名** は、Azure Resource Manager テンプレート ファイルの保存先となる Azure Storage アカウントの名前です。 **リソース グループ名** は、IoT Hub を作成するときに使用するリソース グループの名前です。 名前は既存のリソース グループまたは新しいリソース グループになります。 **デプロイ名** は、デプロイの名前です (**Deployment_01** など)。

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>テンプレートを送信して IoT Hub を作成する

JSON テンプレートとパラメーター ファイルを使用して、リソース グループに IoT Hub を作成します。 Azure Resource Manager テンプレートを使用して、既存の IoT Hub に変更を加えることもできます。

1. ソリューション エクスプローラーで、目的のプロジェクトを右クリックし、**[追加]**、**[新しい項目]** の順にクリックします。 **template.json** という JSON ファイルをプロジェクトに追加します。

2. 標準 IoT Hub を **米国東部** リージョンに追加するには、**template.json** の内容を次のリソース定義に置き換えます。 IoT Hub をサポートするリージョンの最新の一覧については、「[Azure の状態][lnk-status]」を参照してください。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. ソリューション エクスプローラーで、目的のプロジェクトを右クリックし、**[追加]**、**[新しい項目]** の順にクリックします。 **parameters.json** という JSON ファイルをプロジェクトに追加します。

4. **parameters.json** の内容を次のパラメーター情報に置き換えて、新しい IoT Hub の名前を、たとえば **{自分のイニシャル}mynewiothub** に設定します。 IoT ハブ名はグローバルに一意である必要があるため、自分の名前やイニシャルを含めます。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. **サーバー エクスプローラー** から Azure サブスクリプションに接続し、Azure Storage アカウントで **templates** というコンテナーを作成します。 **[プロパティ]** ウィンドウで、**[templates]** コンテナーの **[パブリック読み取りアクセス]** 権限を **[BLOB]** に設定します。

6. **サーバー エクスプローラー** で **[templates]** コンテナーを右クリックし、**[BLOB コンテナーの表示]** をクリックします。 **[BLOB のアップロード]** ボタンをクリックして **parameters.json** と **templates.json** の 2 つのファイルを選択し、**[開く]** をクリックして、**[templates]** コンテナーに JSON ファイルをアップロードします。 JSON データを含んだ BLOB の URL は次のとおりです。

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. 次のメソッドを Program.cs に追加します。

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. テンプレート ファイルとパラメーター ファイルを Azure Resource Manager に送信するために、次のコードを **CreateIoTHub** メソッドに追加します。

    ```csharp
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

9. 次のコードを **CreateIoTHub** メソッドに追加します。これは新しい IoT Hub のステータスとキーを表示するコードです。

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>アプリケーションの完了と実行

**CreateIoTHub** メソッドの呼び出しを追加したうえでアプリケーションをビルドし、実行すれば、アプリケーションは完成です。

1. 次のコードを **Main** メソッドの末尾に追加します。

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. **[ビルド]**、**[ソリューションのビルド]** の順にクリックします。 すべてのエラーを修正します。

3. **[デバッグ]**、**[デバッグの開始]** の順にクリックし、アプリケーションを実行します。 デプロイメントが実行されるまでに数分かかる場合があります。

4. アプリケーションが新しい IoT ハブを追加したことを確認するには、[Azure portal][lnk-azure-portal] にアクセスしてリソースの一覧を表示します。 または、**Get-AzResource** PowerShell コマンドレットを使用します。

> [!NOTE]
> このサンプル アプリケーションでは、課金の対象とする S1 Standard IoT Hub を追加します。 完了したら、[Azure portal][lnk-azure-portal] または **Remove-AzResource** PowerShell コマンドレットを使用してその IoT ハブを削除できます。

## <a name="next-steps"></a>次のステップ
ここでは、C# プログラムと Azure Resource Manager テンプレートを使用して IoT Hub をデプロイしました。次の手順に進んでください。

* [IoT Hub リソース プロバイダー REST API][lnk-rest-api] の機能の詳細をご確認ください。
* Azure Resource Manager の機能の詳細については、「[Azure Resource Manager の概要][lnk-azure-rm-overview]」を参照してください。
* テンプレートで使用する JSON 構文とプロパティについては、「[Microsoft.Devices resource types (Microsoft.Devices のリソースの種類)](/azure/templates/microsoft.devices/iothub-allversions)」を参照してください。

IoT Hub の開発に関する詳細については、以下の記事をご覧ください。

* [C SDK の概要][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [Azure IoT Edge でエッジ デバイスに AI をデプロイする][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: /rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/quickstart-linux.md