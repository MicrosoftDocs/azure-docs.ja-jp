---
title: C# と Resource Manager テンプレートを使用した VM のデプロイ | Microsoft Docs
description: C# および Resource Manager テンプレートを使用して Azure VM をデプロイする方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/14/2017
ms.author: cynthn
ms.openlocfilehash: 65ce7711786e15a5455d91ce829a3bc0bdf4317d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103227"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>C# と Resource Manager テンプレートを使用した Azure の仮想マシンのデプロイ

この記事では、C# を使用して Azure Resource Manager テンプレートをデプロイする方法を示します。 作成したテンプレートは、単一のサブネットを持つ新しい仮想ネットワークに、Windows Server を実行する単一の仮想マシンをデプロイします。

仮想マシン リソースの詳細については、「[Virtual machines in an Azure Resource Manager template](template-description.md)」(Azure Resource Manager テンプレートの仮想マシン) をご覧ください。 テンプレート内のすべてのリソースの詳細については、「[Resource Manager テンプレートのチュートリアル](../../azure-resource-manager/resource-manager-template-walkthrough.md)」をご覧ください。

これらの手順を実行するには約 10 分かかります。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

この手順では、Visual Studio がインストールされていることを確認し、テンプレートをデプロイするために使用するコンソール アプリケーションを作成します。

1. まだ [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio) をインストールしていない場合は、インストールを実行します。 [ワークロード] ページで **[.NET デスクトップ開発]** を選び、 **[インストール]** をクリックします。 サマリーで、 **[.NET Framework 4 から 4.6 の開発ツール]** が自動的に選択されていることが確認できます。 Visual Studio を既にインストールしてある場合は、Visual Studio 起動ツールを使って .NET ワークロードを追加できます。
2. Visual Studio で、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** をクリックします。
3. **[テンプレート]**  >  **[Visual C#]** で **[コンソール アプリ (.NET Framework)]** を選択し、プロジェクトの名前に「*myDotnetProject*」と入力して、プロジェクトの場所を選んだ後、 **[OK]** をクリックします。

## <a name="install-the-packages"></a>パッケージのインストール

NuGet パッケージを使用すると、手順を完了するために必要なライブラリを簡単にインストールできます。 Visual Studio で必要なライブラリを入手するには、次の手順に従います。

1. **[ツール]**  >  **[NuGet パッケージ マネージャー]** をクリックし、 **[パッケージ マネージャー コンソール]** をクリックします。
2. コンソールに次のコマンドを入力します。

    ```powershell
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>ファイルの作成

この手順では、リソースをデプロイするテンプレート ファイルと、テンプレートにパラメーター値を指定するパラメーター ファイルを作成します。 また、Azure Resource Manager の操作を実行するために使用する承認ファイルも作成します。

### <a name="create-the-template-file"></a>テンプレート ファイルを作成する

1. ソリューション エクスプローラーで、 *[myDotnetProject]*  >  **[追加]**  >  **[新しい項目]** を右クリックしてから、 *[Visual C# アイテム]* で **[テキスト ファイル]** を選択します。 ファイルに「*CreateVMTemplate.json*」と名前を付けて、 **[追加]** をクリックします。
2. この JSON コードを作成したファイルに追加します。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]", 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

3. CreateVMTemplate.json ファイルを保存します。

### <a name="create-the-parameters-file"></a>パラメーター ファイルの作成

テンプレートのリソース パラメーターの値を指定するには、値を含むパラメーター ファイルを作成します。

1. ソリューション エクスプローラーで、 *[myDotnetProject]*  >  **[追加]**  >  **[新しい項目]** を右クリックしてから、 *[Visual C# アイテム]* で **[テキスト ファイル]** を選択します。 ファイルに「*Parameters.json*」と名前を付けて、 **[追加]** をクリックします。
2. この JSON コードを作成したファイルに追加します。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

4. Parameters.json ファイルを保存します。

### <a name="create-the-authorization-file"></a>承認ファイルを作成する

テンプレートをデプロイする前に、[Active Directory サービス プリンシパル](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)にアクセスできることを確認します。 このサービス プリンシパルから、Azure Resource Manager への要求を認証するためのトークンを取得します。 また、承認ファイルに必要なアプリケーション ID、認証キー、テナント ID を控えておく必要があります。

1. ソリューション エクスプローラーで、 *[myDotnetProject]*  >  **[追加]**  >  **[新しい項目]** を右クリックしてから、 *[Visual C# アイテム]* で **[テキスト ファイル]** を選択します。 ファイルに *azureauth.properties* と名前を付けて、 **[追加]** をクリックします。
2. 次の承認プロパティを追加します。

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    **&lt;subscription-id&gt;** をサブスクリプション ID に、 **&lt;application-id&gt;** を Active Directory アプリケーション ID に、 **&lt;authentication-key&gt;** をアプリケーション キーに、 **&lt;tenant-id&gt;** をテナント識別子に置き換えます。

3. azureauth.properties ファイルを保存します。
4. AZURE_AUTH_LOCATION という名前の Windows 環境変数に、作成した承認ファイルへの完全なパスを設定します。たとえば、次の PowerShell コマンドを使用できます。

    ```powershell
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

    

## <a name="create-the-management-client"></a>管理クライアントを作成する

1. 作成したプロジェクトの Program.cs ファイルを開きます。 次に、ファイルの先頭にある既存のステートメントに次の using ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. 管理クライアントを作成するには、次のコードを Main メソッドに追加します。

    ```csharp
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>リソース グループの作成

アプリケーションの値を指定するには、Main メソッドにコードを追加します。

```csharp
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

テンプレートとパラメーターは、Azure のストレージ アカウントからデプロイされます。 このステップでは、アカウントを作成して、ファイルをアップロードします。 

アカウントを作成するには、次のコードを Main メソッドに追加します。

```csharp
string storageAccountName = SdkContext.RandomResourceName("st", 10);

Console.WriteLine("Creating storage account...");
var storage = azure.StorageAccounts.Define(storageAccountName)
    .WithRegion(Region.USWest)
    .WithExistingResourceGroup(resourceGroup)
    .Create();

var storageKeys = storage.GetKeys();
string storageConnectionString = "DefaultEndpointsProtocol=https;"
    + "AccountName=" + storage.Name
    + ";AccountKey=" + storageKeys[0].Value
    + ";EndpointSuffix=core.windows.net";

var account = CloudStorageAccount.Parse(storageConnectionString);
var serviceClient = account.CreateCloudBlobClient();

Console.WriteLine("Creating container...");
var container = serviceClient.GetContainerReference("templates");
container.CreateIfNotExistsAsync().Wait();
var containerPermissions = new BlobContainerPermissions()
    { PublicAccess = BlobContainerPublicAccessType.Container };
container.SetPermissionsAsync(containerPermissions).Wait();

Console.WriteLine("Uploading template file...");
var templateblob = container.GetBlockBlobReference("CreateVMTemplate.json");
templateblob.UploadFromFileAsync("..\\..\\CreateVMTemplate.json").Result();

Console.WriteLine("Uploading parameters file...");
var paramblob = container.GetBlockBlobReference("Parameters.json");
paramblob.UploadFromFileAsync("..\\..\\Parameters.json").Result();
```

## <a name="deploy-the-template"></a>テンプレートのデプロイ

作成されたストレージ アカウントからテンプレートとパラメーターをデプロイします。 

テンプレートをデプロイするには、次のコードを Main メソッドに追加します。

```csharp
var templatePath = "https://" + storageAccountName + ".blob.core.windows.net/templates/CreateVMTemplate.json";
var paramPath = "https://" + storageAccountName + ".blob.core.windows.net/templates/Parameters.json";
var deployment = azure.Deployments.Define("myDeployment")
    .WithExistingResourceGroup(groupName)
    .WithTemplateLink(templatePath, "1.0.0.0")
    .WithParametersLink(paramPath, "1.0.0.0")
    .WithMode(Microsoft.Azure.Management.ResourceManager.Fluent.Models.DeploymentMode.Incremental)
    .Create();
Console.WriteLine("Press enter to delete the resource group...");
Console.ReadLine();
```

## <a name="delete-the-resources"></a>リソースの削除

Azure で使用されるリソースに対して課金されるため、不要になったリソースは削除することを常にお勧めします。 リソース グループから各リソースを個別に削除する必要はありません。 リソース グループを削除すると、そのすべてのリソースが自動的に削除されます。 

リソース グループを削除するには、次のメソッドを Main メソッドに追加します。

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>アプリケーションの実行

このコンソール アプリケーションが実行を開始してから完全に終了するまでには、約 5 分かかります。 

1. コンソール アプリケーションを実行するには、 **[開始]** をクリックします。

2. **Enter** キーを押してリソースの削除を開始する前に、Azure Portal でリソースの作成状況を確認することもできます。 デプロイに関する情報を参照するには、デプロイ状態をクリックします。

## <a name="next-steps"></a>次の手順

* デプロイに問題がある場合は、次の手順として、「[Troubleshoot common Azure deployment errors with Azure Resource Manager](../../resource-manager-common-deployment-errors.md)」(Azure Resource Manager を使用した Azure のデプロイで発生する一般的なエラーのトラブルシューティング) を参照してください。
* 「[C# を使用した Azure 仮想マシンのデプロイ](csharp.md)」で、仮想マシンとそれをサポートするリソースのデプロイ方法を確認します。