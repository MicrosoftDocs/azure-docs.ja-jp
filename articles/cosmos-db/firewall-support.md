---
title: "Azure Cosmos DB のファイアウォール サポートと IP アクセス制御 | Microsoft Docs"
description: "Azure Cosmos DB データベース アカウントで IP アクセス制御ポリシーを使用したファイアウォールを実現する方法について説明します。"
keywords: "IP アクセス制御, ファイアウォール サポート"
services: cosmos-db
author: shahankur11
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: ankshah
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: e08c0ba9c1fc0bab72ae8c1158aafaad4f66920e
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017


---
# <a name="azure-cosmos-db-firewall-support"></a>Azure Cosmos DB のファイアウォール サポート
Azure Cosmos DB データベース アカウントに格納されているデータを保護するために、Azure Cosmos DB は、強力なハッシュベースのメッセージ認証コード (HMAC) を利用したシークレット ベースの[承認モデル](https://msdn.microsoft.com/library/azure/dn783368.aspx)を備えています。 シークレット ベースの承認モデルに加え、Azure Cosmos DB は現在、ポリシーに基づく IP ベースのアクセス制御を使った受信ファイアウォールにも対応しています。 このモデルは、従来型データベース システムのファイアウォール規則によく似ていますが、Azure Cosmos DB データベース アカウントのセキュリティ水準をさらに引き上げることができます。 このモデルによって今後は、承認されているコンピューターのグループやクラウド サービスからのみアクセスできるように Azure Cosmos DB データベース アカウントを構成することができます。 ただし承認されているコンピューターのグループやサービスから Azure Cosmos DB リソースにアクセスするためには、呼び出し側が有効な承認トークンを提示する必要がある点は変わりません。

## <a name="ip-access-control-overview"></a>IP アクセス制御の概要
既定では、有効な承認トークンと共に要求が送信されれば、Azure Cosmos DB データベース アカウントにパブリック インターネットからアクセスすることができます。 IP ポリシー ベースのアクセス制御を構成するためには、特定のデータベース アカウントのクライアント IP の許可リストとして追加する一連の IP アドレスまたは IP アドレス範囲を CIDR 形式でユーザーが指定する必要があります。 この構成を適用すると、その許可リストに該当しないコンピューターを発信元とする要求はすべて、サーバーによってブロックされます。  以下の図は、IP ベースのアクセス制御における接続処理フローを表したものです。

![IP ベースのアクセス制御における接続プロセスの図](./media/firewall-support/firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>クラウド サービスからの接続
Azure Cosmos DB を使用した中間層のサービス ロジックをホスティングする手段として、Azure ではクラウド サービスがよく利用されます。 クラウド サービスから Azure Cosmos DB データベース アカウントにアクセスできるようにするには、[IP アクセス制御ポリシーを構成する](#configure-ip-policy)ことで、ご使用の Azure Cosmos DB データベース アカウントに関連付けられている IP アドレスの許可リストにクラウド サービスのパブリック IP アドレスを追加する必要があります。  これにより、クラウド サービスのすべてのロール インスタンスからその Azure Cosmos DB データベース アカウントにアクセスできるようになります。 クラウド サービスの IP アドレスは Azure Portal で確認できます。次のスクリーンショットを参照してください。

![Azure Portal に表示されるクラウド サービスのパブリック IP アドレスを示すスクリーンショット](./media/firewall-support/public-ip-addresses.png)

新たに (複数の) ロール インスタンスを追加してクラウド サービスをスケールアウトした場合、それらのインスタンスは、同じクラウド サービスに属すことになるので、Azure Cosmos DB データベース アカウントへのアクセス権が自動的に割り当てられます。

## <a name="connections-from-virtual-machines"></a>仮想マシンからの接続
Azure Cosmos DB を使用する中間層サービスのホスティングには、[仮想マシン](https://azure.microsoft.com/services/virtual-machines/)または[仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)を使用することもできます。  仮想マシンからのアクセスを許可するように Azure Cosmos DB データベース アカウントを構成するには、[IP アクセス制御ポリシーを構成する](#configure-ip-policy)ことで、仮想マシンや仮想マシン スケール セットのパブリック IP アドレスを、ご使用の Azure Cosmos DB データベース アカウントで許可された IP アドレスとして構成する必要があります。 仮想マシンの IP アドレスは Azure Portal で確認できます。次のスクリーンショットを参照してください。

![Azure Portal に表示される仮想マシンのパブリック IP アドレスを示すスクリーンショット](./media/firewall-support/public-ip-addresses-dns.png)

このグループに新たに仮想マシン インスタンスを追加した場合、それらのインスタンスにも自動的に Azure Cosmos DB データベース アカウントへのアクセス権が割り当てられます。

## <a name="connections-from-the-internet"></a>インターネットからの接続
インターネット上のコンピューターから Azure Cosmos DB データベース アカウントにアクセスするときは、そのコンピューターのクライアント IP アドレスまたは IP アドレス範囲を Azure Cosmos DB データベース アカウントの IP アドレスの許可リストに追加する必要があります。 

## <a id="configure-ip-policy"></a> IP アクセス制御ポリシーの構成
IP アクセス制御ポリシーは、Azure Portal で設定するか、[Azure CLI](cli-samples.md)、[Azure Powershell](powershell-samples.md)、または [REST API](/rest/api/documentdb/) を使用して `ipRangeFilter` プロパティを更新することでプログラムによって設定できます。 IP アドレス/範囲は、コンマで区切る必要があり、スペースを含めることはできません。 (例: "13.91.6.132,13.91.6.1/24")。 これらの方法でデータベース アカウントを更新するときは、既定の設定にリセットされないように、必ずすべてのプロパティを設定してください。

> [!NOTE]
> Azure Cosmos DB データベース アカウントの IP アクセス制御ポリシーを有効にすると、構成されている許可リストの IP アドレス範囲に該当しないコンピューターからのアクセスがすべてブロックされます。 このモデルにより、ポータルからのデータ プレーン操作の閲覧もブロックされ、アクセス制御の整合性が確保されます。

開発をシンプルにするために、Azure Portal では、クライアント コンピューターの IP アドレスを識別して許可リストに追加することができます。これにより、コンピューターで実行中のアプリが Azure Cosmos DB アカウントにアクセスすることができます。 ここでのクライアント IP アドレスは、ポータルから見た IP アドレスとして検出されることに注意してください。 コンピューターのクライアント IP アドレスである可能性がありますが、ネットワーク ゲートウェイの IP アドレスである可能性もあります。 運用環境にそれを移行する前に削除することを忘れないでください。

Azure Portal で IP アクセス制御ポリシーを設定するには、Azure Cosmos DB アカウント ブレードに移動し、ナビゲーション メニューの **[ファイアウォール]** をクリックした後、**[ON] \(オン)** をクリックします。 

![Azure Portal で [ファイアウォール] ブレードを開く方法を示しているスクリーンショット](./media/firewall-support/azure-portal-firewall.png)

新しいウィンドウで、Azure Portal がアカウントにアクセスできるかどうかを指定し、必要に応じてその他のアドレスと範囲を追加した後、**[保存]** をクリックします。  

> [!NOTE]
> IP アクセス制御ポリシーを有効にする場合は、アクセスを維持するために Azure Portal の IP アドレスを追加する必要があります。 ポータルの IP アドレスは次のとおりです。
> |リージョン|IP アドレス|
> |------|----------|
> |以下を除くすべてのリージョン| 104.42.195.92|
> |ドイツ|51.4.229.218|
> |中国|139.217.8.252|
> |米国政府アリゾナ|52.244.48.71|
>

![Azure Portal でファイアウォール設定を構成する方法を示しているスクリーンショット](./media/firewall-support/azure-portal-firewall-configure.png)

## <a name="troubleshooting-the-ip-access-control-policy"></a>IP アクセス制御ポリシーのトラブルシューティング
### <a name="portal-operations"></a>ポータルの操作
Azure Cosmos DB データベース アカウントの IP アクセス制御ポリシーを有効にすると、構成されている許可リストの IP アドレス範囲に該当しないコンピューターからのアクセスがすべてブロックされます。 したがって、ポータルのデータ ウィンドウでコレクションの参照やドキュメントのクエリなどの操作を有効にする場合は、ポータルの **[ファイアウォール]** ブレードを使用して、Azure Portal へのアクセスを明示的に許可する必要があります。 

![Azure Portal へのアクセスを有効にする方法を示しているスクリーンショット](./media/firewall-support/azure-portal-access-firewall.png)

### <a name="sdk--rest-api"></a>SDK と Rest API
許可リストに追加されていないコンピューターから SDK または REST API 経由でアクセスした場合、セキュリティ上の理由から、詳しい情報を含まない汎用的な 404 Not Found 応答が返されます。 ご使用の Azure Cosmos DB データベース アカウントに正しいポリシー構成を適用するために、そのアカウントに対して構成されている IP 許可リストを確認してください。

## <a name="next-steps"></a>次のステップ
ネットワークに関連したパフォーマンス上のヒントについては、[パフォーマンスに関するヒント](performance-tips.md)のページを参照してください。


