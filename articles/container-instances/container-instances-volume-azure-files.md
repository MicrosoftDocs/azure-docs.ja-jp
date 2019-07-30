---
title: Azure Container Instances での Azure Files ボリュームのマウント
description: Azure Files ボリュームをマウントして、Azure Container Instances で状態を保持する方法について説明します
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 25cac6a66baeb1587e4b5ba3f0923ca9c4394706
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325500"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Azure Container Instances に Azure ファイル共有をマウントする

既定では、Azure Container Instances はステートレスです。 コンテナーがクラッシュまたは停止すると、すべての状態が失われます。 コンテナーの有効期間後も状態を保持するには、外部ストアからボリュームをマウントする必要があります。 この記事では、Azure Container Instances で使用できるように [Azure Files](../storage/files/storage-files-introduction.md) で作成された Azure ファイル共有をマウントする方法について説明します。 Azure Files はクラウドで、業界標準の Server Message Block (SMB) プロトコルを介してアクセスできる、完全に管理されたファイル共有を提供します。 Azure Container Instances で Azure ファイル共有を使用することで、 Azure 仮想マシンで Azure ファイル共有を使用するのと同様のファイル共有機能を提供します。

> [!NOTE]
> Azure ファイル共有のマウントは現在、Linux コンテナーに限定されています。 Microsoft ではすべての機能を Windows コンテナーにも取り入れるように取り組んでいますが、現在のプラットフォームの違いは、[概要](container-instances-overview.md#linux-and-windows-containers)に関するページで確認できます。

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する

Azure Container Instances で Azure ファイル共有を使用するには、まず、ファイル共有を作成する必要があります。 次のスクリプトを実行して、ファイル共有および共有自体をホストするためのストレージ アカウントを作成してください。 ストレージ アカウント名はグローバルに一意にする必要があるため、このスクリプトは、ベース文字列にランダムな値を追加します。

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>ストレージの資格情報の取得

Azure Container Instances で Azure ファイル共有をボリュームとしてマウントするには、ストレージ アカウント名、共有名、ストレージ アクセス キーの 3 つの値が必要です。

上記のスクリプトを使用した場合、ストレージ アカウント名は $ACI_PERS_STORAGE_ACCOUNT_NAME 変数に格納されました。 アカウント名を表示するには、次のように入力します。

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

共有名は既にわかっているため (上記のスクリプトでは *acishare* として定義)、あとはストレージ アカウント キーです。このキーを見つけるには、次のコマンドを使用します。

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume---cli"></a>コンテナーのデプロイとボリュームのマウント - CLI

Azure CLI を使用して Azure ファイル共有をコンテナー内のボリュームとしてマウントするには、[az container create][az-container-create] でコンテナーを作成する際に、共有とボリュームのマウント ポイントを指定します。 前述の手順に従った場合、次のコマンドを使ってコンテナーを作成すれば、先ほど作成した共有をマウントすることができます。

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

`--dns-name-label` 値は、コンテナー インスタンスを作成する Azure リージョン内で一意である必要があります。 コマンドを実行したときに **DNS 名ラベル**のエラー メッセージが表示された場合は、前述のコマンドの値を更新してください。

## <a name="manage-files-in-mounted-volume"></a>マウントしたボリューム内のファイルの管理

コンテナーが起動したら、Microsoft [aci-hellofiles][aci-hellofiles] image to create small text files in the Azure file share at the mount path you specified. Obtain the web app's fully qualified domain name (FQDN) with the [az container show][az-container-show] コマンドを使用してデプロイされる単純な Web アプリを使用できます。

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

アプリを使用してテキストを保存したら、[Azure portal][portal] or a tool like the [Microsoft Azure Storage Explorer][storage-explorer] を使用して、ファイル共有に書き込まれるファイルを取得して検査することができます。

## <a name="deploy-container-and-mount-volume---yaml"></a>コンテナーのデプロイとボリュームのマウント - YAML

Azure CLI と [YAML テンプレート](container-instances-multi-container-yaml.md)を使用して、コンテナー グループをデプロイしてコンテナー内にボリュームをマウントすることもできます。 複数のコンテナーから成るコンテナー グループをデプロイするときは、YAML テンプレートによるデプロイ方法が推奨されます。

以下の YAML テンプレートは、`aci-hellofiles` イメージで作成されたコンテナーを 1 つ含むコンテナー グループを定義しています。 コンテナーでは、以前にボリュームとして作成された Azure ファイル共有 *acishare* がマウントされます。 示されているように、ファイル共有をホストするストレージ アカウントの名前とストレージ キーを入力します。 

CLI の例のように、`dnsNameLabel` 値は、コンテナー インスタンスを作成する Azure リージョン内で一意である必要があります。 必要に応じて、YAML ファイル内の値を更新します。

```yaml
apiVersion: '2018-10-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

この YAML テンプレートを使ってデプロイするには、上記の YAML を `deploy-aci.yaml` という名前のファイルに保存し、`--file` パラメーターを指定して [az container create][az-container-create] コマンドを実行します。

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>コンテナーのデプロイとボリュームのマウント - Resource Manager

CLI と YAML によるデプロイに加え、Azure [Resource Manager テンプレート](/azure/templates/microsoft.containerinstance/containergroups)を使って、コンテナー グループをデプロイしてコンテナー内にボリュームをマウントすることもできます。

最初に、テンプレートのコンテナー グループ `properties` セクションに `volumes` 配列を入力します。 

次に、ボリュームをマウントする各コンテナーに対して、コンテナー定義の `properties` セクションで `volumeMounts` 配列を設定します。

以下の Resource Manager テンプレートは、`aci-hellofiles` イメージで作成されたコンテナーを 1 つ含むコンテナー グループを定義しています。 コンテナーでは、以前にボリュームとして作成された Azure ファイル共有 *acishare* がマウントされます。 示されているように、ファイル共有をホストするストレージ アカウントの名前とストレージ キーを入力します。 

前の例のように、`dnsNameLabel` 値は、コンテナー インスタンスを作成する Azure リージョン内で一意である必要があります。 必要に応じて、テンプレート内の値を更新します。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

この Resource Manager テンプレートを使ってデプロイするには、上記の JSON を `deploy-aci.json` という名前のファイルに保存し、`--template-file` パラメーターを指定して [az group deployment create][az-group-deployment-create] コマンドを実行します。

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>複数ボリュームのマウント

コンテナー インスタンスに複数のボリュームをマウントするには、[Azure Resource Manager テンプレート](/azure/templates/microsoft.containerinstance/containergroups)または YAML ファイルを使用してデプロイを行う必要があります。 テンプレートまたは YAML ファイルを使用するには、テンプレートの `properties` セクションで `volumes` 配列を設定することにより、共有の詳細を指定し、ボリュームを定義します。 

たとえば、*share1* および *share2* という 2 つの Azure Files 共有をストレージ アカウント *myStorageAccount* に作成した場合、Resource Manager テンプレート内の `volumes` 配列は次のようになります。

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

次に、ボリュームをマウントするコンテナー グループ内の各コンテナーに対して、コンテナー定義の `properties` セクションで `volumeMounts` 配列を設定します。 たとえば、以下は、前に定義した 2 つのボリューム *myvolume1* と *myvolume2* をマウントします。

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>次の手順

Azure Container Instances にその他の種類のボリュームをマウントする方法について学習してください。

* [Azure Container Instances に emptyDir ボリュームをマウントする](container-instances-volume-emptydir.md)
* [Azure Container Instances に gitRepo ボリュームをマウントする](container-instances-volume-gitrepo.md)
* [Azure Container Instances にシークレット ボリュームをマウントする](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create