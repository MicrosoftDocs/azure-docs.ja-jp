---
title: "Log Analytics についてよく寄せられる質問 | Microsoft Docs"
description: "Azure Log Analytics サービスについてよく寄せられる質問とその回答"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 9ee8f4aafcc35e43c4fcba5a3a72b043dd9fc32c
ms.openlocfilehash: 4695669dc20b4b4b90ccdaf4db06df2cfcba2167
ms.lasthandoff: 02/21/2017


---
# <a name="log-analytics-faq"></a>Log Analytics についてよく寄せられる質問
この Microsoft FAQ は、Microsoft Operations Management Suite (OMS) の Log Analytics についてよく寄せられる質問の一覧です。 Log Analytics に関して他に不明な点がある場合は、 [ディスカッション フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) にアクセスして、質問を投稿してください。 コミュニティのメンバーから回答を得ることができます。 よく寄せられる質問については、すばやく簡単に見つけることができるように、この記事に追加していきます。

## <a name="general"></a>全般
**Q.AD および SQL Assessment ソリューションでは、どのようなチェックが行われますか?**

A.いいえ。 次のクエリは、現在実行されているすべてのチェックの説明を示します。

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

結果を Excel にエクスポートしてレビューすることができます。

**Q: SCOM Administration の *OMS* と異なる点があるのはなぜですか?**

A: 使用している SCOM の更新プログラム ロールアップによって、*System Center Advisor*、*Operational Insights*、*Log Analytics* のいずれかのノードが表示されます。

管理パックでは *OMS* のテキスト文字列が更新されており、手動でインポートする必要があります。 最新の SCOM 更新プログラム ロールアップの KB 記事に記載されている手順に従って操作し、 *OMS* コンソールを更新して OMS ノードの最新の更新プログラムを参照してください。

**Q: "*オンプレミス*" バージョンの OMS はありますか?**

A: いいえ。 Log Analytics では大量のデータが処理され、格納されます。 Log Analytics はクラウド サービスなので、必要に応じてスケールアップし、ユーザー環境のパフォーマンスに対する影響を防ぐことができます。

また、クラウド サービスなので、ユーザーは Log Analytics インフラストラクチャを維持し、稼働させる必要がなく、機能の更新と修正を頻繁に受けることができます。

## <a name="configuration"></a>構成
**Q.Azure 診断 (WAD) からの読み取りに使用されるテーブル/BLOB コンテナー名は変更できますか?**  

A.    いいえ。現在のところは変更できませんが、今後のリリースで予定されています。

**Q.OMS サービスにはどの IP アドレスが使用されていますか?ファイアウォールが OMS サービスへのトラフィックのみを許可していることはどうやって確認できますか?**  

A.いいえ。 Log Analytics サービスは Azure に基づいて構築され、エンドポイントは [Microsoft Azure データセンターの IP 範囲](http://www.microsoft.com/download/details.aspx?id=41653)に含まれる IP を受信します。

サービスがデプロイされるときに、実際の OMS サービスの IP アドレスは変わります。 ファイアウォールを通過できる DNS 名については、「 [Log Analytics のプロキシ設定とファイアウォール設定の構成](log-analytics-proxy-firewall.md)」を参照してください。

**Q.Azure への接続に ExpressRoute を使用しています。Log Analytics トラフィックは、この ExpressRoute 接続を使用するでしょうか?**  

A.いいえ。 各種の ExpressRoute トラフィックについては、[ExpressRoute のドキュメント](../expressroute/expressroute-faqs.md#supported-services)を参照してください。

Log Analytics へのトラフィックには、パブリックピアリング ExpressRoute 回路が使用されます。

**Q.既存の Log Analytics ワークスペースを別の Log Analytics ワークスペース/Azure サブスクリプションに移行する場合、シンプルで簡単な方法はありますか?**  当社の Azure サブスクリプションでテストとトライアルを行い、運用に移行しているお客様の OMS ワークスペースが複数あり、お客様の Azure/OMS サブスクリプションに移行したいと予定しています。  

A.いいえ。 `Move-AzureRmResource` コマンドレットは、Log Analytics ワークスペースを移動できるほか、Automation アカウントを Azure サブスクリプション間で移動することができます。 詳細については、「[Move-AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx)」を参照してください。

この変更は、Azure ポータルで行うこともできます。

ある Log Analytics ワークスペースから別のワークスペースにデータを移行したり、Log Analytics データが格納されているリージョンを変更したりすることはできません。

**Q: OMS を SCOM に追加するにはどうすればよいですか?**

A: 最新の更新プログラム ロールアップに更新し、管理パックをインポートすると、SCOM を Log Analytics に接続できます。

SCOM から Log Analytics への接続は、SCOM 2012 SP1 移行でのみ使用できます。

**Q: エージェントが Log Analytics と通信できることはどうやって確認できますか?**

A: エージェントが OMS と通信できることを確認するには、[コントロール パネル]、[システムとセキュリティ]、**[Microsoft Monitoring Agent]** の順に選択します。

**[Azure Log Analytics (OMS)]** タブの緑色のチェック マークを確認します。 緑色のチェック マーク アイコンは、エージェントが OMS サービスと通信できることを示します。

黄色の警告アイコンは、エージェントと OMS の通信に問題が発生していることを示します。 1 つの一般的な理由は、Microsoft Monitoring Agent サービスが停止し、再起動する必要があるためです。

**Q: エージェントから Log Analytics への通信を停止するにはどうすればよいですか?**

A: SCOM で、OMS 管理対象一覧からコンピューターを削除します。 削除すると、そのエージェントの SCOM 経由の通信はすべて停止されます。 OMS に直接接続されているエージェントの場合、[コントロール パネル]、[システムとセキュリティ]、**[Microsoft Monitoring Agent]** の順に選択して、OMS との通信を停止できます。
**[Azure Log Analytics (OMS)]**に表示されているすべてのワークスペースを削除します。

**Q: Azure サブスクリプション間でワークスペースを移動しようとするとエラーになるのはなぜですか?**

A: ソリューションを追加すると、ワークスペースがある Azure サブスクリプションにリソースが作成されます。

通常、サブスクリプションを追加するのは、*Azure サブスクリプション*の管理者か共同作成者です。 OMS ポータルで管理者または共同作成者であることに加え、Azure Portal でも Azure サブスクリプションの管理者または共同作成者である必要があります。


## <a name="agent-data"></a>エージェント データ
**Q.エージェント経由で Log Analytics に送信できるデータ量はどのくらいですか?顧客ごとのデータ量の上限はありますか?**  
A.いいえ。 無料プランには、1 つのワークスペースにつき 1 日 500 MB という上限があります。 Standard プランと Premium プランの場合、アップロードされるデータ量に上限はありません。 OMS の Log Analytics はクラウド サービスであり、お客様から送信されるデータ量が&1; 日に TB (テラバイト) 単位であったとしても、そのデータ量に合わせて自動的にスケールアップされるように設計されています。

Log Analytics エージェントは、小さなフットプリントで、一部の基本的なデータ圧縮を実行できるように設計されました。 エージェントに対して実行したテストとその感想についてブログに書かれているお客様もいらっしゃいます。 データ量は、お客様が有効にしているソリューションによって変わります。 データ量の詳細とソリューションごとの明細については、OMS の概要ページの **[使用状況]** タイルを参照してください。

OMS エージェントの低フットプリントの詳細については、 [お客様のブログ](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) を参照してください。

**Q.データを Log Analytics に送信するときに、Microsoft Management Agent (MMA) はどのくらいのネットワーク帯域幅を使用しますか?**

A.いいえ。 帯域幅は、送信されるデータ量に関する機能です。 データは、ネットワーク経由で送信されるときに圧縮されます。

**Q.エージェントごとにどのくらいのデータが送信されますか?**

A.いいえ。 次の条件によって大きく変わります。

* 有効にしているソリューション
* 収集しているログとパフォーマンス カウンターの数
* ログのデータ量

いくつかのサーバーをデプロイし、一般的なデータ量を計測する場合は、無料価格レベルがお勧めします。 全体的な使用状況は **[使用状況]** ページに表示されます。
WireData エージェントを実行できるコンピューターの場合、次のクエリを使用して、送信されているデータ量を確認できます。

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```



## <a name="next-steps"></a>次のステップ
* [Log Analytics の起動と開始](log-analytics-get-started.md) 」では、Log Analytics の詳細と、分単位で起動および実行する方法について説明します。

