---
title: Service Fabric Mesh アプリで Azure Files ベースのボリュームを使用する
description: Azure CLI を使用して、Azure Files ベースのボリュームをサービス内にマウントして Azure Service Fabric Mesh アプリケーションに状態を保存する方法について説明します。
author: georgewallace
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: gwallace
ms.custom: mvc, devcenter , devx-track-azurecli
ms.openlocfilehash: 40d10568e13ad455bc5178821da80e89f4132e93
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625839"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Service Fabric Mesh アプリケーションで Azure Files ベースのボリュームをマウントする 

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)に関する記事を参照してください。

この記事では、Service Fabric Mesh アプリケーションのサービスで Azure Files ベースのボリュームをマウントする方法について説明します。  Azure Files ボリューム ドライバーは、サービスの状態を保持するためのコンテナーに Azure Files 共有をマウントするために使用される Docker ボリューム ドライバーです。 ボリュームを利用することで、汎用目的のファイル ストレージが提供され、通常のディスク I/O ファイル API を利用してファイルを読み書きすることができます。  アプリケーション データを保存するためのボリュームとオプションの詳細については、[状態の保存](service-fabric-mesh-storing-state.md)に関するページを参照してください。

サービスにボリュームをマウントするには、Service Fabric Mesh アプリケーションでボリューム リソースを作成し、サービスでそのボリュームを参照します。  ボリューム リソースを宣言し、それをサービス リソースで参照する作業は、[YAML ベースのリソース ファイル](#declare-a-volume-resource-and-update-the-service-resource-yaml)か [JSON ベースのデプロイ テンプレート](#declare-a-volume-resource-and-update-the-service-resource-json)で行うことができます。 ボリュームをマウントする前に、最初に Azure ストレージ アカウントと[ファイル共有を Azure Files](../storage/files/storage-how-to-create-file-share.md) で作成します。

## <a name="prerequisites"></a>前提条件
> [!NOTE]
> **Windows RS5 開発マシンへのデプロイに関する既知の問題:** RS5 Windows マシンの PowerShell コマンドレット New-SmbGlobalMapping には、Azurefile ボリュームのマウントに支障をきたす未解決のバグがあります。 以下に示したのは、AzureFile ベースのボリュームをローカル開発マシンにマウントしているときに発生したエラーの例です。
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
この問題を回避するには、1) 次のコマンドを PowerShell 管理者として実行し、2) マシンを再起動します。
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

Azure Cloud Shell または Azure CLI のローカル インストールを使用し、この記事の作業を完了できます。 

この記事で Azure CLI をローカルで使用するには、`az --version` が少なくとも `azure-cli (2.0.43)` を確実に返すようにします。  こちらの[手順](service-fabric-mesh-howto-setup-cli.md)に従って、Azure Service Fabric Mesh CLI 拡張モジュールをインストール (または更新) してください。

Azure にサインインしてサブスクリプションを設定するには:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>ストレージ アカウントとファイル共有を作成する (任意)
Azure Files ボリュームをマウントするには、ストレージ アカウントとファイル共有が必要です。  既存の Azure ストレージ アカウントとファイル共有を使用するか、リソースを作成できます。

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>ストレージ アカウントの名前とキー、ファイル共有の名前を取得する
ストレージ アカウント名、ストレージ アカウント キー、ファイル共有名は、後続のセクションで `<storageAccountName>`、`<storageAccountKey>`、`<fileShareName>` と呼ばれます。 

ストレージ アカウントを一覧表示し、ストレージ アカウントの名前と使用するファイル共有を取得します。
```azurecli-interactive
az storage account list
```

ファイル共有の名前を取得します。
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

ストレージ アカウント キー ("key1") を取得します。
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

これらの値は [Azure portal](https://portal.azure.com) でも見つけることができます。
* `<storageAccountName>` - **[ストレージ アカウント]** に、ファイル共有を作成するときに使用したストレージ アカウントの名前があります。
* `<storageAccountKey>` - **[ストレージ アカウント]** でストレージ アカウントを選択し、 **[アクセス キー]** を選択して **key1** の下の値を使用します。
* `<fileShareName>` - **[ストレージ アカウント名]** でストレージ アカウントを選択し、 **[ファイル]** を選択します。 使用する名前は、作成したファイル共有の名前です。

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>ボリューム リソースを宣言し、サービス リソース (JSON) を更新する

前の手順で見つけた `<fileShareName>`、`<storageAccountName>`、`<storageAccountKey>` 値のパラメーターを追加します。 

アプリケーション リソースのピアとしてボリューム リソースを作成します。 名前とプロバイダーを指定します (Azure Files ベースのボリュームを使用するには "SFAzureFile")。 `azureFileParameters` で、前の手順で見つけた `<fileShareName>`、`<storageAccountName>`、`<storageAccountKey>` 値のパラメーターを指定します。

サービスにボリュームをマウントするには、サービスの `codePackages` 要素に `volumeRefs` を追加します。  `name` はボリュームのリソース ID (あるいは、ボリューム リソースのデプロイ テンプレート パラメーター) であり、volume.yaml リソース ファイルで宣言されているボリュームの名前です。  `destinationPath` は、ボリュームのマウント先となるローカル ディレクトリです。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>ボリューム リソースを宣言し、サービス リソース (YAML) を更新する

アプリケーションの *アプリ リソース* ディレクトリに新しい *volume.yaml* ファイルを追加します。  名前とプロバイダーを指定します (Azure Files ベースのボリュームを使用するには "SFAzureFile")。 `<fileShareName>`、`<storageAccountName>`、`<storageAccountKey>` は、前の手順で見つけた値です。

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

*サービス リソース* ディレクトリの *service.yaml* ファイルを更新し、サービスでボリュームをマウントします。  `volumeRefs` 要素を `codePackages` 要素に追加します。  `name` はボリュームのリソース ID (あるいは、ボリューム リソースのデプロイ テンプレート パラメーター) であり、volume.yaml リソース ファイルで宣言されているボリュームの名前です。  `destinationPath` は、ボリュームのマウント先となるローカル ディレクトリです。

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>次のステップ

- [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) 上の Azure Files ボリューム サンプル アプリケーションを確認します。
- Service Fabric リソース モデルの詳細については、[Service Fabric Mesh リソース モデル](service-fabric-mesh-service-fabric-resources.md)に関するページを参照してください。
- Service Fabric Mesh の詳細については、[Service Fabric Mesh の概要](service-fabric-mesh-overview.md)に関するページを参照してください。
