---
title: セキュリティとアクセス制御に関するイシューのトラブルシューティング
description: Azure Data Factory でのセキュリティとアクセス制御に関するイシューのトラブルシューティングを行う方法について説明します。
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/05/2021
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 70e4d0c1a9f26f83924c1343e4e8e51c5595b324
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944516"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Azure Data Factory でのセキュリティとアクセス制御に関するイシューのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory でのセキュリティとアクセス制御に対する一般的なトラブルシューティングの方法について説明します。

## <a name="common-errors-and-messages"></a>一般的なエラーとメッセージ

### <a name="connectivity-issue-in-the-copy-activity-of-the-cloud-datastore"></a>クラウド データストアのコピー アクティビティにおける接続の問題

#### <a name="symptoms"></a>現象

ソースまたはシンクのデータストアで接続の問題が発生すると、さまざまなエラー メッセージが返されることがあります。

#### <a name="cause"></a>原因 

この問題は、通常、次のいずれかの要因が原因で発生します。

* セルフホステッド統合ランタイム (IR) を使用している場合は、セルフホステッド IR ノードのプロキシ設定。

* セルフホステッド IR を使用している場合は、セルフホステッド IR ノードのファイアウォール設定。

* クラウド データストアのファイアウォール設定。

#### <a name="resolution"></a>解決方法

* これが接続の問題であることを確認するには、次の点をチェックしてください。

   - エラーは、ソースまたはシンク コネクタからスローされている。
   - 障害は、コピー アクティビティの開始時に発生している。
   - 障害は、Azure IR、または 1 つのノードのセルフホステッド IR で一貫している。これは、一部のノードのみに問題がある場合、複数ノードのセルフホステッド IR でランダムに障害が発生している可能性があるからです。

* **セルフホステッド IR** を使用している場合は、プロキシ、ファイアウォール、およびネットワークの設定を確認してください。これは、Azure IR を使用している場合、同じデータストアへの接続が成功する可能性があるからです。 このシナリオのトラブルシューティングを行うには、次を参照してください。

   * [セルフホステッド IR のポートとファイアウォール](./create-self-hosted-integration-runtime.md#ports-and-firewalls)
   * [Azure Data Lake Storage コネクタ](./connector-azure-data-lake-store.md)
  
* **Azure IR** を使用している場合は、データストアのファイアウォール設定を無効にしてみてください。 この方法で、次の 2 つの状況における問題を解決できます。
  
   * [Azure IR の IP アドレス](./azure-integration-runtime-ip-addresses.md)が許可リストに含まれていない。
   * *[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]* 機能が [Azure Blob Storage](./connector-azure-blob-storage.md#supported-capabilities) および [Azure Data Lake Storage Gen 2](./connector-azure-data-lake-storage.md#supported-capabilities) に対してオフになっている。
   * *[Azure サービスへのアクセスを許可する]* 設定が Azure Data Lake Storage Gen1 に対してオンになっていない。

前述のいずれの方法も機能しない場合は、Microsoft にお問い合わせください。


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>パブリック ネットワーク アクセスを無効にした後に認証キーが無効または空である

#### <a name="symptoms"></a>現象

Data Factory のパブリック ネットワーク アクセスを無効にした後、セルフホステッド統合ランタイムによって、"認証キーが無効または空です。" というエラーがスローされます。

#### <a name="cause"></a>原因

パブリック接続を無効にしてプライベート エンドポイントを確立すると再接続ができないので、この問題は、ドメイン ネーム システム (DNS) の解決の問題が原因である可能性が最も高くなります。

Data Factory の完全修飾ドメイン名 (FQDN) がパブリック IP アドレスに解決されるかどうかを確認するには、次の手順を実行します。

1. Data Factory のプライベート エンドポイントと同じ仮想ネットワーク内に Azure 仮想マシン (VM) が作成されていることを確認します。

2. Azure VM から Data Factory FQDN に対して PsPing と Ping を実行します。

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`
   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   > [!Note]
   > PsPing コマンドのポートを指定する必要があります。 ここではポート 443 が示されていますが、必須ではありません。

3. 両方のコマンドが、指定されたリージョンに基づいた Azure Data Factory のパブリック IP に解決されるかどうかを確認します。 IP は、`xxx.xxx.xxx.0` の形式にする必要があります。

#### <a name="resolution"></a>解決方法

この問題を解決するには、以下の手順を実行します。
- 「[Azure Data Factory 用の Azure Private Link](./data-factory-private-link.md#dns-changes-for-private-endpoints)」という記事を参照してください。 この手順では、プライベート DNS ゾーンまたはサーバーを構成して、Data Factory の FQDN をプライベート IP アドレスに解決します。

- 長期的なソリューションとして、カスタム DNS を使用することをお勧めします。 ただし、プライベート DNS ゾーンまたはサーバーを構成したくない場合は、次の一時的ソリューションを試してください。

  1. Windows で host ファイルを変更し、プライベート IP (Azure Data Factory のプライベート エンドポイント) を Azure Data Factory の FQDN にマップします。
  
     Azure VM で `C:\Windows\System32\drivers\etc` にアクセスし、メモ帳で *host* ファイルを開きます。 プライベート IP を FQDN にマップする行をファイルの末尾に追加し、変更を保存します。
     
     ![プライベート IP からホストへのマップのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. 前の検証手順と同じコマンドを再実行して、応答を確認します。プライベート IP が含まれているはずです。

  1. セルフホステッド統合ランタイムを再登録すると、問題が解決されるはずです。

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>プライベート リンクが原因で、セルフホステッド VM 上で IR 認証キーを登録できない

#### <a name="symptoms"></a>現象

プライベート リンクが有効になっているため、セルフホステッド VM 上で IR の認証キーを登録できません。 次のエラー メッセージが表示されます。

"Failed to get service token from ADF service with key *************** and time cost is:0.1250079 seconds, the error code is:InvalidGatewayKey, activityId is:XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection." (キー *************** を使用して ADF サービスからサービス トークンを取得できませんでした。時間コスト:0.1250079 秒、エラー コード:InvalidGatewayKey、アクティビティ ID:XXXXXXX、詳細なエラー メッセージ: クライアント IP アドレスは有効なプライベート IP ではありません。原因: Data Factory のパブリック ネットワーク IP にアクセスできなかったため、クラウドにアクセスして正常に接続を確立できませんでした。)

#### <a name="cause"></a>原因

この問題は、セルフホステッド IR をインストールしようとしている VM が原因で発生する可能性があります。 クラウドに接続するには、パブリック ネットワーク アクセスが有効になっていることを確認します。

#### <a name="resolution"></a>解決方法

**解決策 1**
 
この問題を解決するには、以下の手順を実行します。

1. [ファクトリ - 更新](/rest/api/datafactory/Factories/Update)に関するページにアクセスします。

1. 右上にある **[試してみる]** ボタンを選択します。
1. **[パラメーター]** で、必要な情報を入力します。 
1. **[本文]** で、次のプロパティを貼り付けます。

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ```
1. **[実行]** を選択して、関数を実行します。 

1. **[パラメーター]** で、必要な情報を入力します。 

1. **[本文]** で、次のプロパティを貼り付けます。
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. **[実行]** を選択して、関数を実行します。 
1. **応答コード:200** が表示されていることを確認します。 貼り付けたプロパティも JSON 定義に表示されているはずです。

1. 統合ランタイムで、IR 認証キーをもう一度追加します。


**解決策 2**

この問題を解決するには、[Azure Data Factory 用の Azure Private Link](./data-factory-private-link.md) にアクセスします。

次のスクリーンショットに示すように、ユーザー インターフェイスでパブリック ネットワーク アクセスを有効にします。

![[ネットワーク] ペイン上の [パブリック ネットワーク アクセスを許可する] に対する [有効] コントロールのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>次のステップ

トラブルシューティングの詳細について、次のリソースを参照してください。

*  [Data Factory 用の Azure Private Link](data-factory-private-link.md)
*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
*  [Data Factory に関する Stack overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)