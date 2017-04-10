---
title: "Azure DocumentDB のリージョン内フェールオーバー | Microsoft Docs"
description: "Azure DocumentDB での手動および自動フェールオーバーの動作について説明します。"
services: documentdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 446e2580-ff49-4485-8e53-ae34e08d997f
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 4af4d30a3378e1aea66309a1d757be1c1da2ea0d
ms.lasthandoff: 03/07/2017


---
# <a name="automatic-regional-failovers-for-business-continuity-in-documentdb"></a>DocumentDB でのビジネス継続性のためのリージョン内自動フェールオーバー
Azure DocumentDB はデータのグローバル分散を容易にします。そのために、対応する保証と共に一貫性、可用性、パフォーマンスの間の明確なトレードオフを提供する、完全に管理された[複数リージョンのデータベース アカウント](documentdb-distribute-data-globally.md)が用意されています。 DocumentDB アカウントには、高可用性、10 ミリ秒未満の遅延、[明確に定義された整合性レベル](documentdb-consistency-levels.md)、マルチホーム API による透過的なリージョン内フェールオーバー、世界規模でスループットとストレージを柔軟にスケールする機能が備わっています。 

Azure DocumentDB では、明示的なフェールオーバーおよびポリシーに基づくフェールオーバーの両方がサポートされており、障害発生の際にエンド ツー エンド システムの動作を制御することができます。 この記事では、次のことについて説明します。

* 手動フェールオーバーが DocumentDB で動作する仕組み
* 自動フェールオーバーが DocumentDB で動作する仕組み
* 手動フェールオーバーをアプリケーション アーキテクチャで使用する方法

リージョン内フェールオーバーについては、Scott Hanselman とプリンシパル エンジニアリング マネージャー Karthik Raman による次の Azure Friday ビデオもご覧ください。

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]  

## <a id="ConfigureMultiRegionApplications"></a>複数リージョンのアプリケーションの構成
フェールオーバー モードを詳しく説明する前に、複数リージョンの可用性を活用し、リージョン内フェールオーバーの際も耐障害性を持つようなアプリケーションを構成する方法について説明します。

* 最初に、複数のリージョンでアプリケーションをデプロイします。
* アプリケーションがデプロイされているすべてのリージョンからのアクセスを確実に低待機時間にするために、サポートされているいずれかの SDK によってリージョンごとに、対応する[優先リージョンの一覧](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations)を構成します。

次のスニペットは、複数リージョンのアプリケーションを初期化する方法を示しています。 ここでは、DocumentDB アカウント `contoso.documents.azure.com` は&2; つのリージョン、米国西部と北ヨーロッパで構成されています。 

* アプリケーションは米国西部リージョンにデプロイされている (たとえば Azure App Services を使用) 
* 低待機時間読み取りの最初の優先リージョンとして `West US` を指定している
* 2 番目の優先リージョン (リージョン内障害時の高可用性のため) として `North Europe` を指定している

.Net では、この構成は次のスニペットのようになります。

    ConnectionPolicy usConnectionPolicy = new ConnectionPolicy 
    { 
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
    };

    usConnectionPolicy.PreferredLocations.Add(LocationNames.WestUS);
    usConnectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

    DocumentClient usClient = new DocumentClient(
        new Uri("https://contosodb.documents.azure.com"),
        "memf7qfF89n6KL9vcb7rIQl6tfgZsRt5gY5dh3BIjesarJanYIcg2Edn9uPOUIVwgkAugOb2zUdCR2h0PTtMrA==",
        usConnectionPolicy);

アプリケーションは北ヨーロッパでもデプロイされていますが、逆の優先リージョン順位です。 つまり、北ヨーロッパが低待機時間読み取りの最初に指定されています。 そして米国西部が、リージョン内障害時の高可用性のため&2; 番目の優先リージョンとして指定されています。

下記のアーキテクチャの図は、複数リージョンのアプリケーションのデプロイを示しており、DocumentDB とアプリケーションが Azure の&4; つの地理的リージョンで利用できるように構成されています。  

![Azure DocumentDB を使用して世界各地に分散してデプロイされたアプリケーション](./media/documentdb-regional-failovers/app-deployment.png)

では、DocumentDB サービスによる自動フェールオーバーを使用したリージョン内障害の処理方法を見てみましょう。 

## <a id="AutomaticFailovers"></a>自動フェールオーバー
Azure のリージョン内障害はまれですが、発生した場合に DocumentDB は、影響を受けたリージョンに存在するすべての DocumentDB アカウントのフェールオーバーを自動的にトリガーします。 

**読み取りリージョンで障害が起きた場合**

影響を受けるリージョンのいずれかに読み取りリージョンを指定している DocumentDB アカウントは、自動的にそれらの書き込みリージョンから切断され、オフラインとしてマークされます。 DocumentDB SDK ではリージョン内探索プロトコルが実装されており、リージョンが使用可能になったことを自動的に検出し、優先リージョンの一覧に従って次の使用可能なリージョンに読み込み呼び出しをリダイレクトすることができます。 優先リージョンの一覧にあるいずれのリージョンも使用可能でない場合、呼び出しは自動的に現在の書き込みリージョンに戻ります。 リージョン内フェールオーバーを処理するアプリケーション コードは、変更する必要はありません。 この処理全体において、DocumentDB によって一貫性の保証が継続的に実現されています。

![Azure DocumentDB での読み取りリージョン障害](./media/documentdb-regional-failovers/read-region-failures.png)

影響を受けたリージョンが障害から回復したら、そのリージョン内で影響を受けたすべての DocumentDB アカウントは、サービスにより自動的に復旧されます。 そして、影響を受けたリージョンに読み取りリージョンがあった DocumentDB アカウントは、自動的に現在の書き込みリージョンと同期してオンラインになります。 DocumentDB SDK では、新しいリージョンが使用可能かを検出し、そのリージョンを現在の読み取りリージョンとして選択すべきかどうか、アプリケーションで構成された優先リージョンの一覧に基づいて判断します。 それ以降の読み取りは、アプリケーション コードを変更しなくても、回復したリージョンにリダイレクトされます。

**書き込みリージョンで障害が起きた場合**

もし影響を受けるリージョンが、指定の Azure DocumentDB アカウントの現在の書き込みリージョンである場合は、そのリージョンは自動的にオフラインとしてマークされます。 そして、影響を受ける DocumentDB アカウントの各々について、代替リージョンが書き込みリージョンとして昇格されます。 Azure Portal を使用して、または[プログラムを使用して](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange)、DocumentDB アカウントのリージョンの選択順序を完全に制御することができます。 

![Azure DocumentDB のフェールオーバーの優先順位](./media/documentdb-regional-failovers/failover-priorities.png)

自動フェールオーバー中、DocumentDB は指定された優先順位に基づいて、指定の Azure DocumentDB アカウント用の次の書き込みリージョンを自動的に選択します。 

![Azure DocumentDB での書き込みリージョン障害](./media/documentdb-regional-failovers/write-region-failures.png)

影響を受けたリージョンが障害から回復したら、そのリージョン内で影響を受けたすべての DocumentDB アカウントは、サービスにより自動的に復旧されます。 

* 影響を受けるリージョンに直前の書き込みリージョンがある DocumentDB アカウントは、リージョンの復旧後も読み取りは可能ですがオフライン モードのままとなります。 
* このリージョンをクエリし、現在の書き込みリージョン内にあるデータと比較することによって、障害中にレプリケートされなかった書き込みを計算することができます。 アプリケーションの必要性に応じて、マージや競合の解決を実行し、変更内容の最終版を現在の書き込みリージョンに書き戻すことができます。 
* 変更のマージを完了したら、影響を受けたリージョンを削除してから DocumentDB アカウントに再び追加して、そのリージョンをオンラインに戻すことができます。 リージョンが再び追加されたら、 Azure Portal で手動フェールオーバーを実行するか、または[プログラムを使用して](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_CreateOrUpdate)、そのリージョンを書き込みリージョンとして構成しなおすことができます。

## <a id="ManualFailovers"></a>手動フェールオーバー

自動フェールオーバーに加えて、指定の DocumentDB アカウントの現在の書込みリージョンは、手動で動的に、既存の書込みリージョンのいずれかに変更することができます。 手動フェールオーバーは、Azure Portal から、または[プログラムを使用して](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_CreateOrUpdate)開始することができます。 

手動フェールオーバーは**データ損失なし**および**可用性の損失なし**を保証するもので、指定した DocumentDB アカウントの書き込みリージョンを元のリージョンから新しいリージョンに適切に移行します。 自動フェールオーバーの場合と同様に、Azure DocumentDB SDK は手動フェールオーバー時に自動的に書き込みリージョンの変更処理を行い、呼び出しが自動的に新しい書き込みリージョンにリダイレクトされるようにします。 フェールオーバー管理のためにお使いのアプリケーションのコードや構成を変更する必要はありません。 

![Azure DocumentDB の手動フェールオーバー](./media/documentdb-regional-failovers/manual-failovers.png)

手動フェールオーバーが役立つ一般的なシナリオには、次のようなものがあります。

**時系列のパターンに合わせる**: お使いのアプリケーションにおいて、1 日の時刻に基づいたトラフィックのパターンが予測可能な場合、書き込み状態を定期的に&1; 日の時刻に基づいて最もアクティブな地域リージョンに変更することができます。

**サービスの更新**: グローバルに分散デプロイされているアプリケーションでは、計画的なサービスの更新の実行中に、トラフィック マネージャーを使用して別のリージョンにトラフィックを最ルーティングすることがあります。 そのようなアプリケーションのデプロイでは、手動フェールオーバーを使用して、サービスの更新中にトラフィックがアクティブになるリージョンに、書き込み状態を保持することができます。

**ビジネス継続性と障害復旧 (BCDR) のテスト**: ほとんどのエンタープライズ アプリケーションには、開発およびリリース プロセスの一部として、ビジネス継続性のテストが含まれています。 BCDR テストは、コンプライアンス証明と、リージョン内障害の際のサービス可用性の保証のために、しばしば重要な手順となります。 ストレージに DocumentDB を使用しているアプリケーションで BCDR に対応できているかテストするには、DocumentDB アカウントの手動フェールオーバーをトリガーするか、動的なリージョンの追加と削除を行います。

この記事では、Azure DocumentDB における手動および自動フェールオーバーの動作の仕組み、グローバルに使用する DocumentDB アカウントとアプリケーションの構成方法を確認しました。 Azure DocumentDB のグローバルなレプリケーション サポートを使用すると、エンド ツー エンドの待機時間を向上させ、リージョンで障害が発生しても高可用性を保証することができます。 

## <a id="NextSteps"></a>次のステップ
* DocumentDB の[グローバル配布](documentdb-distribute-data-globally.md)サポートについて確認する
* [DocumentDB とのグローバルな整合性](documentdb-consistency-levels.md)について確認する
* [Azure DocumentDB SDK](documentdb-developing-with-multiple-regions.md) を使用して複数リージョンで開発する
* Azure DocumentDB を使用して[複数リージョン ライター アーキテクチャ](documentdb-multi-region-writers.md)を作成する方法を確認する


