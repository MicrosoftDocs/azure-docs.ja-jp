---
title: セキュリティとアクセス制御に関するイシューのトラブルシューティング
description: Azure Data Factory でのセキュリティとアクセス制御に関するイシューのトラブルシューティングを行う方法について説明します。
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.author: lle
ms.openlocfilehash: fa410441203c50d96c0de1d9188fb73b6fd4d577
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706149"
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

- オプションとして、Data Factory の "プライベート リンク DNS ゾーン" の下に "仮想ネットワークのリンク" を手動で追加することをお勧めします。 詳細については、「[Azure Data Factory 用の Azure Private Link](./data-factory-private-link.md#dns-changes-for-private-endpoints)」という記事を参照してください。 この手順では、プライベート DNS ゾーンまたはカスタム DNS サーバーを構成して、Data Factory の FQDN をプライベート IP アドレスに解決します。 

- ただし、プライベート DNS ゾーンまたはカスタム DNS サーバーを構成したくない場合は、次の一時的なソリューションを試してください。

  1. Windows で host ファイルを変更し、プライベート IP (Azure Data Factory のプライベート エンドポイント) を Azure Data Factory の FQDN にマップします。
  
     Azure VM で `C:\Windows\System32\drivers\etc` にアクセスし、メモ帳で *host* ファイルを開きます。 プライベート IP を FQDN にマップする行をファイルの末尾に追加し、変更を保存します。
     
     ![プライベート IP からホストへのマップのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. 前の検証手順と同じコマンドを再実行して、応答を確認します。プライベート IP が含まれているはずです。

  1. セルフホステッド統合ランタイムを再登録すると、問題が解決されるはずです。

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>プライベート リンクが原因で、セルフホステッド VM 上で IR 認証キーを登録できない

#### <a name="symptoms"></a>現象

プライベート リンクが有効になっているため、セルフホステッド VM 上で IR の認証キーを登録できません。 次のエラー メッセージが表示されます。

"Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 second, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection." (キー *************** を使用して ADF サービスからサービス トークンを取得できませんでした。時間コスト: 0.1250079 秒、エラー コード: InvalidGatewayKey、アクティビティ ID: XXXXXXX、詳細なエラー メッセージ: クライアント IP アドレスは有効なプライベート IP ではありません。原因: Data Factory のパブリック ネットワーク IP にアクセスできなかったため、クラウドにアクセスして正常に接続を確立できませんでした。)

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

### <a name="adf-private-dns-zone-overrides-azure-resource-manager-dns-resolution-causing-not-found-error"></a>ADF プライベート DNS ゾーンで、"見つかりません" エラーが発生している Azure Resource Manager の DNS 解決が上書きされます

#### <a name="cause"></a>原因
Azure Resource Manager と ADF は両方とも同じプライベート ゾーンを使用しています。これにより、顧客のプライベート DNS で潜在的な競合が発生し、Azure Resource Manager レコードが見つからないことがあります。

#### <a name="solution"></a>解決策
1. Azure portal でプライベート DNS ゾーン **privatelink.azure.com** を検索します。
![プライベート DNS ゾーンの検索のスクリーンショット。](media/security-access-control-troubleshoot-guide/private-dns-zones.png)
2. A レコード **adf** があるかどうかを確認します。
![A レコードのスクリーンショット。](media/security-access-control-troubleshoot-guide/a-record.png)
3.  **仮想ネットワークのリンク** にアクセスし、すべてのレコードを削除します。
![仮想ネットワークのリンクのスクリーンショット。](media/security-access-control-troubleshoot-guide/virtual-network-link.png)
4.  Azure portal でお使いのデータ ファクトリに移動し、Azure Data Factory ポータル用のプライベート エンドポイントを再作成します。
![プライベート エンドポイントの再作成のスクリーンショット。](media/security-access-control-troubleshoot-guide/create-private-endpoint.png)
5.  プライベート DNS ゾーンに戻り、新しいプライベート DNS ゾーン **privatelink.adf.azure.com** があるかどうかを確認します。
![新しい DNS レコードのスクリーンショット。](media/security-access-control-troubleshoot-guide/check-dns-record.png)

### <a name="connection-error-in-public-endpoint"></a>パブリック エンドポイントでの接続エラー

#### <a name="symptoms"></a>現象

Azure Blob Storage アカウントのパブリック アクセスを使用してデータをコピーすると、パイプラインは次のエラーでランダムに失敗します。

例: Azure Blob Storage シンクで Azure IR (マネージド VNet ではなくパブリック) が使用され、Azure SQL Database ソースでマネージド VNet IR が使用されました。 または、ソース/シンクでは、ストレージ パブリック アクセスでのみマネージド VNet IR が使用されます。

`
<LogProperties><Text>Invoke callback url with req:
"ErrorCode=UserErrorFailedToCreateAzureBlobContainer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Unable to create Azure Blob container. Endpoint: XXXXXXX/, Container Name: test.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.WindowsAzure.Storage.StorageException,Message=Unable to connect to the remote server,Source=Microsoft.WindowsAzure.Storage,''Type=System.Net.WebException,Message=Unable to connect to the remote server,Source=System,''Type=System.Net.Sockets.SocketException,Message=A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond public ip:443,Source=System,'","Details":null}}</Text></LogProperties>.
`

#### <a name="cause"></a>原因

ADF では引き続きマネージド VNet IR を使用できますが、[マネージド仮想ネットワークとマネージド プライベート エンドポイント](https://docs.microsoft.com/azure/data-factory/managed-virtual-network-private-endpoint#outbound-communications-through-public-endpoint-from-adf-managed-virtual-network)に関する記事に説明されているように、マネージド VNet 内の Azure Blob Storage へのパブリック エンドポイントにはテスト結果に基づく信頼性がなく、ADF マネージド仮想ネットワークからパブリック エンドポイントを経由する Azure Blob Storage と Azure Data Lake Gen2 へ接続はサポートされていないため、このようなエラーが発生することがあります。

#### <a name="solution"></a>解決策

- マネージド VNet IR を使用する場合は、ソース側と同じようにシンク側でもプライベート エンドポイントを有効にします。
- パブリック エンドポイントを引き続き使用する場合は、ソースとシンクに対してマネージド VNet IR を使用するのではなく、パブリック IR のみに切り替えることができます。 パブリック IR に切り替えた場合でも、マネージド VNet IR がまだ存在する場合は、ADF でマネージド VNet IR が引き続き使用される可能性があります。

## <a name="next-steps"></a>次のステップ

トラブルシューティングの詳細について、次のリソースを参照してください。

*  [Data Factory 用の Azure Private Link](data-factory-private-link.md)
*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
*  [Data Factory に関する Stack overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)