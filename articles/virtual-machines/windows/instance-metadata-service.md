---
title: "Windows 用の Azure Instance Metadata Service | Microsoft Docs"
description: "Windows VM のコンピューティング、ネットワーク、および今後のメンテナンス イベントに関する情報を取得するための RESTful インターフェイスです。"
services: virtual-machines-windows
documentationcenter: 
author: harijay
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/11/2017
ms.author: harijay
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 55b97b89cb297dc08dc73f6714c5159d4565a97c
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---

# <a name="azure-instance-metadata-service-for-windows-vms"></a>Windows VM 用の Azure Instance Metadata Service


Azure Instance Metadata Service は、実行中の仮想マシン インスタンスに関する情報を提供します。これらの情報を使用して仮想マシンの管理と構成を行うことができます。
SKU、ネットワークの構成、今後のメンテナンス イベントなどの情報が含まれます。 使用できる情報の種類の詳細については、「[メタデータ のカテゴリ](#instance-metadata-data-categories)」を参照してください。

Azure Instance Metadata Service は、[Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) を使用して作成されたすべての IaaS VM にアクセスできる REST エンドポイントです。 このエンドポイントは、VM からのみアクセスすることができる、よく知られているルーティング不可能な IP アドレス (`169.254.169.254`) で使用できます。



> [!IMPORTANT]
> このサービスは、全世界の Azure リージョンで**一般公開**されています。 米国政府、中国、およびドイツ向けの Azure クラウドでは、パブリック プレビュー版が公開されています。 仮想マシン インスタンスに関する新しい情報を公開する更新が定期的に行われています。 このページは、使用できる最新の[データ カテゴリ](#instance-metadata-data-categories)を反映しています。



## <a name="service-availability"></a>サービスの可用性
このサービスは、一般公開されている全世界の Azure リージョンで使用できます。 米国政府、中国、またはドイツの各リージョンでは、パブリック プレビュー段階です。

リージョン                                        | 可用性
-----------------------------------------------|-----------------------------------------------
[一般公開されている全世界のすべての Azure リージョン](https://azure.microsoft.com/regions/)     | 一般公開 
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | プレビュー段階 
[Azure China](https://www.azure.cn/)                                                           | プレビュー段階
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | プレビュー段階

この表は、他の Azure クラウドでサービスが利用可能になった時点で更新されます。

Instance Metadata Service を試すには、上記のリージョンで [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) または [Azure Portal](http://portal.azure.com) から VM を作成し、この後の例に従います。

## <a name="usage"></a>使用法

### <a name="versioning"></a>バージョン管理
インスタンス メタデータ サービスはバージョン管理されています。 バージョンは必須で、現在のバージョンは `2017-04-02` です。

> [!NOTE] 
> スケジュールされたイベントの前のプレビュー リリースでは、api-version として {latest} がサポートされていました。 この形式はサポートされなくなり、今後廃止される予定です。

新しいバージョンが追加されても、特定のデータ形式への依存関係がスクリプトにある場合、互換性を確保するために古いバージョンにもアクセスできます。 ただし、サービスが一般公開されると、現在のプレビュー バージョン (2017-03-01) は使用できなくなる可能性があることに注意してください。

### <a name="using-headers"></a>ヘッダーの使用
Instance Metadata Service のクエリを実行するときは、要求が意図せずリダイレクトされないように、`Metadata: true` ヘッダーを指定する必要があります。

### <a name="retrieving-metadata"></a>メタデータの取得

インスタンス メタデータは、[Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) を使用して作成/管理されている実行中の VM で使用できます。 仮想マシン インスタンスのすべてのデータ カテゴリにアクセスするには、次の要求を使用します。

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02"
```

> [!NOTE] 
> すべてのインスタンス メタデータ クエリでは、大文字小文字が区別されます。

### <a name="data-output"></a>データ出力
既定では、Instance Metadata Service は JSON 形式でデータを返します (`Content-Type: application/json`)。 ただし、要求すれば、別の API で別の形式でデータを返すことができます。
次の表は、API がサポートできるその他のデータ形式を示しています。

API | 既定のデータ形式 | その他の形式
--------|---------------------|--------------
/instance | json | テキスト
/scheduledevents | json | なし

既定以外の応答形式にアクセスするには、要求のクエリ文字列パラメーターとして要求の形式を指定します。 次に例を示します。

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02&format=text"
```

### <a name="security"></a>セキュリティ
Instance Metadata Service エンドポイントには、実行中の仮想マシン インスタンスからのみ、ルーティング不可能な IP アドレスでアクセスできます。 さらに、`X-Forwarded-For` ヘッダーがあるすべての要求は、サービスによって拒否されます。
また、実際の要求が意図しないリダイレクトの一部として行われたのではなく、直接意図されたものであったことを明確に示すために、要求に `Metadata: true`ヘッダーを含める必要があります。 

### <a name="error"></a>エラー
見つからないデータ要素または無効な形式の要求がある場合、Instance Metadata Service は標準 HTTP エラーを返します。 For example:

HTTP 状態コード | 理由
----------------|-------
200 OK |
400 Bad Request | `Metadata: true` ヘッダーがありません
404 Not Found | 要求された要素は存在しません 
405 Method Not Allowed | `GET` 要求と `POST` 要求のみがサポートされています
429 Too Many Requests | 現在 API は、1 秒あたり最大 5 つのクエリをサポートしています
500 Service Error     | しばらくしてからやり直してください

### <a name="examples"></a>例

> [!NOTE] 
> すべての API の応答は、JSON 文字列です。 次のすべての例の応答は、読みやすくするために整えられています。

#### <a name="retrieving-network-information"></a>ネットワーク情報の取得

**要求**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-04-02"
```

**応答**

> [!NOTE] 
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```
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

#### <a name="retrieving-public-ip-address"></a>パブリック IP アドレスの取得

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-04-02&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>インスタンスのすべてのメタデータの取得

**要求**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02"
```

**応答**

> [!NOTE] 
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```
{
  "compute": {
    "location": "westcentralus",
    "name": "IMDSSample",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "Canonical",
    "sku": "16.04.0-LTS",
    "version": "16.04.201610200",
    "vmId": "5d33a910-a7a0-4443-9f01-6a807801b29b",
    "vmSize": "Standard_A1"
  },
  "network": {
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
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Windows 仮想マシンでのメタデータの取得

**要求**

インスタンス メタデータは、Windows で Powershell ユーティリティ `curl` を使用して取得できます。 

```
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-04-02 | select -ExpandProperty Content
```

または、`Invoke-RestMethod` コマンドレットを使用して取得できます。
    
```
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-04-02 -Method get 
```

**応答**

> [!NOTE] 
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```
{
  "compute": {
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "MicrosoftSQLServer",
    "sku": "Enterprise",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": [
            
          ]
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>インスタンス メタデータのデータ カテゴリ
Instance Metadata Service を通して次のデータ カテゴリを使用できます。

データ | 説明
-----|------------
location | VM を実行中の Azure リージョン
name | VM の名前 
offer | VM イメージのプラン情報。 この値は、Azure イメージ ギャラリーからデプロイされるイメージにのみ存在します。
publisher | VM イメージの発行元
sku | VM イメージの特定の SKU  
version | VM イメージのバージョン 
osType | Linux または Windows 
platformUpdateDomain |  VM を実行中の[更新ドメイン](manage-availability.md)
platformFaultDomain | VM を実行中の[障害ドメイン](manage-availability.md)
vmId | VM の[一意の識別子](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
vmSize | [VM サイズ](sizes.md)
ipv4/privateIpAddress | VM のローカル IPv4 アドレス 
ipv4/publicIpAddress | VM のパブリック IPv4 アドレス
subnet/address | VM のサブネット アドレス
subnet/prefix | サブネットのプレフィックス (24 など)
ipv6/ipAddress | VM のローカル IPv6 アドレス
macAddress | VM の mac アドレス 
scheduledevents | 現在パブリック プレビュー段階です。[スケジュールされたイベント](scheduled-events.md)に関するページを参照してください。

## <a name="example-scenarios-for-usage"></a>使用に関するシナリオ例  

### <a name="tracking-vm-running-on-azure"></a>Azure で動作している VM の追跡

サービス プロバイダーは、ソフトウェアを実行している VM の数を追跡する必要があったり、VM の一意性を追跡する必要のあるエージェントを使用していたりする場合があります。 VM の一意の ID を取得できるようにするには、Instance Metadata Service から `vmId` フィールドを使用します。

**要求**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-04-02&format=text"
```

**応答**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>障害/更新ドメインに基づくコンテナー、データ パーティションの配置 

特定のシナリオでは、異なるレプリカの配置が非常に重要です。 たとえば、[Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) を介した[ HDFS レプリカの配置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)またはコンテナーの配置では、VM が実行されている `platformFaultDomain` と `platformUpdateDomain` を把握しておかなければならない場合があります。
このデータは、Instance Metadata Service を使用して直接照会できます。

**要求**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-04-02&format=text" 
```

**応答**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>サポート ケースの際の VM に関する詳細情報の取得 

サービス プロバイダーとして、VM の詳細情報を得る必要があるサポート コールを受けることがあります。 顧客にコンピューティング メタデータの共有を依頼すると、サポート担当者が Azure 上の VM の種類を確認するための基本情報を得られる場合があります。 

**要求**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-04-02"
```

**応答**

> [!NOTE] 
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
}
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>VM 内でさまざまな言語を使用してメタデータ サービスを呼び出す例 

言語 | 例 
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go Lan   | https://github.com/Microsoft/azureimds/blob/master/imdssample.go            
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Powershell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
    

## <a name="faq"></a>FAQ
1. `400 Bad Request, Required metadata header not specified` エラーが発生します。 これはどういう意味でしょうか。
   * Instance Metadata Service では、要求に `Metadata: true` ヘッダーを渡す必要があります。 このヘッダーを REST 呼び出しに渡すと、Instance Metadata Service へのアクセスが許可されます。 
2. 使用している VM に関するコンピューティング情報を取得できないのはなぜですか。
   * 現時点では、Instance Metadata Service は、Azure Resource Manager で作成されたインスタンスのみをサポートします。 今後、クラウド サービス VM に対するサポートが追加される可能性があります。
3. 少し前に、Azure Resource Manager を介して仮想マシンを作成しました。 コンピューティング メタデータ情報が表示されないのはなぜですか。
   * 2016 年 9 月以降に作成されたすべての VM については、[タグ](../../azure-resource-manager/resource-group-using-tags.md)を追加して、コンピューティング メタデータの表示を開始してください。 それよりも古い VM (2016 年 9 月よりも前に作成された VM) については、VM に対して拡張機能またはデータ ディスクを追加/削除してメタデータを更新してください。
4. `500 Internal Server Error` エラーが発生します。なぜですか。
   * 指数バックオフ システムに基づいて、要求を再試行してください。 問題が解決しない場合は、Azure サポートにお問い合わせください。
5. その他の質問/コメントはどこで共有すればいいですか。
   * http://feedback.azure.com でコメントをお送りください。
7. このサービスは仮想マシン スケール セット インスタンスで機能しますか。
   * はい。Metadata Service は、スケール セット インスタンスで利用できます。 
6. このサービスのサポートを受けるにはどうすればよいですか。
   * サービスのサポートを受けるには、時間がかかる再試行の後もメタデータの応答を取得できない VM を管理する Azure Portal でサポート問題を作成します。 

   ![Instance Metadata のサポート](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>次のステップ

- Instance Metadata Service によって提供される[スケジュールされたイベント](scheduled-events.md) API **(パブリック プレビュー段階)** を参照します。

