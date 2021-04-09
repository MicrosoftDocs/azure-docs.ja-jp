---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: 357223751112af03bf797ae9a0e6352a10132ab9
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2021
ms.locfileid: "103464955"
---
Azure Instance Metadata Service (IMDS) によって、現在実行中の仮想マシン インスタンスに関する情報が提供されます。 これを使用して、仮想マシンの管理と構成を行うことができます。
この情報には、SKU、ストレージ、ネットワークの構成、今後のメンテナンス イベントなどがあります。 使用できるデータの完全な一覧については、[エンドポイント カテゴリの概要](#endpoint-categories)に関するページを参照してください。

IMDS は、仮想マシン (VM) のインスタンスと仮想マシン スケール セット インスタンスを実行するために使用できます。 [Azure Resource Manager](/rest/api/resources/) を使用して作成および管理されている VM は、すべてのエンドポイントでサポートされます。 クラシック デプロイ モデルを使用して作成された VM は、構成証明されたカテゴリと、インスタンス カテゴリのネットワーク部分のみでサポートされます。 構成証明されたエンドポイントでのサポートは、制限された範囲内のみとなります。

IMDS は、既知のルーティング不可能な IP アドレス (`169.254.169.254`) で使用できる REST API です。 VM 内からのみアクセスできます。 VM と IMDS 間の通信がホストから離れることはありません。
IMDS に対してクエリを実行する場合は、HTTP クライアントから VM 内の Web プロキシをバイパスし、`169.254.169.254` を [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md) と同様に扱います。

## <a name="usage"></a>使用方法

### <a name="access-azure-instance-metadata-service"></a>Azure Instance Metadata Service にアクセスする

IMDS にアクセスするには、[Azure Resource Manager](/rest/api/resources/) または [Azure ポータル](https://portal.azure.com)から VM を作成し、以下のサンプルを使用します。
その他の例については、「[Azure Instance Metadata Samples (Azure Instance Metadata のサンプル)](https://github.com/microsoft/azureimds)」を参照してください。

インスタンスのすべてのメタデータを取得するサンプル コードを次に示します。 特定のデータ ソースにアクセスするには、使用可能なすべての機能の概要について「[エンドポイント カテゴリ](#endpoint-categories)」を参照してください。

**Request**

> [!IMPORTANT]
> この例では、プロキシをバイパスします。 IMDS に対してクエリを実行する場合は、プロキシをバイパスする **必要があります**。 追加情報については、「[プロキシ](#proxies)」を参照してください。

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | jq
```

---

**Response**

> [!NOTE]
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>セキュリティと認証

Instance Metadata Service には、実行中の仮想マシン インスタンス内からのみ、ルーティング不可能な IP アドレスでアクセスできます。 VM は、それ自体に関連するメタデータまたは機能との相互作用に限定されます。 API は HTTP のみであり、ホストから出ることはありません。

要求が IMDS に確実に直接送信されるようにし、意図しない、または望ましくない要求のリダイレクトを防ぐには、要求を次のように処理する必要があります。
- ヘッダー `Metadata: true` を **含める**
- ヘッダー `X-Forwarded-For` を **含めない**

これらの要件の **両方** が満たされていない要求は、サービスによって拒否されます。

> [!IMPORTANT]
> IMDS は、機密データ用のチャネルでは **ありません**。 API は認証されておらず、VM 上のすべてのプロセスに対してオープン状態になっています。 このサービスを通じて公開される情報は、VM 内で実行されているすべてのアプリケーションに対する共有情報と見なす必要があります。

## <a name="proxies"></a>プロキシ

IMDS をプロキシの背後で使用することは **想定されておらず**、サポートされていません。 ほとんどの HTTP クライアントでは、要求に応じてプロキシを無効にするオプションが提供されています。この機能は、IMDS と通信するときに使用する必要があります。 詳細については、クライアントのドキュメントを参照してください。

> [!IMPORTANT]
> お使いの環境でプロキシ構成がわからない場合でも、**既定のクライアント プロキシ設定をオーバーライドする必要があります**。 プロキシ構成は自動的に検出され、このような構成をバイパスしないと、コンピューターの構成が将来変更された場合に侵害のリスクが生じます。

## <a name="rate-limiting"></a>レート制限

一般に、IMDS への要求は、1 秒あたり 5 つの要求に制限されます。 このしきい値を超える要求は、429 の応答で拒否されます。 [マネージド ID](#managed-identity) カテゴリに対する要求は、1 秒あたり 20 個の要求、同時要求数は 5 個に制限されます。

## <a name="http-verbs"></a>HTTP 動詞

現在、次の HTTP 動詞がサポートされています。

| 動詞 | 説明 |
|------|-------------|
| `GET` | 要求されたリソースの取得

## <a name="parameters"></a>パラメーター

エンドポイントでは、必須パラメーターまたは省略可能なパラメーターがサポートされる場合があります。 詳細については、「[スキーマ](#schema)」と、対象の特定のエンドポイントに関するドキュメントを参照してください。

### <a name="query-parameters"></a>クエリ パラメーター

IMDS エンドポイントでは、HTTP クエリ文字列パラメーターがサポートされています。 次に例を示します。 

```
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

パラメーターを指定します。

| 名前 | 値 |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

クエリ パラメーター名が重複している要求は拒否されます。

### <a name="route-parameters"></a>ルート パラメーター

より大きな JSON BLOB を返す一部のエンドポイントでは、応答のサブセットに絞り込むために、要求エンドポイントへのルート パラメーターの追加がサポートされています。 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
パラメーターは、解析された表現を操作する場合に、JSON オブジェクトを処理するのに使用されるインデックスとキーに対応します。

たとえば、`/metatadata/instance` によって JSON オブジェクトが返されます。
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
                "ipv4": {
                   "ipAddress": [{
                        "privateIpAddress": "10.144.133.132",
                        "publicIpAddress": ""
                    }],
                    "subnet": [{
                        "address": "10.144.133.128",
                        "prefix": "26"
                    }]
                },
                "ipv6": {
                    "ipAddress": [
                     ]
                },
                "macAddress": "0011AAFFBB22"
            },
            ...
        ]
    }
}
```

応答をフィルター処理して compute プロパティのみに絞り込む場合は、次の要求を送信します。 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

同様に、入れ子になったプロパティまたは特定の配列要素に絞り込む場合は、次のようにさらにキーを追加します。 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
これにより、`Network.interface` プロパティの最初の要素に絞り込まれ、次の内容が返されます。

```json
{
    "ipv4": {
       "ipAddress": [{
            "privateIpAddress": "10.144.133.132",
            "publicIpAddress": ""
        }],
        "subnet": [{
            "address": "10.144.133.128",
            "prefix": "26"
        }]
    },
    "ipv6": {
        "ipAddress": [
         ]
    },
    "macAddress": "0011AAFFBB22"
}
```

> [!NOTE]
> リーフ ノードに絞り込む場合、`format=json` は機能しません。 これらのクエリでは、既定の形式は json なので、`format=text` を明示的に指定する必要があります。

## <a name="schema"></a>スキーマ

### <a name="data-format"></a>データ形式

既定では、IMDS によって JSON 形式 (`Content-Type: application/json`) でデータが返されます。 ただし、応答のフィルター処理をサポートするエンドポイント (「[ルート パラメーター](#route-parameters)」を参照) では、`text` 形式もサポートされています。

既定以外の応答形式にアクセスするには、要求のクエリ文字列パラメーターとして要求の形式を指定します。 次に例を示します。

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

JSON 応答では、すべてのプリミティブは `string` 型になり、欠損値または対象外の値は常に含まれますが、空の文字列に設定されます。

### <a name="versioning"></a>バージョン管理

IMDS はバージョン管理されており、HTTP 要求での API バージョンの指定は必須です。 この要件の唯一の例外は[バージョン](#versions) エンドポイントであり、使用可能な API バージョンを動的に取得するために使用できます。

新しいバージョンが追加されても、特定のデータ形式への依存関係がスクリプトにある場合、互換性を確保するために古いバージョンにもアクセスできます。

バージョンを指定しない場合、エラーが表示され、サポートされている最新バージョンの一覧が示されます。
```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

#### <a name="supported-api-versions"></a>サポートされる API バージョン
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
- 2020-07-15
- 2020-09-01
- 2020-10-01
- 2020-12-01

### <a name="swagger"></a>Swagger

IMDS の完全な Swagger 定義は、 https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md で入手できます。

## <a name="regional-availability"></a>リージョン別の提供状況

このサービスは、すべての Azure クラウドで **一般提供** されています。

## <a name="root-endpoint"></a>ルート エンドポイント

ルート エンドポイントは `http://169.254.169.254/metadata` です。

## <a name="endpoint-categories"></a>エンドポイント カテゴリ

IMDS API には、さまざまなデータ ソースを表す複数のエンドポイント カテゴリが含まれており、それぞれに 1 つ以上のエンドポイントが含まれています。 詳細については、各カテゴリを参照してください。

| カテゴリ ルート | 説明 | 導入されたバージョン |
|---------------|-------------|--------------------|
| `/metadata/attested` | 「[構成証明済みデータ](#attested-data)」をご覧ください | 2018-10-01
| `/metadata/identity` | [IMDS 経由でのマネージド ID](#managed-identity) に関するページを参照 | 2018-02-01
| `/metadata/instance` | 「[インスタンス メタデータ](#instance-metadata)」を参照 | 2017-04-02
| `/metadata/loadbalancer` | 「[IMDS を使用した Load Balancer メタデータの取得](#load-balancer-metadata)」を参照 | 2020-10-01
| `/metadata/scheduledevents` | [IMDS 経由のスケジュールされたイベント](#scheduled-events)に関するページを参照 | 2017-08-01
| `/metadata/versions` | 「[バージョン](#versions)」を参照 | 該当なし

## <a name="versions"></a>バージョン

> [!NOTE]
> この機能はバージョン 2020-10-01 と共にリリースされましたが、現在展開中であるため、一部のリージョンではまだ使用できない場合があります。

### <a name="list-api-versions"></a>API バージョンの一覧表示

サポートされている API バージョンのセットを返します。

```
GET /metadata/versions
```

#### <a name="parameters"></a>パラメーター

なし (このエンドポイントはバージョン管理されていません)。

#### <a name="response"></a>Response

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## <a name="instance-metadata"></a>インスタンス メタデータ

### <a name="get-vm-metadata"></a>VM メタデータの取得

コンピューティング、ネットワーク、ストレージなど、VM インスタンスの重要なメタデータを公開します。 

```
GET /metadata/instance
```

#### <a name="parameters"></a>パラメーター

| 名前 | 必須/省略可能 | 説明 |
|------|-------------------|-------------|
| `api-version` | 必須 | 要求を処理するために使用されるバージョン。
| `format` | 省略可能* | 応答の形式 (`json` または `text`)。 *注:要求パラメーターを使用するときに必要になる場合があります。

このエンドポイントでは、[ルート パラメーター](#route-parameters)を使用した応答フィルター処理がサポートされています。

#### <a name="response"></a>Response

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

スキーマの内訳:

**Compute**

| Data | 説明 | 導入されたバージョン |
|------|-------------|--------------------|
| `azEnvironment` | VM が実行されている Azure 環境 | 2018-10-01
| `customData` | この機能は現在無効になっています。 使用できるようになり次第、このドキュメントを更新する予定です。 | 2019-02-01
| `evictionPolicy` | [スポット VM](../articles/virtual-machines/spot-vms.md) を削除する方法を設定します。 | 2020-12-01
| `isHostCompatibilityLayerVm` | ホストの互換性レイヤーで VM が実行されているかどうかを示します | 2020-06-01
| `licenseType` | [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit)のライセンスの種類。 これは AHB 対応 VM でのみ利用できます。 | 2020-09-01
| `location` | VM を実行中の Azure リージョン | 2017-04-02
| `name` | VM の名前 | 2017-04-02
| `offer` | VM イメージのオファーの情報。Azure イメージ ギャラリーからデプロイされるイメージについてのみ存在します。 | 2017-04-02
| `osProfile.adminUsername` | 管理者アカウントの名前を指定します。 | 2020-07-15
| `osProfile.computerName` | コンピューターの名前を指定します。 | 2020-07-15
| `osProfile.disablePasswordAuthentication` | パスワード認証が無効かどうかを指定します。 これは Linux VM でのみ利用できます。 | 2020-10-01
| `osType` | Linux または Windows | 2017-04-02
| `placementGroupId` | お使いの仮想マシン スケール セットの[配置グループ](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
| `plan` | VM が Azure Marketplace イメージである場合、[プラン](/rest/api/compute/virtualmachines/createorupdate#plan)にはその名前、製品、および発行元が含まれています | 2018-04-02
| `platformUpdateDomain` |  VM を実行中の[更新ドメイン](../articles/virtual-machines/availability.md) | 2017-04-02
| `platformFaultDomain` | VM を実行中の[障害ドメイン](../articles/virtual-machines/availability.md) | 2017-04-02
| `priority` | VM の優先度。 詳細については、「[スポット VM](../articles/virtual-machines/spot-vms.md)」を参照してください | 2020-12-01
| `provider` | VM のプロバイダー | 2018-10-01
| `publicKeys` | VM とパスに割り当てられた[公開キーのコレクション](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) | 2018-04-02
| `publisher` | VM イメージの発行元 | 2017-04-02
| `resourceGroupName` | お使いの仮想マシンの[リソース グループ](../articles/azure-resource-manager/management/overview.md) | 2017-08-01
| `resourceId` | リソースの[完全修飾](/rest/api/resources/resources/getbyid) ID | 2019-03-11
| `sku` | VM イメージの特定の SKU | 2017-04-02
| `securityProfile.secureBootEnabled` | VM で UEFI セキュア ブートが有効になっているかどうかを示します | 2020-06-01
| `securityProfile.virtualTpmEnabled` | VM で仮想トラステッド プラットフォーム モジュール (TPM) が有効になっているかどうかを示します | 2020-06-01
| `storageProfile` | 下記の「ストレージ プロファイル」を参照してください | 2019-06-01
| `subscriptionId` | 仮想マシンの Azure サブスクリプション | 2017-08-01
| `tags` | お使いの仮想マシンの[タグ](../articles/azure-resource-manager/management/tag-resources.md)  | 2017-08-01
| `tagsList` | プログラムによる解析を簡単にするために JSON 配列として書式設定されたタグ  | 2019-06-04
| `version` | VM イメージのバージョン | 2017-04-02
| `vmId` | VM の[一意の識別子](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
| `vmScaleSetName` | お使いの仮想マシン スケール セットの[仮想マシン スケール セット名](../articles/virtual-machine-scale-sets/overview.md) | 2017-12-01
| `vmSize` | [VM サイズ](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | 仮想マシンの[可用性ゾーン](../articles/availability-zones/az-overview.md) | 2017-12-01

**ストレージ プロファイル**

VM のストレージ プロファイルは、イメージ参照、OS ディスク、データ ディスクの 3 つのカテゴリに分類されます。

イメージ参照オブジェクトには、OS イメージに関する次の情報が含まれています。

| Data | 説明 |
|------|-------------|
| `id` | Resource ID
| `offer` | プラットフォームまたは marketplace イメージのオファー
| `publisher` | イメージ発行者
| `sku` | イメージ SKU
| `version` | プラットフォームまたは marketplace イメージのバージョン

OS ディスク オブジェクトには、VM によって使用される OS ディスクに関する次の情報が含まれています。

| Data | 説明 |
|------|-------------|
| `caching` | キャッシュの要件
| `createOption` | VM が作成された方法に関する情報
| `diffDiskSettings` | 一時ディスクの設定
| `diskSizeGB` | ディスクのサイズ (GB)
| `image`   | ソース ユーザー イメージの仮想ハード ディスク
| `lun`     | ディスクの論理ユニット番号
| `managedDisk` | マネージド ディスクのパラメーター
| `name`    | ディスク名
| `vhd`     | 仮想ハード ディスク
| `writeAcceleratorEnabled` | ディスクで writeAccelerator が有効になっているかどうか

データ ディスク アレイには、VM に接続されているデータ ディスクの一覧が含まれています。 各データ ディスク オブジェクトには、次の情報が含まれています。

Data | 説明 |
-----|-------------|
| `caching` | キャッシュの要件
| `createOption` | VM が作成された方法に関する情報
| `diffDiskSettings` | 一時ディスクの設定
| `diskSizeGB` | ディスクのサイズ (GB)
| `encryptionSettings` | ディスクの暗号化の設定
| `image` | ソース ユーザー イメージの仮想ハード ディスク
| `managedDisk` | マネージド ディスクのパラメーター
| `name` | ディスク名
| `osType` | ディスクに含まれている OS の種類
| `vhd` | 仮想ハード ディスク
| `writeAcceleratorEnabled` | ディスクで writeAccelerator が有効になっているかどうか

**Network**

| Data | 説明 | 導入されたバージョン |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | VM のローカル IPv4 アドレス | 2017-04-02
| `ipv4.publicIpAddress` | VM のパブリック IPv4 アドレス | 2017-04-02
| `subnet.address` | VM のサブネット アドレス | 2017-04-02
| `subnet.prefix` | サブネットのプレフィックス (24 など) | 2017-04-02
| `ipv6.ipAddress` | VM のローカル IPv6 アドレス | 2017-04-02
| `macAddress` | VM の mac アドレス | 2017-04-02


#### <a name="sample-1-tracking-vm-running-on-azure"></a>サンプル 1:Azure で動作している VM の追跡

サービス プロバイダーは、ソフトウェアを実行している VM の数を追跡する必要があったり、VM の一意性を追跡する必要のあるエージェントを使用していたりする場合があります。 VM の一意の ID を取得できるようにするには、Instance Metadata Service から `vmId` フィールドを使用します。

**Request**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**Response**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>サンプル 2:異なるデータ レプリカの配置

特定のシナリオでは、異なるレプリカの配置が非常に重要です。 たとえば、[Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) を介した [HDFS レプリカの配置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)またはコンテナーの配置では、VM が実行されている `platformFaultDomain` と `platformUpdateDomain` を把握しておく必要がある場合があります。
これらの決定を行うために、インスタンスの[可用性ゾーン](../articles/availability-zones/az-overview.md)を使用することもできます。
IMDS を介して、このデータに直接クエリを実行できます。

**Request**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Response**

```
0
```

#### <a name="sample-3-get-vm-tags"></a>サンプル 3: VM タグを取得する

VM タグは、instance/compute/tags エンドポイントの下にあるインスタンス API に含まれています。
論理的に分類するために Azure VM にタグが適用されている場合があります。 次の要求を使用して、VM に割り当てられたタグを取得できます。

**Request**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Response**

```
Department:IT;ReferenceNumber:123456;TestStatus:Pending
```

`tags` フィールドは、セミコロンで区切られたタグを含む文字列です。 この出力は、タグ自体でセミコロンが使用されている場合に問題になることがあります。 プログラムでタグを抽出するようにパーサーが作成されている場合は、`tagsList` フィールドを使用する必要があります。 `tagsList` フィールドは区切り記号のない JSON 配列であるため、解析が容易になります。 次の要求を使用して、VM に割り当てられた tagsList を取得できます。

**Request**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | jq
```

---

**Response**

#### <a name="windows"></a>[Windows](#tab/windows/)

```json
{
    "value":  [
                  {
                      "name":  "Department",
                      "value":  "IT"
                  },
                  {
                      "name":  "ReferenceNumber",
                      "value":  "123456"
                  },
                  {
                      "name":  "TestStatus",
                      "value":  "Pending"
                  }
              ],
    "Count":  3
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "ReferenceNumber",
    "value": "123456"
  },
  {
    "name": "TestStatus",
    "value": "Pending"
  }
]
```

---


#### <a name="sample-4-get-more-information-about-the-vm-during-support-case"></a>サンプル 4: サポート ケースの際の VM に関する詳細情報を取得する

サービス プロバイダーとして、VM の詳細情報を得る必要があるサポート コールを受けることがあります。 顧客にコンピューティング メタデータの共有を依頼すると、サポート担当者が Azure 上の VM の種類を確認するための基本情報を得られる場合があります。

**Request**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**Response**

> [!NOTE]
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

#### <a name="windows"></a>[Windows](#tab/windows/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "WindowsServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Windows",
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
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "2019-Datacenter",
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
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
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
            "offer": "WindowsServer",
            "publisher": "MicrosoftWindowsServer",
            "sku": "2019-Datacenter",
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
            "osType": "Windows",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "UbuntuServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Linux",
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
    "publisher": "Canonical",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "18.04-LTS",
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
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
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
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

---

#### <a name="sample-5-get-the-azure-environment-where-the-vm-is-running"></a>サンプル 5: VM が実行されている Azure 環境を取得する

Azure には、[Azure Government](https://azure.microsoft.com/overview/clouds/government/) など多数のソブリン クラウドがあります。 ランタイムの決定を行うために、Azure 環境が必要な場合があります。 次の例では、この動作を実現する方法を示します。

**Request**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**Response**

```
AzurePublicCloud
```

クラウドと Azure 環境の値を次に示します。

| クラウド | Azure 環境 |
|-------|-------------------|
| [一般公開されている全世界のすべての Azure リージョン](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure Germany](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-6-retrieve-network-information"></a>サンプル 6: ネットワーク情報を取得する

**Request**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json  -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

**Response**

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

#### <a name="sample-7-retrieve-public-ip-address"></a>サンプル 7: パブリック IP アドレスを取得する

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>構成証明済みデータ

### <a name="get-attested-data"></a>構成証明済みデータの取得

IMDS は、提供されるデータが Azure から送信されることを保証するために役立ちます。 Microsoft によってこの情報の一部に署名が行われるため、Azure Marketplace のイメージが Azure で実行されているものであることを確認できます。

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>パラメーター

| 名前 | 必須/省略可能 | 説明 |
|------|-------------------|-------------|
| `api-version` | 必須 | 要求を処理するために使用されるバージョン。
| `nonce` | Optional | 暗号化 nonce として機能する 10 桁の文字列。 値が指定されていない場合、IMDS では現在の UTC タイムスタンプが使用されます。

#### <a name="response"></a>Response

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> IMDS のキャッシュ メカニズムにより、以前にキャッシュされた nonce 値が返される場合もあります。

signature BLOB は、ドキュメントの [pkcs7](https://aka.ms/pkcs7) で署名されたバージョンです。 特定の VM 固有の詳細と共に署名に使用される証明書が含まれています。

Azure Resource Manager を使用して作成された VM の場合、ドキュメントには、ドキュメントの作成と有効期限のための `vmId`、`sku`、`nonce`、`subscriptionId`、`timeStamp` とイメージに関するプラン情報が含まれています。 プラン情報は Azure Marketplace のイメージにのみ設定されます。

クラシック デプロイ モデルを使用して作成された VM の場合、設定されることが保証されるのは `vmId` のみです。 応答から証明書を抽出し、応答が有効で Azure から送られたものであることを確認するために使用できます。

デコードされたドキュメントには、次のフィールドが含まれています。

| Data | 説明 | 導入されたバージョン |
|------|-------------|--------------------|
| `licenseType` | [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit)のライセンスの種類。 これは AHB 対応 VM でのみ利用できます。 | 2020-09-01
| `nonce` | 必要に応じて、要求で指定できる文字列。 `nonce` が指定されていない場合は、現在の協定世界時のタイムスタンプが使用されます。 | 2018-10-01
| `plan` | [Azure Marketplace イメージ プラン](/rest/api/compute/virtualmachines/createorupdate#plan)。 プラン ID (名前)、製品イメージまたはオファー (製品)、およびパブリッシャー ID (パブリッシャー) が含まれます。 | 2018-10-01
| `timestamp.createdOn` | 署名されたドキュメントが作成されたときの UTC タイムスタンプ | 2018-20-01
| `timestamp.expiresOn` | 署名されたドキュメントの有効期限が切れるときの UTC タイムスタンプ | 2018-10-01
| `vmId` | VM の[一意の識別子](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2018-10-01
| `subscriptionId` | 仮想マシンの Azure サブスクリプション | 2019-04-30
| `sku` | VM イメージの特定の SKU | 2019-11-01

> [!NOTE]
> 従来の (Azure Resource Manager 以外の) VM の場合は、vmId の設定のみが保証されます。

ドキュメントのサンプル:
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>サンプル 1:VM が Azure で実行されていることの検証

Azure Marketplace のベンダーは、自分たちのソフトウェアが Azure でのみ実行されるようにライセンスされていることを確認することを望みます。 だれかが VHD をオンプレミスの環境にコピーする場合、ベンダーはそれを検出できる必要があります。 これらのベンダーは、IMDS を介して、応答が Azure のみからであることを保証する署名付きデータを取得できます。

> [!NOTE]
> このサンプルを実行するには、jq ユーティリティがインストールされている必要があります。

**検証**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

署名が Microsoft Azure のものであることを確認し、証明書チェーンにエラーがないか確認します。

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
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

**結果**

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
  "licenseType": "Windows_Client",  
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

---

> [!NOTE]
> IMDS のキャッシュ メカニズムにより、以前にキャッシュされた `nonce` 値が返される場合があります。

最初の要求で `nonce` パラメーターを指定した場合、署名されたドキュメントの `nonce` を比較できます。

> [!NOTE]
> パブリック クラウド用の証明書と各ソブリン クラウド用の証明書は異なります。

| クラウド | Certificate |
|-------|-------------|
| [一般公開されている全世界のすべての Azure リージョン](https://azure.microsoft.com/regions/) | *.metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *.metadata.azure.us
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *.metadata.azure.cn
| [Azure Germany](https://azure.microsoft.com/overview/clouds/germany/) | *.metadata.microsoftazure.de

> [!NOTE]
> 証明書では、パブリック クラウドに対して `metadata.azure.com` が完全に一致していない可能性があります。 そのため、証明書の検証では、任意の `.metadata.azure.com` サブドメインからの共通名を許可する必要があります。

検証時にネットワークの制約が原因で中間証明書をダウンロードできない場合は、中間証明書をピン留めできます。 Azure によって証明書がロールオーバーされます。これは PKI の標準的な動作です。 ロールオーバーが行われたら、ピン留めされた証明書を更新する必要があります。 中間証明書を更新する変更を計画すると、そのたびに、Azure ブログが更新され、Azure の顧客に通知されます。 

中間証明書は、「[PKI リポジトリ](https://www.microsoft.com/pki/mscorp/cps/default.htm)」で見つけることができます。 中間証明書は、リージョンごとに異なる場合があります。

> [!NOTE]
> Azure China 21Vianet 用の中間証明書は、Baltimore ではなく DigiCert Global Root CA からのものです。
ルート チェーン機関の変更の一環として Azure China 用の中間証明書をピン留めした場合は、中間証明書を更新する必要があります。


## <a name="managed-identity"></a>マネージド ID

システムによって割り当てられたマネージド ID は、VM 上で有効にすることができます。 1 つ以上のユーザー割り当てのマネージド ID を VM に割り当てることもできます。
その後、IMDS からマネージド ID のトークンを要求できます。 これらのトークンは、Azure Key Vault などの他の Azure サービスで認証するために使用します。

この機能を有効にするための詳細な手順については、[アクセストークンの取得](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)に関するページを参照してください。

## <a name="load-balancer-metadata"></a>Load Balancer メタデータ
仮想マシンまたは仮想マシン セットのインスタンスを Azure Standard Load Balancer の背後に配置する場合は、IMDS を使用して、ロード バランサーとインスタンスに関連するメタデータを取得できます。 詳細については、[ロード バランサー情報の取得](../articles/load-balancer/instance-metadata-service-load-balancer.md)に関する記事を参照してください。

## <a name="scheduled-events"></a>スケジュールされたイベント
IMDS を使用すると、スケジュールされたイベントの状態を取得できます。 その後、ユーザーは、これらのイベントに対して実行する一連のアクションを指定できます。 詳細については、[Linux のスケジュールされたイベント](../articles/virtual-machines/linux/scheduled-events.md)または [Windows のスケジュールされたイベント](../articles/virtual-machines/windows/scheduled-events.md)に関する記事を参照してください。


## <a name="sample-code-in-different-languages"></a>さまざまな言語のサンプル コード

次の表に、VM 内で各種の言語を使用して IMDS を呼び出す例を示します。

| Language | 例 |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Go | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| NodeJS | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>エラーとデバッグ

見つからないデータ要素または無効な形式の要求がある場合、Instance Metadata Service は標準 HTTP エラーを返します。 次に例を示します。

| HTTP 状態コード | 理由 |
|------------------|--------|
| `200 OK` | 要求は成功しました。
| `400 Bad Request` | リーフ ノードのクエリ時に `Metadata: true` ヘッダーがないか、パラメーター `format=json` がありません
| `404 Not Found` | 要求された要素は存在しません
| `405 Method Not Allowed` | このエンドポイントでは、HTTP メソッド (動詞) はサポートされていません。
| `410 Gone` | しばらくしてから再試行してください (最長 70 秒)
| `429 Too Many Requests` | API の[転送率の制限](#rate-limiting)を超えました。
| `500 Service Error` | しばらくしてからやり直してください

## <a name="frequently-asked-questions"></a>よく寄せられる質問

- `400 Bad Request, Required metadata header not specified` エラーが発生します。 これはどういう意味でしょうか。
  - IMDS では、要求に `Metadata: true` ヘッダーを渡す必要があります。 このヘッダーを REST 呼び出しに渡すと、IMDS へのアクセスが許可されます。

- 使用している VM に関するコンピューティング情報を取得できないのはなぜですか。
  - 現在、IMDS では、Azure Resource Manager で作成されたインスタンスのみがサポートされます。

- しばらく前に Azure Resource Manager で VM を作成しました。 コンピューティング メタデータ情報が表示されないのはなぜですか。
  - 2016 年 9 月以降に VM を作成した場合は、[タグ](../articles/azure-resource-manager/management/tag-resources.md)を追加して、コンピューティング メタデータの表示を開始してください。 2016 年 9 月より前に VM を作成した場合は、VM インスタンスに対して拡張機能またはデータ ディスクを追加/削除して、メタデータを更新してください。

- 新しいバージョンに入力されたすべてのデータが表示されないのはなぜですか。
  - 2016 年 9 月以降に VM を作成した場合は、[タグ](../articles/azure-resource-manager/management/tag-resources.md)を追加して、コンピューティング メタデータの表示を開始してください。 2016 年 9 月より前に VM を作成した場合は、VM インスタンスに対して拡張機能またはデータ ディスクを追加/削除して、メタデータを更新してください。

- エラー `500 Internal Server Error` または `410 Resource Gone` が発生するのはなぜですか。
  - 要求を再試行してください。 詳細については、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」を参照してください。 問題が解決しない場合は、VM の Azure portal でサポートの問題を作成してください。

- これは仮想マシン スケール セット インスタンスで機能しますか。
  - はい。IMDS は仮想マシン スケール セット インスタンスで使用できます。

- 仮想マシン スケール セットでタグを更新しましたが、(単一インスタンスの VM とは異なり) インスタンスにタグが表示されません。 操作方法に何か間違いがありますか。
  - 現時点では、仮想マシン スケール セットのタグは、再起動、再イメージ化、またはインスタンスに対するディスクの変更の際に VM に対してのみ表示されます。

- VM の SKU 情報が `instance/compute` 詳細に表示されないのはなぜですか。
  - Azure Marketplace から作成されたカスタム イメージの場合、Azure プラットフォームではカスタム イメージの SKU 情報と、カスタム イメージから作成された VM の詳細は保持されません。 これは仕様であるため、VM `instance/compute` 詳細には表示されません。

- サービスの呼び出しの要求がタイムアウトするのはなぜですか。
  - メタデータの呼び出しは、VM のプライマリ ネットワーク カードに割り当てられたプライマリ IP アドレスから行う必要があります。 さらに、ルートを変更した場合、VM のローカル ルーティング テーブルにアドレスが 169.254.169.254/32 のルートが存在する必要があります。

    ### <a name="windows"></a>[Windows](#tab/windows/)

    1. ローカル ルーティング テーブルをダンプし、IMDS エントリを探します。 次に例を示します。
        ```console
        > route print
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
            168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
        169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
        ... (continues) ...
        ```
    1. `169.254.169.254` のルートが存在することを確認し、対応するネットワーク インターフェイス (`172.16.69.7` など) をメモします。
    1. インターフェイス構成をダンプし、ルーティング テーブルで参照されているものに対応するインターフェイスを見つけ、MAC (物理) アドレスをメモします。
        ```console
        > ipconfig /all
        ... (continues) ...
        Ethernet adapter Ethernet:

        Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
        Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
        Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
        DHCP Enabled. . . . . . . . . . . : Yes
        Autoconfiguration Enabled . . . . : Yes
        Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
        IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
        Subnet Mask . . . . . . . . . . . : 255.255.255.0
        ... (continues) ...
        ```
    1. インターフェイスが VM のプライマリ NIC とプライマリ IP に対応していることを確認します。 Azure portal でネットワーク構成を確認するか、Azure CLI を使用して調べることで、プライマリ NIC および IP を見つけることができます。 プライベート IP (および CLI を使用している場合は MAC アドレス) をメモします。 PowerShell CLI の例を次に示します。
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: wintest767 True 00-0D-3A-E5-1C-C0
        ```
    1. 一致しない場合は、プライマリ NIC および IP がターゲットになるようにルーティング テーブルを更新します。

    ### <a name="linux"></a>[Linux](#tab/linux/)

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
    1. インターフェイスが VM のプライマリ NIC とプライマリ IP に対応していることを確認します。 Azure portal でネットワーク構成を確認するか、Azure CLI を使用して調べることで、プライマリ NIC および IP を見つけることができます。 プライベート IP (および CLI を使用している場合は MAC アドレス) をメモします。 PowerShell CLI の例を次に示します。
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

    ---

- Windows Server でのフェールオーバー クラスタリング
  - フェールオーバー クラスタリングを使用して IMDS に対してクエリを実行する場合は、ルーティング テーブルにルートを追加する必要がある場合があります。 その方法は次のとおりです。

    1. 管理者特権でコマンド プロンプトを開きます。

    1. 次のコマンドを実行し、IPv4 ルーティング テーブルのネットワーク宛先 (`0.0.0.0`) のインターフェイスのアドレスをメモします。

    ```bat
    route print
    ```

    > [!NOTE]
    > 次の出力例は、フェールオーバー クラスターが有効になっている Windows Server VM からのものです。 わかりやすくするために、出力には IPv4 ルート テーブルだけが含まれています。

    ```
    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
            10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
            10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
    127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
        169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
        169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
    169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
            224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
    255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
    ```

    次のコマンドを実行し、ネットワーク宛先 (`0.0.0.0`) のインターフェイスのアドレスを使用します。この例では `10.0.1.10` です。

    ```bat
    route add 169.254.169.254/32 10.0.1.10 metric 1 -p
    ```

## <a name="support"></a>サポート

複数回試行してもメタデータの応答を取得できない場合は、Azure portal でサポートの問題を作成できます。

## <a name="product-feedback"></a>製品フィードバック

製品のフィードバックとアイデアは、[Virtual Machines] > [Instance Metadata Service] にあるユーザー フィードバック チャネル ([こちら](https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627)) にお寄せください。

## <a name="next-steps"></a>次のステップ

- [VM のアクセス トークンの取得](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

- [Linux のスケジュールされたイベント](../articles/virtual-machines/linux/scheduled-events.md)

- [Windows のスケジュールされたイベント](../articles/virtual-machines/windows/scheduled-events.md)
