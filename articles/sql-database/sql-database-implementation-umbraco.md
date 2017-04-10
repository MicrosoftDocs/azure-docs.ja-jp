---
title: "Azure SQL Database Azure 導入事例 - Umbraco | Microsoft Docs"
description: "Umbraco が SQL Database を使用して、クラウド上の数千のテナントに、サービスの迅速なプロビジョニングとスケールをどのように行っているかをご紹介します"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5243d31e-3241-4cb0-9470-ad488ff28572
ms.service: sql-database
ms.custom: customer implementations
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 187954f3ddafdbc17e341ce41f5b109cb95f8a24
ms.openlocfilehash: 774d5ac6f3d5d9d97120ab895157677e4a92bb05
ms.lasthandoff: 01/12/2017


---
# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Umbraco、Azure SQL Database を使用して、クラウド上の数千のテナント向けに迅速にサービスをプロビジョニングおよびスケール
![Umbraco ロゴ](./media/sql-database-implementation-umbraco/umbracologo.png)

Umbraco は、キャンペーンやパンフレットの小規模サイトから、Fortune 500 企業やグローバル メディアのWeb サイト向けの複雑なアプリケーションに至るまで、あらゆるものを機能させるオープン ソース コンテンツ管理システム (CMS) です。 

> 「Umbraco を利用する開発者の大規模なコミュニティがあり、私たちのフォーラムには 10 万人を超える開発者が集います。また Umbraco を実行するライブ サイト数は35 万以上に上ります。」
> 
> — Umbraco テクニカル リード、Morten Christensen 氏
> 
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-Umbraco/player]
> 
> 

顧客の環境を簡素化するため、Umbraco は、サービスとしてのソフトウェア (SaaS) の一種である、「サービスとしての Umbraco (UaaS)」 を追加しました。UaaS はオンプレミス環境の必要性をなくし、組み込み型拡張機能を提供して、ソリューション管理の手間を省き、開発者が製品のイノベーションに注力することで、管理費用を削減します。 Microsoft Azure の柔軟性に優れたサービスとしてのプラットフォーム (PaaS) モデルに依存することで、Umbraco はこれらのメリットを提供することが可能になりました。

UaaS により、SaaS ユーザーは、これまで利用が不可能だった Umbraco の CMS 機能を利用できるようになりました。 これらの顧客には実稼働データベースを含む作業のための CMS 環境が提供されます。  要件に応じて、開発およびステージング環境用にデータベースを最大&2; 台追加できます。 顧客が新しい環境を要求すると、自動化されたプロセスにより、データベースが自動的に割り当てられます。 利用可能なデータベースの Azure エラスティック プールから Umbraco による事前プロビジョニングが済んでいるため、データベースの割り当ては数秒で完了します。 (図 1 参照)。

![Umbraco provisioning lifecycle](./media/sql-database-implementation-umbraco/figure1.png)

図 1. サービスとしての Umbraco (UaaS) のプロビジョニング ライフサイクル

## <a name="azure-elastic-pools-and-automation-simplify-deployments"></a>Azure エラスティック プールと自動化が環境を簡素化 
Azure SQL Database および Azure のその他のサービスにより、Umbraco を利用する顧客は環境のセルフ プロビジョニングを行うことができます。また Umbraco は直観的なワークフローの一環として、データベースの監視と管理を容易に行うことができます。

1. プロビジョニング
   
   Umbraco は、エラスティック プールから利用可能な事前プロビジョニング済みデータベース 200 台分の容量を保持しています。 新規顧客が UaaS にサインアップすると、利用可能なプールからデータベースを割り当てることにより、ほぼリアルタイムに新しい CMS 環境を提供します。
   
   可用性プールがしきい値に達すると、新しいエラスティック プールが作成され、顧客のニーズに応じて割り当てられる新しいデータベースが事前プロビジョニングされます。
   
   実装は、C# 管理ライブラリおよび Azure Service Bus キューの使用により、完全に自動化されています。
2. 利用
   
   顧客は各自のデータベースで、1～3 つの環境 (実稼働、ステージング、開発用) を使用します。 顧客のデータベースはエラスティック プール内にあるため、Umbraco は過剰にプロビジョニングすることなく、効率的に拡大/縮小することができます。
   
   ![Umbraco project overview](./media/sql-database-implementation-umbraco/figure2.png)
   
   ![Umbraco project detail](./media/sql-database-implementation-umbraco/figure3.png)
   
   図 2. サービスとしての Umbraco (UaaS) 顧客の Web サイト、プロジェクトの概要と詳細 
   
   Azure SQL Database では、データベースのトランザクションという実環境に必要な相対的な能力の表示に、データベース トランザクション ユニット (DTU) という単位が使用されます。 UaaS 顧客の場合、データベースは通常約 10 DTU で動作していますが、需要に応じて自由に拡大/縮小します。 つまり UaaS では、ピーク時でも常に必要なリソースが確保されます。 たとえば最近では、日曜日夜のスポーツ イベントを放映する UaaS ユーザーのデータベースが、試合中に最大 100 DTU に達したケースがありました。 Azure エラスティック プールを利用することで、Umbraco はパフォーマンスを低下させることなく、高い需要に応えます。
3. 監視
   
   Umbraco は、データベースでのアクティビティの監視に、Azure Portal 内のダッシュボード、およびカスタムの電子メール アラートを使用しています。
4. 障害復旧
   
   Azure では、アクティブ geo レプリケーションおよび geo リストアの2 つの障害復旧 (DR) オプションが提供されます。 [ビジネス継続性の目標](sql-database-business-continuity.md)によって、企業がどちらの DR オプションを選択すべきかは異なります。
   
   アクティブ geo レプリケーションは、ダウンタイムの発生時に最も速やかに対応します。 アクティブ geo レプリケーションを使用して、異なるリージョンのサーバーに最大&4; つの読み取り可能なセカンダリ データベースを作成でき、障害発生時には、任意のセカンダリ データベースにフェールオーバーを開始することができます。
   
   Umbraco では geo レプリケーションは必要ありませんが、障害発生時のダウンタイムを最小限に抑えるために Azure geo リストアが利用されています。 geo リストアは Azure の地理冗長ストレージでのデータベースのバックアップに依存します。 これにより、ユーザーは、プライマリ リージョンで障害が発生した場合に、バックアップ コピーから復元することができます。
5. プロビジョニングを解除する
   
   プロジェクトの環境を削除すると、Azure Service Bus キューのクリーンアップ中に、関連するすべてのデータベース (開発、ステージング、またはライブ) が削除されます。 この自動化されたプロセスでは、Umbraco のエラスティック データベース可用性プールに未使用のデータベースが復元され、最大の使用率を維持しつつ、将来のプロビジョニングに利用できるようにしています。

## <a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>エラスティック プールで UaaS のスケールが容易に
Azure エラスティック プールを活用することで、Umbraco はプロビジョニングの過不足なく、顧客のパフォーマンスを最適化できます。 現在 Umbraco は 3,000 台近くのデータベースを 19 のエラスティック プールにわたって保有しており、必要に応じて容易にスケールする機能により、325,000 人の既存顧客、およびクラウドでの CMS のデプロイを望む新規顧客に対応しています。

Umbraco テクニカル リードの Morten Christensen 氏は次のように述べています。「現在 UaaS では、1 日あたり約 30 人のペースで新規顧客が増え続けています。 お客様は、新規プロジェクトを即座にプロビジョニングし、「ワン クリック デプロイ」で開発環境からライブ サイトに直ちに更新を発行して、エラー検出時には速やかに変更できる利便性に満足しています。

2 つめ、または&3; つめの環境が不要になれば、顧客はこれらの環境を簡単に削除できます。  これにより、Umbraco のエラスティック データベース可用性プールの一部として、他の顧客が使用できるリソースが確保されます。

![Umbraco deployment architecture](./media/sql-database-implementation-umbraco/figure4.png)

図 3: Microsoft Azure 上の UaaS デプロイ アーキテクチャ

## <a name="the-path-from-datacenter-to-cloud"></a>データセンターからクラウドへの道のり
Umbraco 開発者は、当初 SaaS モデルへの移行を決めたとき、サービスを構築するにはコスト効果が高くスケーラブルな方法が必要だと考えました。

> 「エラスティック プールは必要に応じて容量を調整できるため、私たちの SaaS サービスに最適です。 容易なプロビジョニング、さらに当社のセットアップにより、使用率を最大に保つことができます。」
> 
> — Umbraco テクニカル リード、Morten Christensen 氏
> 
> 

「インフラストラクチャの管理ではなく、お客様の問題の解決に注力する必要がありました。 お客様が最大の価値を容易に得ることができるようにしたかったのです」と Umbraco 創設者 Niels Hartvig 氏は語っています。 「当初は自前でサーバーをホストすることを検討しましたが、キャパシティ プランニングが大変そうでした。」 偶然にも Umbraco には、データベース管理者が&1; 人もいません。このことからも、UaaS の使用によって顧客にもたらされる重要な価値は明らかです。

Umbraco の開発者にとって重要な目標の&1; つが、UaaS を利用する顧客に、容量の制約なしに、環境を迅速にプロビジョニングする方法を提供することでした。 しかし、Umbraco のデータセンターで専用のホステッド サービスを提供するには、プロセスのバースト処理のために余分な容量が必要になります。 これは、使用率が定期的に低下するコンピューティング インフラストラクチャの大幅な拡張を意味します。

さらに、Umbraco の開発チームには、既存のコードを最大限再利用できるソリューションが必要でした。 Umbraco の開発者 Mikkel Madsen 氏は次のように述べています。「私たちは、Microsoft SQL Server、Microsoft Azure SQL Database、ASP.NET、およびインターネット インフォメーション サービス (IIS) などの、なじみのある Microsoft の開発ツールに満足していました。 IaaS または PaaS のクラウド ソリューションに投資する前に、Microsoft のツールとプラットフォームをサポートして、コード ベースに大幅な変更を加えずに済むことを確認する必要がありました。」

これらすべての条件を満たすために、Umbraco は、以下の条件を満たすクラウド パートナーを探しました。

* 十分な容量と信頼性
* Microsoft の開発ツールをサポートして、Umbraco のエンジニアが開発環境を一から構築し直す必要がないこと
* UaaS が競合するすべての地理的市場で事業を展開していること (企業にとっては、データにすぐアクセスでき、地域の規制要件を満たす場所にデータが保存されている必要がある)

## <a name="why-umbraco-chose-azure-for-uaas"></a>Azure が Umbraco の UaaS に選ばれた理由
Morten Christensen 氏は次のように述べています。「すべての選択肢を検討した結果、私たちはAzure を選びました。管理のしやすさ、スケーラビリティ、使いやすさ、そしてコスト効率性に至るまで、Azure はすべての条件を満たしています。 私たちは Azure 仮想マシンで環境を設定します。各環境はそれぞれ Azure SQL Database インスタンスを持ち、すべてのインスタンスがエラスティック プール内にあります。 開発、ステージング、およびライブ環境間でデータベースを分離することで、規模に適した堅牢なパフォーマンスの隔離をお客様に提供できることは、大きなメリットです。」

Morten 氏は続けます。「以前は、Web データベースに手動でサーバーをプロビジョニングする必要がありました。 現在では、これを心配せずに済みます。 プロビジョニングからクリーンアップまで、すべてが自動化されています。」

Morten 氏は、Azure の拡張機能にも満足しています。 「エラスティック プールは必要に応じて容量を調整できるため、私たちの SaaS サービスに最適です。 容易なプロビジョニング、さらに当社のセットアップにより、使用率を最大に保つことができます。」 エラスティック プールの簡便性、およびサービス層ベースの DTU の確実性により、需要に応じて新しいリソース プールをプロビジョニングすることが可能になりました。  最近では、大手企業のお客様のデータベースが、ライブ環境で 100 DTU に達するケースがありました。 Azure を使用することで、当社のエラスティック プールはお客様のデータベースに必要なリソースをリアルタイムで提供し、DTU の需要を予測する必要はありません。 簡単に言えば、お客様は期待するスピードで対応を受け、当社はパフォーマンスに関するサービス レベル アグリーメントを満たすことができます。」

Mikkel Madsen 氏は次のように締めくくっています。「私たちは、Azure SQL Database との結合に Azure Service Bus キューを使用する基盤技術の上に、大規模でリアルタイムな新規顧客の受け入れという共通の SaaS シナリオを結ぶ強力な Azure のアルゴリズムを、当社のアプリケーション パターン (開発およびライブ環境両方のデータベースの事前プロビジョニング) に採用しています。」

## <a name="with-azure-uaas-is-exceeding-customer-expectations"></a>Azure で顧客の期待を超える UaaS
クラウド パートナーとして Azure を選択してからというもの、Umbraco は、自己ホスト型ソリューションで必要な IT リソースへの投資を省きつつ、UaaS ユーザーにコンテンツ管理の最適化されたパフォーマンスを提供しています。 Morten 氏は次のように述べています。「開発者にとって利便性が高く、スケーラビリティに優れた Azure に非常に満足しています。 機能と信頼性は当社のお客様に高く評価されています。 全体的に見て、Azure を選んだことは私たちにとって大きな成功です。」

## <a name="more-information"></a>詳細情報
* Azure エラスティック プールの詳細については、[エラスティック プール](sql-database-elastic-pool.md)に関する記事をご覧ください。
* Azure Service Bus の詳細については、「 [Azure Service Bus](https://azure.microsoft.com/services/service-bus/)」をご覧ください。
* Web ロールと worker ロールの詳細については、 [worker ロール](../fundamentals-introduction-to-azure.md#compute)に関する記事をご覧ください。    
* 仮想ネットワークの詳細については、 [仮想ネットワーク](https://azure.microsoft.com/documentation/services/virtual-network/)に関する記事を参照してください。    
* バックアップと復旧の詳細については、 [ビジネス継続性](sql-database-business-continuity.md)に関する記事を参照してください。    
* プールの監視の詳細については、 [プールの監視](sql-database-elastic-pool-manage-portal.md)に関する記事を参照してください。    
* サービスとしての Umbraco について、詳しくは、 [Umbraco](https://umbraco.com/cloud)を参照してください。


