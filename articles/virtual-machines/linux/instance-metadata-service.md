---
title: Azure Instance Metadata Service
description: VM のコンピューティング、ネットワーク、および今後のメンテナンス イベントに関する情報を取得するための RESTful インターフェイスです。
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/29/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: bb9bc978e49cddab13ab1e4f7ec4f0b74d369ac1
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705845"
---
# <a name="azure-instance-metadata-service-imds"></a>Azure Instance Metadata Service (IMDS)

Azure Instance Metadata Service (IMDS) は、現在実行中の仮想マシン インスタンスに関する情報を提供し、仮想マシンの管理と構成に使用できます。
この情報には、SKU、ストレージ、ネットワークの構成、今後のメンテナンス イベントなどがあります。 使用できるデータの完全な一覧については、「[メタデータ API](#metadata-apis)」を参照してください。
Instance Metadata Service は、仮想マシンと仮想マシン スケール セット インスタンスを実行するために使用できます。 [Azure Resource Manager](/rest/api/resources/) を使用して作成/管理されている VM は、すべての API でサポートされています。 従来の (ARM 以外の) VM は、構成証明済みエンドポイントとネットワーク エンドポイントでのみサポートされ、構成証明済みでは限定された範囲内でのみサポートされています。

Azure の IMDS は、既知のルーティング不可能な IP アドレス (`169.254.169.254`) で利用できる REST エンドポイントです。VM 内からのみアクセスできます。 VM と IMDS 間の通信がホストから離れることはありません。
IMDS に対してクエリを実行する場合は、HTTP クライアントから VM 内の Web プロキシをバイパスし、`169.254.169.254` を [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) と同じように扱うことがベスト プラクティスです。

## <a name="security"></a>セキュリティ

Instance Metadata Service エンドポイントには、実行中の仮想マシン インスタンスからのみ、ルーティング不可能な IP アドレスでアクセスできます。 さらに、`X-Forwarded-For` ヘッダーがあるすべての要求は、サービスによって拒否されます。
また、実際の要求が意図しないリダイレクトの一部として行われたのではなく、直接意図されたものであったことを明確に示すために、要求に `Metadata: true`ヘッダーを含める必要があります。

> [!IMPORTANT]
> Instance Metadata Service は、機密データ用のチャネルではありません。 このエンドポイントは、VM 上のすべてのプロセスに対して開かれています。 このサービスを通じて公開される情報は、VM 内で実行されているすべてのアプリケーションに対する共有情報と見なす必要があります。

## <a name="usage"></a>使用法

### <a name="accessing-azure-instance-metadata-service"></a>Azure Instance Metadata Service へのアクセス

Instance Metadata Service にアクセスするには、[Azure Resource Manager](/rest/api/resources/) または [Azure ポータル](https://portal.azure.com)から VM を作成し、以下のサンプルに従います。
IMDS のクエリ方法のその他の例については、[Azure Instance Metadata のサンプル](https://github.com/microsoft/azureimds)を参照してください。

インスタンスのすべてのメタデータを取得するサンプルコードを次に示します。特定のデータ ソースにアクセスするには、「[メタデータ API](#metadata-apis)」セクションを参照してください。 

**Request**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-06-01"
```

**Response**

> [!NOTE]
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osType": "linux",
        "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
        "plan": {
            "name": "planName",
            "product": "planProduct",
            "publisher": "planPublisher"
        },
        "platformFaultDomain": "36",
        "platformUpdateDomain": "42",
        "publicKeys": [{
                "keyData": "ssh-rsa 0",
                "path": "/home/user/.ssh/authorized_keys0"
            },
            {
                "keyData": "ssh-rsa 1",
                "path": "/home/user/.ssh/authorized_keys1"
            }
        ],
        "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
        "resourceGroupName": "macikgo-test-may-23",
        "resourceId": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
        "securityProfile": {
            "secureBootEnabled": "true",
            "virtualTpmEnabled": "false"
        },
        "sku": "Windows-Server-2012-R2-Datacenter",
        "storageProfile": {
            "dataDisks": [{
                "caching": "None",
                "createOption": "Empty",
                "diskSizeGB": "1024",
                "image": {
                    "uri": ""
                },
                "lun": "0",
                "managedDisk": {
                    "id": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampledatadiskname",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }],
            "imageReference": {
                "id": "",
                "offer": "UbuntuServer",
                "publisher": "Canonical",
                "sku": "16.04.0-LTS",
                "version": "latest"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "createOption": "FromImage",
                "diskSizeGB": "30",
                "diffDiskSettings": {
                    "option": "Local"
                },
                "encryptionSettings": {
                    "enabled": "false"
                },
                "image": {
                    "uri": ""
                },
                "managedDisk": {
                    "id": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampleosdiskname",
                "osType": "Linux",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }
        },
        "subscriptionId": "8d10da13-8125-4ba9-a717-bf7490507b3d",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    }
}
```

### <a name="data-output"></a>データ出力

既定では、Instance Metadata Service は JSON 形式でデータを返します (`Content-Type: application/json`)。 ただし、一部の API では、要求された場合に異なる形式でデータを返すことができます。
次の表は、API がサポートできるその他のデータ形式を示しています。

API | 既定のデータ形式 | その他の形式
--------|---------------------|--------------
/attested | json | なし
/identity | json | なし
/instance | json | text
/scheduledevents | json | なし

既定以外の応答形式にアクセスするには、要求のクエリ文字列パラメーターとして要求の形式を指定します。 次に例を示します。

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> /metadata/instance のリーフ ノードの場合、`format=json` は機能しません。 これらのクエリでは、既定の形式は json なので、`format=text` を明示的に指定する必要があります。

### <a name="versioning"></a>バージョン管理

Instance Metadata Service はバージョン管理されています。HTTP 要求で API バージョンを指定することは必須です。

サポートされる API のバージョンは次のとおりです。 
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01

新しいバージョンがリリースされる場合、すべてのリージョンにロールアウトされるまでには時間がかかります。

新しいバージョンが追加されても、特定のデータ形式への依存関係がスクリプトにある場合、互換性を確保するために古いバージョンにもアクセスできます。

バージョンが指定されていない場合は、最新のサポートされているバージョンの一覧と共にエラーが返されます。

> [!NOTE]
> 応答は JSON 文字列です。 次の例は、バージョンが指定されていない場合のエラー状態を示しています。応答は読みやすくするために整えられています。

**Request**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance"
```

**Response**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

## <a name="metadata-apis"></a>メタデータ API

Metadata Service には、さまざまなデータ ソースを表す複数の API があります。

API | 説明 | 導入されたバージョン
----|-------------|-----------------------
/attested | 「[構成証明済みデータ](#attested-data)」をご覧ください | 2018-10-01
/identity | [アクセス トークンの取得](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)に関するページを参照してください | 2018-02-01
/instance | 「[インスタンス API](#instance-api)」を参照してください | 2017-04-02
/scheduledevents | [スケジュールされたイベント](scheduled-events.md)に関する記事を参照してください。 | 2017-08-01

## <a name="instance-api"></a>インスタンス API

インスタンス API は、VM、ネットワーク、ストレージなど、VM インスタンスの重要なメタデータを公開します。 instance/compute を介して次のカテゴリにアクセスできます。

Data | 説明 | 導入されたバージョン
-----|-------------|-----------------------
azEnvironment | VM が実行されている Azure 環境 | 2018-10-01
customData | この機能は現在無効になっています。 使用できるようになり次第、このドキュメントを更新する予定です。 | 2019-02-01
isHostCompatibilityLayerVm | ホストの互換性レイヤーで VM が実行されているかどうかを示します | 2020-06-01
location | VM を実行中の Azure リージョン | 2017-04-02
name | VM の名前 | 2017-04-02
offer | VM イメージのオファーの情報。Azure イメージ ギャラリーからデプロイされるイメージについてのみ存在します。 | 2017-04-02
osType | Linux または Windows | 2017-04-02
placementGroupId | お使いの仮想マシン スケール セットの[配置グループ](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
plan | VM が Azure Marketplace イメージである場合、[プラン](/rest/api/compute/virtualmachines/createorupdate#plan)にはその名前、製品、および発行元が含まれています | 2018-04-02
platformUpdateDomain |  VM を実行中の[更新ドメイン](manage-availability.md) | 2017-04-02
platformFaultDomain | VM を実行中の[障害ドメイン](manage-availability.md) | 2017-04-02
provider | VM のプロバイダー | 2018-10-01
publicKeys | VM とパスに割り当てられた[公開キーのコレクション](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) | 2018-04-02
publisher | VM イメージの発行元 | 2017-04-02
resourceGroupName | お使いの仮想マシンの[リソース グループ](../../azure-resource-manager/management/overview.md) | 2017-08-01
resourceId | リソースの[完全修飾](/rest/api/resources/resources/getbyid) ID | 2019-03-11
sku | VM イメージの特定の SKU | 2017-04-02
securityProfile.secureBootEnabled | VM で UEFI セキュア ブートが有効になっているかどうかを示します | 2020-06-01
securityProfile.virtualTpmEnabled | VM で仮想トラステッド プラットフォーム モジュール (TPM) が有効になっているかどうかを示します | 2020-06-01
storageProfile | [ストレージ プロファイル](#storage-metadata)を参照してください | 2019-06-01
subscriptionId | 仮想マシンの Azure サブスクリプション | 2017-08-01
tags | お使いの仮想マシンの[タグ](../../azure-resource-manager/management/tag-resources.md)  | 2017-08-01
tagsList | プログラムによる解析を簡単にするために JSON 配列として書式設定されたタグ  | 2019-06-04
version | VM イメージのバージョン | 2017-04-02
vmId | VM の[一意の識別子](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
vmScaleSetName | お使いの仮想マシン スケール セットの[仮想マシン スケール セット名](../../virtual-machine-scale-sets/overview.md) | 2017-12-01
vmSize | [VM サイズ](../sizes.md) | 2017-04-02
ゾーン | 仮想マシンの[可用性ゾーン](../../availability-zones/az-overview.md) | 2017-12-01

### <a name="sample-1-tracking-vm-running-on-azure"></a>サンプル 1:Azure で動作している VM の追跡

サービス プロバイダーは、ソフトウェアを実行している VM の数を追跡する必要があったり、VM の一意性を追跡する必要のあるエージェントを使用していたりする場合があります。 VM の一意の ID を取得できるようにするには、Instance Metadata Service から `vmId` フィールドを使用します。

**Request**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Response**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-containers-data-partitions-based-faultupdate-domain"></a>サンプル 2:障害/更新ドメインに基づくコンテナー、データ パーティションの配置

特定のシナリオでは、異なるレプリカの配置が非常に重要です。 たとえば、[Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) を介した[ HDFS レプリカの配置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)またはコンテナーの配置では、VM が実行されている `platformFaultDomain` と `platformUpdateDomain` を把握しておかなければならない場合があります。
これらの決定を行うために、インスタンスの[可用性ゾーン](../../availability-zones/az-overview.md)を使用することもできます。
このデータは、Instance Metadata Service を使用して直接照会できます。

**Request**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Response**

```text
0
```

### <a name="sample-3-getting-more-information-about-the-vm-during-support-case"></a>サンプル 3: サポート ケースの際の VM に関する詳細情報の取得

サービス プロバイダーとして、VM の詳細情報を得る必要があるサポート コールを受けることがあります。 顧客にコンピューティング メタデータの共有を依頼すると、サポート担当者が Azure 上の VM の種類を確認するための基本情報を得られる場合があります。

**Request**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**Response**

> [!NOTE]
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```json
{
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
}
```

### <a name="sample-4-getting-azure-environment-where-the-vm-is-running"></a>サンプル 4: VM が実行されている Azure 環境の取得

Azure には、[Azure Government](https://azure.microsoft.com/overview/clouds/government/) など多数のソブリン クラウドがあります。 ランタイムの決定を行うために、Azure 環境が必要な場合があります。 次の例では、この動作を実現する方法を示します。

**Request**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Response**

```text
AzurePublicCloud
```

クラウドと Azure 環境の値を以下に示します。

 クラウド   | Azure 環境
---------|-----------------
[一般公開されている全世界のすべての Azure リージョン](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>ネットワーク メタデータ 

ネットワーク メタデータは、インスタンス API の一部です。 instance/network エンドポイントを介して次のネットワーク カテゴリを使用できます。

Data | 説明 | 導入されたバージョン
-----|-------------|-----------------------
ipv4/privateIpAddress | VM のローカル IPv4 アドレス | 2017-04-02
ipv4/publicIpAddress | VM のパブリック IPv4 アドレス | 2017-04-02
subnet/address | VM のサブネット アドレス | 2017-04-02
subnet/prefix | サブネットのプレフィックス (24 など) | 2017-04-02
ipv6/ipAddress | VM のローカル IPv6 アドレス | 2017-04-02
macAddress | VM の mac アドレス | 2017-04-02

> [!NOTE]
> すべての API の応答は、JSON 文字列です。 次のすべての例の応答は、読みやすくするために整えられています。

#### <a name="sample-1-retrieving-network-information"></a>サンプル 1:ネットワーク情報の取得

**Request**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Response**

> [!NOTE]
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="sample-2-retrieving-public-ip-address"></a>サンプル 2:パブリック IP アドレスの取得

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>ストレージ メタデータ

ストレージ メタデータは、instance/compute/storageProfile エンドポイントの下にあるインスタンス API の一部です。
VM に関連付けられているストレージ ディスクの詳細を提供します。 

VM のストレージ プロファイルは、イメージ参照、OS ディスク、データ ディスクの 3 つのカテゴリに分類されます。

イメージ参照オブジェクトには、OS イメージに関する次の情報が含まれています。

Data    | 説明
--------|-----------------
id      | Resource ID
offer   | プラットフォームまたは marketplace イメージのオファー
publisher | イメージ発行者
sku     | イメージ SKU
version | プラットフォームまたは marketplace イメージのバージョン

OS ディスク オブジェクトには、VM によって使用される OS ディスクに関する次の情報が含まれています。

Data    | 説明
--------|-----------------
caching | キャッシュの要件
createOption | VM が作成された方法に関する情報
diffDiskSettings | 一時ディスクの設定
diskSizeGB | ディスクのサイズ (GB)
image   | ソース ユーザー イメージの仮想ハード ディスク
lun     | ディスクの論理ユニット番号
managedDisk | マネージド ディスクのパラメーター
name    | ディスク名
vhd     | 仮想ハード ディスク
writeAcceleratorEnabled | ディスクで writeAccelerator が有効になっているかどうか

データ ディスク アレイには、VM に接続されているデータ ディスクの一覧が含まれています。 各データ ディスク オブジェクトには、次の情報が含まれています。

Data    | 説明
--------|-----------------
caching | キャッシュの要件
createOption | VM が作成された方法に関する情報
diffDiskSettings | 一時ディスクの設定
diskSizeGB | ディスクのサイズ (GB)
encryptionSettings | ディスクの暗号化の設定
image   | ソース ユーザー イメージの仮想ハード ディスク
managedDisk | マネージド ディスクのパラメーター
name    | ディスク名
osType  | ディスクに含まれている OS の種類
vhd     | 仮想ハード ディスク
writeAcceleratorEnabled | ディスクで writeAccelerator が有効になっているかどうか

次の例は、VM のストレージ情報に対してクエリを実行する方法を示しています。

**Request**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**Response**

> [!NOTE]
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

## <a name="vm-tags"></a>VM のタグ

VM タグは、instance/compute/tags エンドポイントの下にあるインスタンス API に含まれています。
論理的に分類するために Azure VM にタグが適用されている場合があります。 次の要求を使用して、VM に割り当てられたタグを取得できます。

**Request**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Response**

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags` フィールドは、セミコロンで区切られたタグを含む文字列です。 この出力は、タグ自体でセミコロンが使用されている場合に問題になることがあります。 プログラムでタグを抽出するようにパーサーが作成されている場合は、`tagsList` フィールドを使用する必要があります。 `tagsList` フィールドは区切り記号のない JSON 配列であるため、解析が容易になります。

**Request**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04"
```

**Response**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

## <a name="attested-data"></a>構成証明済みデータ

Instance Metadata Service によって提供されるシナリオの一部は、提供されるデータが Azure から送信されたものであることを保証するものです。 マーケットプレースのイメージが Azure で実行されるイメージであることを確認できるよう、この情報の一部に署名します。

### <a name="sample-1-getting-attested-data"></a>サンプル 1:構成証明済みデータの取得

> [!NOTE]
> すべての API の応答は、JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

**Request**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

api-version は必須フィールドです。 サポートされる API バージョンについては、[使用法のセクション](#usage)を参照してください。
nonce は、省略可能な 10 桁の文字列です。 指定しない場合は、その代わりに IMDS によって現在の UTC タイムスタンプが返されます。

> [!NOTE]
> IMDS のキャッシュ メカニズムにより、以前にキャッシュされた nonce 値が返される場合もあります。

**Response**

> [!NOTE]
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

signature BLOB は、ドキュメントの [pkcs7](https://aka.ms/pkcs7) で署名されたバージョンです。 特定の VM 固有の詳細と共に署名に使用される証明書が含まれています。 ARM VM の場合は、ドキュメントの作成と有効期限のための vmId、sku、nonce、subscriptionId、timeStamp とイメージに関するプラン情報が含まれています。 プラン情報は Azure Marketplace のイメージにのみ設定されます。 従来の (ARM 以外の) VM の場合は、vmId の設定のみが保証されます。 証明書は、応答から抽出でき、応答が有効で Azure から送られたものであることを検証するために使用できます。
ドキュメントには次のフィールドが含まれています。

Data | 説明
-----|------------
nonce | 必要に応じて、要求で指定できる文字列。 nonce が指定されなかった場合、現在の UTC タイムスタンプが使用されます
plan | [Azure Marketplace イメージ プラン](/rest/api/compute/virtualmachines/createorupdate#plan)。 プラン ID (名前)、製品イメージまたはオファー (製品)、およびパブリッシャー ID (パブリッシャー) が含まれます。
timestamp/createdOn | 署名されたドキュメントが作成されたときの UTC タイムスタンプ
timestamp/expiresOn | 署名されたドキュメントの有効期限が切れるときの UTC タイムスタンプ
vmId |  VM の[一意の識別子](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
subscriptionId | `2019-04-30` で導入された、仮想マシンの Azure サブスクリプション
sku | VM イメージの特定の SKU、`2019-11-01` で導入

> [!NOTE]
> 従来の (ARM 以外の) VM の場合は、vmId の設定のみが保証されます。

### <a name="sample-2-validating-that-the-vm-is-running-in-azure"></a>サンプル 2:VM が Azure で実行されていることの検証

Marketplace ベンダーは、自分たちのソフトウェアが Azure でのみ実行されるようにライセンスされていることを確認することを望みます。 誰かがオンプレミスの外に VHD をコピーした場合、それを検出する機能が必要です。 Instance Metadata Service を呼び出すことによって、Marketplace ベンダーは、応答が Azure からのみであることを保証する署名付きデータを取得できます。

> [!NOTE]
> jq をインストールする必要があります。

**Request**

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2019-04-30" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Response**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

署名が Microsoft Azure からのものであることを確認し、証明書チェーンにエラーがないか確認します。

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

> [!NOTE]
> IMDS のキャッシュ メカニズムにより、以前にキャッシュされた nonce 値が返される場合もあります。

最初の要求で nonce パラメーターを指定した場合、署名されたドキュメントの nonce を比較できます。

> [!NOTE]
> パブリック クラウド用の証明書とソブリン クラウド用の証明書は異なります。

クラウド | Certificate
------|------------
[一般公開されている全世界のすべての Azure リージョン](https://azure.microsoft.com/regions/) | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *.metadata.azure.cn
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                | *.metadata.microsoftazure.de

> [!NOTE]
> 署名に使用される証明書に関する既知の問題があります。 証明書では、パブリック クラウドに対して `metadata.azure.com` が完全に一致していない可能性があります。 そのため、証明書の検証では、任意の `.metadata.azure.com` サブドメインからの共通名を許可する必要があります。

検証時、ネットワークの制約が原因で中間証明書をダウンロードできない場合は、中間証明書をピン留めできます。 ただし、Azure では、標準 PKI 手順に従って、証明書がロールオーバーされます。 ピン留めされた証明書は、ロールオーバーの実行時に更新する必要があります。 中間証明書を更新する変更を計画すると、そのたびに、Azure ブログが更新され、Azure の顧客に通知されます。 中間証明書は、[こちら](https://www.microsoft.com/pki/mscorp/cps/default.htm)で確認できます。 中間証明書は、リージョンごとに異なる場合があります。

> [!NOTE]
> Azure China 21Vianet 用の中間証明書は、Baltimore ではなく DigiCert Global Root CA からのものです。
また、ルート チェーン機関の変更の一環として Azure China 用の中間証明書をピン留めした場合は、中間証明書を更新する必要があります。

## <a name="managed-identity-via-metadata-service"></a>メタデータ サービス経由のマネージド ID

システム割り当てマネージド ID を VM で有効にするか、1 つ以上のユーザー割り当てマネージド ID を VM に割り当てることができます。
これでマネージド ID のトークンを Instance Metadata Service から要求できるようになります。 これらのトークンを使用して、Azure Key Vault などの他の Azure サービスで認証することができます。

この機能を有効にするための詳細な手順については、[アクセストークンの取得](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)に関するページを参照してください。

## <a name="scheduled-events-via-metadata-service"></a>メタデータ サービス経由のスケジュール化されたイベント
スケジュール化されたイベントの状態をメタデータ サービス経由で取得できます。その後、ユーザーは、これらのイベントで実行する一連のアクションを指定できます。  詳細については、[スケジュール化されたイベント](scheduled-events.md)に関するページを参照してください。 

## <a name="regional-availability"></a>リージョン別の提供状況

このサービスは、すべての Azure クラウドで**一般提供**されています。

## <a name="sample-code-in-different-languages"></a>さまざまな言語のサンプル コード

VM 内でさまざまな言語を使用してメタデータ サービスを呼び出すサンプル:

Language      | 例
--------------|----------------
Bash          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Go            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="error-and-debugging"></a>エラーとデバッグ

見つからないデータ要素または無効な形式の要求がある場合、Instance Metadata Service は標準 HTTP エラーを返します。 次に例を示します。

HTTP 状態コード | 理由
----------------|-------
200 OK |
400 Bad Request | リーフ ノードのクエリ時に `Metadata: true` ヘッダーがないか、パラメーター `format=json` がありません
404 見つかりません | 要求された要素は存在しません
405 Method Not Allowed | `GET` 要求のみがサポートされています
410 削除 | しばらくしてから再試行してください (最長 70 秒)
429 Too Many Requests | 現在 API は、1 秒あたり最大 5 つのクエリをサポートしています
500 Service Error     | しばらくしてからやり直してください

### <a name="known-issues-and-faq"></a>既知の問題とよくあるご質問

1. `400 Bad Request, Required metadata header not specified` エラーが発生します。 これはどういう意味でしょうか。
   * Instance Metadata Service では、要求に `Metadata: true` ヘッダーを渡す必要があります。 このヘッダーを REST 呼び出しに渡すと、Instance Metadata Service へのアクセスが許可されます。
1. 使用している VM に関するコンピューティング情報を取得できないのはなぜですか。
   * 現時点では、Instance Metadata Service は、Azure Resource Manager で作成されたインスタンスのみをサポートします。 今後、クラウド サービス VM に対するサポートが追加される可能性があります。
1. 少し前に、Azure Resource Manager を介して仮想マシンを作成しました。 コンピューティング メタデータ情報が表示されないのはなぜですか。
   * 2016 年 9 月以降に作成されたすべての VM については、[タグ](../../azure-resource-manager/management/tag-resources.md)を追加して、コンピューティング メタデータの表示を開始してください。 それよりも古い VM (2016 年 9 月よりも前に作成された VM) については、VM インスタンスに対して拡張機能またはデータ ディスクを追加/削除してメタデータを更新してください。
1. 新しいバージョンに入力されたすべてのデータが表示されません。
   * 2016 年 9 月以降に作成されたすべての VM については、[タグ](../../azure-resource-manager/management/tag-resources.md)を追加して、コンピューティング メタデータの表示を開始してください。 それよりも古い VM (2016 年 9 月よりも前に作成された VM) については、VM インスタンスに対して拡張機能またはデータ ディスクを追加/削除してメタデータを更新してください。
1. エラー `500 Internal Server Error` または `410 Resource Gone` が発生するのはなぜですか。
   * 指数バック オフ システムまたは「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」で説明されているその他の方法に基づいて、要求を再試行してください。 問題が解決しない場合は、VM の Azure portal でサポートの問題を作成してください。
1. このサービスは仮想マシン スケール セット インスタンスで機能しますか。
   * はい。Metadata Service は、スケール セット インスタンスで利用できます。
1. Virtual Machine Scale Sets でタグを更新しましたが、単一インスタンスの VM とは異なり、インスタンスにタグが表示されません。
   * 現時点では、スケール セットのタグは、再起動、再イメージ化、またはインスタンスに対するディスクの変更の際に VM に対してのみ表示されます。
1. サービスの呼び出しの要求がタイムアウトになりました。
   * メタデータの呼び出しは、VM のプライマリ ネットワーク カードに割り当てられたプライマリ IP アドレスから行う必要があります。 さらに、ルートを変更した場合、VM のローカル ルーティング テーブルに 169.254.169.254/32 アドレスのルートが存在する必要があります。
   * <details>
        <summary>ルーティング テーブルの確認</summary>

        1. `netstat -r` などのコマンドを使用してローカル ルーティング テーブルをダンプし、IMDS エントリを探します (例)。
            ```console
            ~$ netstat -r
            Kernel IP routing table
            Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
            default         _gateway        0.0.0.0         UG        0 0          0 eth0
            168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
            169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
            172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
            ```
        1. `169.254.169.254` のルートが存在することを確認し、対応するネットワーク インターフェイス (`eth0` など) をメモします。
        1. ルーティング テーブル内の対応するインターフェイスのインターフェイス構成をダンプします (構成ファイルの正確な名前は異なっている場合があることに注意してください)。
            ```console
            ~$ cat /etc/netplan/50-cloud-init.yaml
            network:
            ethernets:
                eth0:
                    dhcp4: true
                    dhcp4-overrides:
                        route-metric: 100
                    dhcp6: false
                    match:
                        macaddress: 00:0d:3a:e4:c7:2e
                    set-name: eth0
            version: 2
            ```
        1. 動的 IP を使用している場合は、MAC アドレスをメモします。 静的 IP を使用している場合は、表示されている IP アドレスまたは MAC アドレスをメモできます。
        1. インターフェイスが VM のプライマリ NIC とプライマリ IP に対応していることを確認します。 プライマリ NIC/IP を見つけるには、Azure portal でネットワーク構成を確認するか、[Azure CLI](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-show) を使用して探します。 パブリック IP とプライベート IP (CLI を使用している場合は MAC アドレス) をメモします。 PowerShell CLI の例:
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: ipexample606 True 00-0D-3A-E4-C7-2E
            ```
        1. 一致しない場合は、プライマリ NIC/IP がターゲットになるようにルーティング テーブルを更新します。
    </details>

## <a name="support-and-feedback"></a>サポートとフィードバック

https://feedback.azure.com でフィードバックとコメントをお寄せください。

サービスのサポートを受けるには、時間がかかる再試行の後もメタデータの応答を取得できない VM を管理する Azure portal でサポート問題を作成します。
[問題の種類] に `Management` を使用し、[カテゴリ] として `Instance Metadata Service` を選択します。

![Instance Metadata のサポート](./media/instance-metadata-service/InstanceMetadata-support.png "スクリーンショット: Instance Metadata Service で問題が発生したときにサポート ケースを開く")

## <a name="next-steps"></a>次の手順

各項目の詳細情報
1. [VM のアクセス トークンの取得](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。
1. [スケジュール化されたイベント](scheduled-events.md)