---
title: "Azure Monitor の概要 | Microsoft Docs"
description: "Azure Monitor は、アラート、webhook、自動スケール、およびオートメーションで使用するための統計を収集します。 この記事では、その他の Microsoft 監視オプションも示します。"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: ca55f1c295d99ff92abaef1a919d4c90d3c33b8a
ms.lasthandoff: 03/09/2017


---

# <a name="overview-of-azure-monitor"></a>Azure Monitor の概要
この記事では、Azure リソースの監視の概念の概要を説明します。 特定の種類に該当するリソースについては、詳しい情報のある場所も紹介しています。  Azure 以外の観点からアプリケーションを監視する場合の概要については、「[監視と診断のガイダンス](../best-practices-monitoring.md)」をご覧ください。

Azure Monitor のビデオ チュートリアルは、  
「[Get Started with Azure Monitor](https://channel9.msdn.com/Blogs/Azure-Monitoring/Get-Started-with-Azure-Monitor)」 (Azure Monitor を使ってみる) をご覧ください。 Azure Monitor の使用シナリオについて説明する追加ビデオ「[Explore Microsoft Azure monitoring and diagnostics](https://channel9.msdn.com/events/Ignite/2016/BRK2234)」 (Microsoft Azure 監視と診断の詳細) もあります。  

クラウド アプリケーションは、動的なパーツを多数使った複雑な構成になっています。 監視では、アプリケーションを正常な状態で稼働させ続けるためのデータを取得できます。 また、潜在的な問題を防止したり、発生した問題をトラブルシューティングするのにも役立ちます。 さらに、監視データを使用して、アプリケーションに関する深い洞察を得ることもできます。 そのような知識は、アプリケーションのパフォーマンスや保守容易性を向上させたり、手作業での介入が必要な操作を自動化したりするうえで役立ちます。

次の図は、Azure での監視の概念、収集できるログの種類、データを使用して行うことができることを示しています。   

![非コンピューティング リソースに対する監視と診断のモデル](./media/monitoring-overview/Monitoring_Azure_Resources-compute_v4.png)

図 1: 非コンピューティング リソースに対する監視と診断の概念モデル

<br/>

![コンピューティング リソースに対する監視と診断のモデル](./media/monitoring-overview/Monitoring_Azure_Resources-non-compute_v4.png)

図 2: コンピューティング リソースに対する監視と診断の概念モデル

## <a name="monitoring-sources"></a>監視のソース
### <a name="activity-logs"></a>アクティビティ ログ
リソースの情報については、Azure インフラストラクチャで確認できるようなアクティビティ ログ (旧称、操作ログまたは監査ログ) を検索できます。 このログには、リソースが作成された時点や破棄された時点などの情報が記載されています。  

### <a name="host-vm"></a>ホスト VM
**コンピューティングのみ**

Cloud Services、Virtual Machines、Service Fabric など、一部のコンピューティング リソースには、やり取りの相手となる専用のホスト VM が存在します。 ホスト VM は、Hyper-V ハイパーバイザー モデルのルート VM と同じものです。 この場合、ゲスト OS だけでなくホスト VM についてもメトリックを収集できます。  

他の Azure サービスでは、リソースと特定のホスト VM の間に必ずしも 1 対 1 のマッピングが存在するわけではありません。このため、ホスト VM のメトリックは収集できません。

### <a name="resource---metrics-and-diagnostics-logs"></a>リソース - メトリックと診断ログ
収集可能なメトリックは、リソースのタイプによって異なります。 たとえば、Virtual Machines は、ディスク IO や CPU 使用率に関する統計情報を提供します。 しかし、Service Bus キューにはこのような統計情報が存在しません。キューでは、代わりにキューのサイズやメッセージのスループットのようなメトリックが提供されます。

コンピューティング リソースでは、ゲスト OS のほか、Azure 診断のような診断モジュールのメトリックが得られます。 Azure 診断は、診断データを収集し、Azure Storage をはじめとする他の場所にルーティングする際に役立ちます。

現在収集可能なメトリックの一覧については、[サポートされるメトリック](monitoring-supported-metrics.md)のページを参照してください。

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>アプリケーション - 診断ログ、アプリケーション ログ、メトリック
**コンピューティングのみ**

コンピューティング リソース用のモデルに示したとおり、アプリケーションはゲスト OS で実行できます。 各アプリケーションからは、それぞれに固有のログとメトリックのセットが出力されます。

メトリックのタイプには、以下のものがあります。

* パフォーマンス カウンター
* Application Logs
* Windows イベント ログ
* .NET イベント ソース
* IIS ログ
* マニフェスト ベースの ETW
* クラッシュ ダンプ
* カスタム エラー ログ

## <a name="uses-for-monitoring-data"></a>監視データの用途
### <a name="visualize"></a>視覚化
グラフィックスやチャートを使って監視データを視覚化すると、データ自体を見るよりも格段に早く傾向を把握できます。  

視覚化の方法には、次のようなものがあります。

* Azure ポータルの使用
* Azure Application Insights にデータをルーティングする
* Microsoft PowerBI にデータをルーティングする
* ライブ ストリーミングを使ってサード パーティ製の視覚化ツールにデータをルーティングするか、ツールに Azure Storage のアーカイブからデータを読み込ませる

### <a name="archive"></a>アーカイブ
監視データは通常、Azure ストレージに書き込まれ、削除するまでの間保持されます。

このデータを使用する方法はいくつかあります。

* データが書き込まれたら、それを Azure の内部または外部の他のツールに読み込ませ、処理する。
* データをローカルにダウンロードしてアーカイブするか、クラウドのアイテム保持ポリシーを変更して、データを長期間保持する。  
* <a name="you-leave-the-data-in-azure-storage-indefinitely-though-you-have-to-pay-for-azure-storage-based-on-the-amount-of-data-you-keep"></a>期限を定めることなく Azure ストレージにデータを置いておく (この場合、データの量に応じて Azure ストレージの料金が発生します)。
  -

### <a name="query"></a>クエリ
Azure Monitor REST API、クロス プラットフォーム コマンド ライン インターフェイス (CLI) コマンド、PowerShell コマンドレット、または .NET SDK を使えば、システム ストレージまたは Azure ストレージ内のデータにアクセスできます。

たとえば、次のようになります。

* 独自に作成したカスタム監視アプリケーションのためのデータを取得する
* カスタム クエリを作成し、そのデータをサードパーティ製アプリケーションに送信する

### <a name="route"></a>ルート
監視データは他の場所にリアルタイムでストリーミングできます。

たとえば、次のようになります。

* データを Application Insights に送り、そこで視覚化ツールを使用する
* データを Event Hubs に送り、リアルタイム分析を実行するサードパーティ製ツールにルーティングする

### <a name="automate"></a>自動化
アラートはもとより、プロセス全体をトリガーする場合にも、監視データを利用できます。
たとえば、次のようになります。

* データを使用し、アプリケーションの負荷に応じてコンピューティング インスタンスを自動でスケールアップまたはスケールダウンする
* メトリックが事前に定義されているしきい値に達したときにメールを送信する
* Web URL (webhook) を呼び出して Azure 外部のシステムでアクションを実行する
* Azure Automation で Runbook を開始し、さまざまなタスクを実行する

## <a name="methods-of-use"></a>使用方法
一般に、次のいずれかの方法で、データの追跡、ルーティング、および取得の操作を行うことができます。 ただし、アクションやデータの種類によっては利用できない方法もあります。

* [Azure ポータル](https://portal.azure.com)
* [PowerShell](insights-powershell-samples.md)  
* [クロスプラットフォーム コマンド ライン インターフェイス (CLI)](insights-cli-samples.md)
* [REST API](https://msdn.microsoft.com/library/dn931943.aspx)
* [.NET SDK](https://msdn.microsoft.com/library/dn802153.aspx)

## <a name="azures-monitoring-offerings"></a>Azure の監視ソリューション
Azure では、ベアメタル インフラストラクチャからアプリケーション テレメトリに至るまで、サービスを監視するためのソリューションをいくつか用意しています。 最善の監視戦略は、サービスの正常性に関して包括的かつ詳細な情報を得るために、以下の 3 つをすべて使用することです。

* [Azure Monitor](http://aka.ms/azmondocs) – Azure インフラストラクチャ (アクティビティ ログ) と個々の Azure リソース (診断ログ) から得られたデータを基に視覚化、クエリ、ルーティング、アラート、自動スケール、自動化を行います。 この記事は、Azure Monitor ドキュメントの一部です。 Azure Monitor という名前は、9 月 25 日に開催された Ignite 2016 で発表されたものです。  以前の名前は、"Azure Insights" でした。  
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) – Azure の監視により得られたデータを活用して、サービスのアプリケーション レイヤーの問題を検出および診断するものです。 App Service Web Apps では、これが既定の診断プラットフォームとなっています。  データは他のサービスからルーティングできます。  
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) ([Operations Management Suite](https://www.microsoft.com/oms/) の一部) – Azure リソースだけでなく、オンプレミスのインフラストラクチャやサードパーティ製のクラウド インフラストラクチャ (AWS など) にも使える総合的 IT 管理ソリューションです。  Log Analytics には Azure Monitor のデータを直接ルーティングできるため、環境全体のメトリックとログを 1 か所で確認できます。     

## <a name="next-steps"></a>次のステップ
項目ごとに詳しい情報を確認できます。

* [Azure Monitor の紹介ビデオ (Ignite 2016 より)](https://myignite.microsoft.com/videos/4977)
* [Azure Monitor の概要](monitoring-get-started.md)
* [Azure 診断](../azure-diagnostics.md) Cloud Services、Virtual Machines、または Service Fabric のアプリケーションに発生した問題を診断する場合には、こちらをご覧ください。
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) App Service Web アプリの問題を診断する場合には、こちらをご覧ください。
* [Azure Storage のトラブルシューティング](../storage/storage-e2e-troubleshooting.md) Storage Blobs、テーブル、またはキューを使用している場合には、こちらをご覧ください。
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) と [Operations Management Suite](https://www.microsoft.com/oms/)

