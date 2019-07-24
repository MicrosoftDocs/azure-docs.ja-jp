---
title: Azure Monitor for VMs (プレビュー) についてよく寄せられる質問 | Microsoft Docs
description: Azure Monitor for VMs は、Azure VM オペレーティング システムの正常性およびパフォーマンスの監視と、アプリケーション コンポーネントおよび他のリソースとの依存関係の自動検出を組み合わせ、それらの間の通信をマップする Azure のソリューションです。 この記事ではよくある質問の回答を示します。
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: magoedte
ms.openlocfilehash: 32f2833b4c1ba77564d5388bc080a7cb32d90201
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243775"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>Azure Monitor for VMs (プレビュー) についてよく寄せられる質問
この Microsoft FAQ では、Azure Monitor for VMs についてよく寄せられる質問を紹介します。 このソリューションについてほかに質問がある場合は、[ディスカッション フォーラム](https://feedback.azure.com/forums/34192--general-feedback)にアクセスして質問を投稿してください。 よく寄せられる質問については、すばやく簡単に見つけることができるように、この記事に追加していきます。

## <a name="can-i-onboard-to-an-existing-workspace"></a>既存のワークスペースにオンボードすることはできますか?
仮想マシンが Log Analytics ワークスペースに既に接続されている場合、ワークスペースが[こちら](vminsights-onboard.md#prerequisites)に記載されたサポートされているリージョンのいずれかにあれば、VM 用 Azure Monitor にオンボードするときにそのワークスペースを引き続き使用できます。

オンボード時に、ワークスペースのパフォーマンス カウンターが構成され、Azure Monitor for VMs で表示および分析するために、ワークスペースにデータを報告するすべての VM で この情報の収集が開始されます。  これにより、選択したワークスペースに接続されているすべての VM のパフォーマンス データを確認できます。  正常性機能とマップ機能は、オンボードの対象として指定した VM に対してのみ有効になります。

有効化されるパフォーマンス カウンターの詳細については、[オンボード](vminsights-onboard.md)に関する記事をご覧ください。

## <a name="can-i-onboard-to-a-new-workspace"></a>新しいワークスペースにオンボードすることはできますか? 
現在、VM が既存の Log Analytics ワークスペースに接続されていない場合は、データを保存するために新しいワークスペースを作成する必要があります。  Azure portal を使用して Azure Monitor for VMs で単一の Azure VM を構成すると、新しい既定のワークスペースが自動的に作成されます。

スクリプトベースの方法を使用する場合は、[オンボード](vminsights-onboard.md)に関する記事に手順が記載されています。 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>VM が既に既存のワークスペースに報告している場合はどうすればよいですか?
仮想マシンから既にデータを収集している場合、既存の Log Analytics ワークスペースにデータを報告するように仮想マシンを構成済みである可能性があります。  そのワークスペースがサポートされているリージョンのいずれかにあれば、その既存のワークスペースに対して Azure Monitor for VMs を有効にすることができます。  既に使用しているワークスペースがサポートされているリージョンにない場合、現時点では Azure Monitor for VMs にオンボードすることはできません。  Microsoft では、その他のリージョンのサポートに積極的に取り組んでいます。

>[!NOTE]
>Azure Monitor for VMs へのオンボードを選択したかどうかに関係なく、ワークスペースのパフォーマンス カウンターが構成されます。これらは、ワークスペースに報告するすべての VM に影響を及ぼします。 ワークスペースのパフォーマンス カウンターの構成の詳細については、こちらの[ドキュメント](../../azure-monitor/platform/data-sources-performance-counters.md)をご覧ください。 VM 用 Azure Monitor 用に構成されるカウンターについては、[オンボードに関するドキュメント](vminsights-onboard.md#performance-counters-enabled)をご覧ください。  

## <a name="why-did-my-vm-fail-to-onboard"></a>VM のオンボードに失敗したのはなぜですか?
Azure portal から Azure VM をオンボードすると、次の手順が実行されます。

* 既定の Log Analytics ワークスペースが作成されます (該当するオプションが選択されている場合)。
* 選択したワークスペースのパフォーマンス カウンターが構成されます。 この手順が失敗すると、一部のパフォーマンス グラフやテーブルに、オンボードした VM のデータが表示されません。 これを修正するには、[こちら](vminsights-onboard.md#enable-with-powershell)に記載された PowerShell スクリプトを実行します。
* Log Analytics エージェントが必要と判断されると、VM 拡張機能を使用して Azure VM にインストールされます。  
* Azure Monitor for VMs Map Dependency エージェントが必要と判断されると、拡張機能を使用して Azure VM にインストールされます。  
* 必要に応じて、正常性機能をサポートする Azure Monitor コンポーネントが構成され、正常性データを報告するように VM が構成されます。

オンボード プロセス中、上記の各手順で状態がチェックされ、ポータルで通知の状態が返されます。 ワークスペースとエージェントのインストールの構成には、通常 5 から 10 分かかります。 監視データと正常性データがポータルに表示されるまでに、さらに 5 から 10 分かかります。  

オンボードを開始したときに、VM をオンボードする必要があることを示すメッセージが表示された場合は、VM がプロセスを完了するまでに最大 30 分かかります。 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Azure Monitor for VMs のみを有効にしましたが、正常性機能によってすべての VM が監視されているのはなぜですか。
正常性機能は、Log Analytics ワークスペースに接続されているすべての VM に対して有効にされます。これは、アクションが単一の VM に対して開始される場合にも当てはまります。

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>正常性基準で条件を評価するスケジュールを変更できますか。
いいえ。このリリースでは、正常性基準となる期間と頻度を変更できません。 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>監視する必要がない条件に対して、正常性基準を無効にできますか。
正常性基準は、このリリースでは無効にできません。

## <a name="are-the-health-alert-severities-configurable"></a>正常性アラートの重大度は構成できますか。  
正常性アラートの重大度を変更することはできません。有効または無効にのみ変更できます。 また、一部のアラートの重大度は、正常性基準の状態に基づいて更新されます。 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>特定の正常性基準の設定を再構成した場合、それを特定のインスタンスだけに適用できますか。  
正常性基準インスタンスの設定を変更すると、Azure VM 上の同じ種類の正常性基準インスタンスすべてが変更されます。 たとえば、論理ディスク C: に対応する空きディスク領域の正常性基準インスタンスのしきい値を変更すると、このしきい値は、同じ VM において検出および監視されている他の論理ディスクすべてに適用されます。

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>正常性機能は論理プロセッサとコアを監視しますか。
いいえ。個々のプロセッサおよび論理プロセッサ レベルの正常性基準は、Windows に対しては含まれていません。既定では、Azure VM で使用可能な論理 CPU の合計数に基づいて CPU 負荷を効果的に評価するために、CPU 使用率の合計だけが監視されます。 

## <a name="are-all-health-criteria-thresholds-configurable"></a>正常性基準のすべてのしきい値を構成できますか。  
Windows VM を対象とする正常性基準のしきい値は変更できません。これらの正常性状態は、"*実行中*" または "*使用可能*" に設定されているためです。 [Workload Monitor API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components) から正常性状態に対してクエリを実行すると、以下に該当する場合、サービスまたはエンティティに対して、**LessThan** または **GreaterThan** の *comparisonOperator* 値と、"*しきい*" 値 **4** が表示されます。
   - DNS クライアント サービスの正常性 – サービスが実行されていない。 
   - DHCP クライアント サービスの正常性 – サービスが実行されていない。 
   - RPC サービスの正常性 – サービスが実行されていない。 
   - Windows ファイアウォール サービスの正常性 – サービスが実行されていない。
   - Windows イベント ログ サービスの正常性 – サービスが実行されていない。 
   - サーバー サービスの正常性 – サービスが実行されていない。 
   - Windows リモート管理サービスの正常性 – サービスが実行されていない。 
   - ファイル システム エラーまたは破損 – 論理ディスクが使用できない。

次の Linux の正常性基準のしきい値は変更できません。これらの正常性状態は、既に *true* に設定されているためです。 Workload Monitoring API からエンティティに対するクエリが実行されると、コンテキストに応じて、正常性状態には、値 **LessThan** を使用した *comparisonOperator* と "*しきい*" 値 **1** が表示されます。
   - 論理ディスクの状態 – 論理ディスクがオンライン/使用可能でない
   - ディスクの状態 – ディスクがオンライン/使用可能でない
   - ネットワーク アダプターの状態 - ネットワーク アダプターが無効

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>正常性機能に含まれているアラートを変更する方法はありますか。
各正常性基準に対して定義されているアラート ルールは、Azure portal には表示されません。 [Workload Monitor API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components) でのみ、正常性アラート ルールを有効または無効にすることができます。 また、正常性アラート用の [Azure Monitor アクション グループ](../../azure-monitor/platform/action-groups.md)を Azure portal 内で割り当てることはできません。 正常性アラートが発生するたびにトリガーされるアクション グループを構成するには、通知設定 API を使用する必要があります。 現在、VM に対して発生したすべての*正常性アラート*によって同じアクション グループがトリガーされるように、VM に対してアクション グループを割り当てることができます。 従来の Azure アラートとは異なり、正常性アラート ルールごとに個別のアクション グループが存在するという概念はありません。 さらに、正常性アラートがトリガーされたときに、電子メールまたは SMS 通知を提供するように構成されたアクション グループのみがサポートされます。 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>パフォーマンス グラフに VM のデータが表示されません
ディスク テーブルまたは一部のパフォーマンス グラフにパフォーマンス データが表示されない場合、ワークスペースでパフォーマンス カウンターが構成されていない可能性があります。 これを解決するには、こちらの [PowerShell スクリプト](vminsights-onboard.md#enable-with-powershell)を実行してください。

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Azure Monitor for VMs のマップ機能は Service Map とどのように異なるのですか?
Azure Monitor for VMs のマップ機能は Service Map に基づいていますが、次の点が異なります。

* マップ ビューには、VM のブレード、および [Azure Monitor] の [Azure Monitor for VMs]\(Azure Monitor for VMs\) からアクセスできます。
* マップ内の接続がクリック可能になっており、選択した接続のサイド パネルに接続メトリック データのビューが表示されます。
* より複雑なマップのサポートを強化するために、マップの作成に使用される新しい API が用意されています。
* 監視対象の VM がクライアント グループ ノードに含まれるようになりました。グループ内の監視対象の仮想マシンと監視対象外の仮想マシンの割合がドーナツ グラフに示されます。  また、グループを展開したときに、これを使用してマシンの一覧をフィルター処理することもできます。
* 監視対象の仮想マシンがサーバー ポート グループ ノードに含まれるようになりました。グループ内の監視対象のマシンと監視対象外のマシンの割合がドーナツ グラフに示されます。  また、グループを展開したときに、これを使用してマシンの一覧をフィルター処理することもできます。
* Application Insights のアプリ マップとの一貫性を向上させるために、マップのスタイルが更新されました。
* サイド パネルが更新されましたが、Service Map でサポートされていた Update Management、Change Tracking、Security、およびService Desk との統合はまだ含まれていません。 
* マップするグループとマシンを選択するオプションが更新され、サブスクリプション、リソース グループ、Azure 仮想マシン スケール セット、クラウド サービスがサポートされるようになりました。
* Azure Monitor for VMs のマップ機能では、新しい Service Map コンピューター グループを作成することはできません。  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>パフォーマンス グラフに点線が表示されるのはなぜですか?
これはいくつかの理由で発生する可能性があります。  表示するデータ収集にギャップがある場合、点線が表示されます。  有効になっているパフォーマンス カウンターのデータ サンプリング頻度を変更したときに (既定の設定では、60 秒ごとにデータが収集されます)、グラフの時間範囲を狭め、サンプリング頻度がグラフで使用されるバケット サイズよりも少ない場合 (たとえば、サンプリング頻度が 10分 ごとで、グラフの各バケットが 5 分の場合)、グラフに点線が表示されます。  この場合、表示する時間範囲を広げると、グラフの線が点線ではなく実線で表示されます。

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Azure Monitor for VMs でグループはサポートされていますか?
はい、Dependency エージェントをインストールすると、VM から情報が収集され、サブスクリプション、リソース グループ、仮想マシン スケール セット、およびクラウド サービスに基づいてグループが表示されます。  Service Map を使用し、マシン グループを作成している場合も、これらが表示されます。  表示中のワークスペースでコンピューター グループを作成している場合は、それらもグループ フィルターに表示されます。 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>集計パフォーマンス グラフで 95 百分位線を引き上げているものについて詳細を確認するにはどうすればよいですか?
既定では、選択されたメトリックで 95 パーセンタイルの最大値を持つ VM を示すためにリストが並べ替えられます。ただし、5 パーセンタイルの最小値を持つマシンが表示される、使用可能なメモリのグラフを除きます。  グラフをクリックすると、適切なメトリックが選択された **[Top N List]\(上位 N のリスト\)** ビューが開きます。

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>マップ機能では、VNet 間およびサブネット間で重複する IP はどのように処理されますか?
サブネット間および VNet 間で VM または Azure 仮想マシン スケール セットの IP 範囲が重複している場合、Azure Monitor for VMs に間違った情報が表示されることがあります。 これは既知の問題であり、このエクスペリエンスを改善するためのオプションを検討中です。

## <a name="does-map-feature-support-ipv6"></a>マップ機能で IPv6 はサポートされていますか?
現在、マップ機能では IPv4 のみがサポートされています。IPv6 のサポートは検討中です。 IPv6 内でトンネリングされる IPv4 もサポートされています。

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>リソース グループや他の大規模なグループのマップを読み込んだときに、マップを表示しづらくなります
大規模で複雑な構成に対応できるようにマップを改善しましたが、マップに多数のノード、接続、クラスターとして機能するノードが含まれている場合があることがわかりました。  Microsoft では、スケーラビリティを向上させるサポートの継続的な強化に取り組んでいます。   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>[パフォーマンス] タブのネットワーク グラフと Azure VM の概要ページのネットワーク グラフが異なるのはなぜですか?

Azure VM の概要ページには、ゲスト VM でのアクティビティのホストの測定に基づいてグラフが表示されます。  Azure VM の概要のネットワーク グラフでは、課金対象となるネットワーク トラフィックのみが表示されます。  これには、VNET 間トラフィックは含まれません。  Azure Monitor for VMs に表示されるデータとグラフは、ゲスト VM のデータに基づいており、ネットワーク グラフには、VNET 間も含め、その VM に対する受信および送信のすべての TCP/IP トラフィックが表示されます。

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Log Analytics の無料プランを利用している場合、機能の制限はありますか?
*無料*の価格レベルを使った Log Analytics ワークスペースで Azure Monitor を構成した場合、Azure Monitor for VMs Map 機能では、ワークスペースに接続できるマシンが 5 つに制限されます。 無料のワークスペースに VM が 5 つ接続されている場合、いずれかの VM を切断した後に新しい VM を接続すると、新しい VM は監視されず、マップ ページにも反映されません。  

この条件下では、VM を開いて左側のウィンドウから **[Insights (プレビュー)]** を選択すると、機能が既に VM にインストール済みであっても、**[今すぐ試す]** オプションが表示されます。  ただし、その VM が Azure Monitor for VMs にオンボードされていない場合には、オプションは表示されません。 

## <a name="next-steps"></a>次の手順
仮想マシンの監視を有効にするための要件と方法を理解するため、「[Azure Monitor for VM の配布準備をする](vminsights-onboard.md)」を確認します。
