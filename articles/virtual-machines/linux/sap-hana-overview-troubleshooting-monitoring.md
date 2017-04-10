---
title: "SAP HANA on Azure (L インスタンス) のトラブルシューティングと監視 | Microsoft Docs"
description: "SAP HANA on Azure (L インスタンス) のトラブルシューティングと監視を行います。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fa842efd99718be7fa9eaf8aac8030c32cbceeec
ms.openlocfilehash: da712db02fe91f7d075e5203b627a12f341fac78
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-troubleshoot-and-monitor-sap-hana-large-instances-on-azure"></a>SAP HANA on Azure (L インスタンス) のトラブルシューティングと監視の方法


## <a name="monitoring-in-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (L インスタンス) での監視

SAP HANA on Azure (L インスタンス) には、他の IaaS デプロイとの違いはありません。OS とアプリケーションが何を行っているかと、それらがどのように以下のリソースを使用しているかを監視する必要があります。

- CPU
- メモリ
- ネットワーク帯域幅
- ディスク領域

Azure Virtual Machines と同様に、上に挙げたリソース クラスが十分であるか不足しているかを把握する必要があります。 各クラスの詳細は次のとおりです。

**CPU リソースの使用量:** メモリに保存されているデータの操作に使用できる十分な CPU リソースを確保するために、HANA に対する特定のワークロード用に SAP が定義した比率が強制されます。 しかし、HANA がクエリを実行し、インデックスが見つからないなどの問題のために大量の CPU を使用する場合もあります。 つまり、HANA L インスタンス ユニットによる CPU リソースの使用量と、特定の HANA サービスによって使用されている CPU リソースを監視する必要があります。

**メモリの使用量:** HANA 内部からだけでなく、ユニット上の HANA の外部からも監視することが重要です。 HANA 内部では、HANA に割り当てられているメモリをデータがどのように使用しているかを監視して、SAP の必要サイズのガイドライン内に収まるようにします。 追加でインストールした非 HANA ソフトウェアがメモリを過剰に使用して HANA とメモリを奪い合っていないことを確認するために、L インスタンス レベルのメモリ使用量を監視する場合もあります。

**ネットワーク帯域幅:** Azure VNet ゲートウェイは Azure VNet に移動するデータの帯域幅による制限を受けるので、VNet 内のすべての Azure VM が受信するデータを監視することは、選択した Azure ゲートウェイ SKU の上限にどのくらい近づいているかを判断するうえで役に立ちます。 HANA L インスタンス ユニットでは、着信および発信ネットワーク トラフィックを監視することと、処理量の推移を追跡することにも意味があります。

**ディスク領域:** ディスク領域の使用量は、通常、時間の経過と共に増加します。 これには多くの原因が考えられますが、多くの場合は、データ量の増加、トランザクション ログ バックアップの実行、トレース ファイルの保存、およびストレージ スナップショットの実行が原因です。 そのため、ディスク領域の使用量を監視し、HANA L インスタンス ユニットに関連付けられているディスク領域を管理することが重要です。

## <a name="monitoring-and-troubleshooting-from-hana-side"></a>HANA 側からの監視とトラブルシューティング

SAP HANA on Azure (L インスタンス) に関連した問題を効果的に分析するには、問題の根本原因を絞り込むことが役立ちます。 SAP は、役に立つ大量のドキュメントを公開しています。

SAP HANA のパフォーマンスに関連した FAQ は、以下の SAP Note で参照できます。

- [SAP Note #2222200 – FAQ: SAP HANA のネットワーク](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP Note #2100040 – FAQ: SAP HANA の CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP Note #199997 – FAQ: SAP HANA のメモリ](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP Note #200000 – FAQ: SAP HANA のパフォーマンス最適化](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP Note #199930 – FAQ: SAP HANA の I/O 分析](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP Note #2177064 – FAQ: SAP HANA のサービスの再起動とクラッシュ](https://launchpad.support.sap.com/#/notes/2177064)

**SAP HANA のアラート**

最初の手順として、現在の SAP HANA アラート ログを確認します。 SAP HANA Studio で、**[Administration Console (管理コンソール)]、[Alerts (アラート)]、[Show (表示)]、[all alerts (すべてのアラート)]** の順に移動します。 このタブには、設定されている最小および最大しきい値から外れる特定の値 (空き物理メモリ、CPU 使用率など) のすべての SAP HANA アラートが表示されます。 既定では、チェックが 15 分ごとに自動更新されます。

![SAP HANA Studio で、[Administration Console (管理コンソール)]、[Alerts (アラート)]、[Show (表示)]、[all alerts (すべてのアラート)] の順に移動する](./media/sap-hana-overview-troubleshooting-monitoring/image1-show-alerts.png)

**CPU**

しきい値の設定が不適切であるためにトリガーされたアラートの場合、解決方法は、既定値またはより適切なしきい値に再設定することです。

![既定値またはより適切なしきい値に再設定する](./media/sap-hana-overview-troubleshooting-monitoring/image2-cpu-utilization.png)

以下のアラートは、CPU リソースの問題を示している場合があります。

- ホスト CPU 使用率 (アラート 5)
- 最新のセーブポイント操作 (アラート 28)
- セーブポイント期間 (アラート 54)

次のいずれかによって、SAP HANA データベースで CPU 使用率が高いことに気付くことがあります。

- 現在または過去の CPU 使用率で、アラート 5 (ホスト CPU 使用率) が発生する
- 概要画面に表示されている CPU 使用率

![概要画面に表示されている CPU 使用率](./media/sap-hana-overview-troubleshooting-monitoring/image3-cpu-usage.png)

[Load (負荷)] のグラフには、高い CPU 使用率や、過去の高い使用率が示される場合があります。

![[Load (負荷)] のグラフに高い CPU 使用率や過去の高い使用率が示される場合がある](./media/sap-hana-overview-troubleshooting-monitoring/image4-load-graph.png)

高い CPU 使用率が原因でトリガーされるアラートには、いくつかの理由が考えられます。たとえば、特定のトランザクションの実行、データの読み込み、ジョブの停止、SQL ステートメントの長時間実行、クエリの低パフォーマンス (例: HANA キューブでの BW の場合) などです。

トラブルシューティングの詳細な手順については、[SAP HANA トラブルシューティングの CPU 関連の原因と解決策](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false)に関するサイトを参照してください。

**オペレーティング システム**

SAP HANA on Linux に対する最も重要なチェックの 1 つとして、Transparent Huge Pages が無効になっていることを確認します。[SAP Note #2131662 の SAP HANA サーバーでの Transparent Huge Pages (THP)](https://launchpad.support.sap.com/#/notes/2131662)に関するページを参照してください。

- Transparent Huge Pages が有効になっているかどうかを確認するには、**cat /sys/kernel/mm/transparent\_hugepage/enabled** という Linux コマンドを使用します。
- _always_ が角かっこに囲まれて [always] madvise never のようになっている場合、Transparent Huge Pages は有効になっています。_never_ が角かっこに囲まれて always madvise [never] のようになっている場合、Transparent Huge Pages は無効になっています。

Linux コマンドの **rpm -qa | grep ulimit** は何も返しません。 _ulimit_ がインストールされていることがわかった場合は、すぐにアンインストールしてください。

**メモリ**

SAP HANA データベースによって割り当てられたメモリの量が予想よりも大きいことに気付く場合があります。 以下のアラートは、メモリ使用量が高いことに関連した問題を示しています。

- ホスト物理メモリ使用量 (アラート 1)
- ネーム サーバーのメモリ使用量 (アラート 12)
- 列ストア テーブルの合計メモリ使用量 (アラート 40)
- サービスのメモリ使用量 (アラート 43)
- 列ストア テーブルのメイン ストレージのメモリ使用量 (アラート 45)
- ランタイム ダンプ ファイル (アラート 46)

トラブルシューティングの詳細な手順については、[SAP HANA トラブルシューティングのメモリの問題](http://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false)に関するサイトを参照してください。

**ネットワーク**

[SAP Note #2081065 – SAP HANA ネットワークのトラブルシューティング](https://launchpad.support.sap.com/#/notes/2081065)のページを参照して、この SAP Note に記載されているネットワークに関するトラブルシューティングの手順を実行してください。

1. サーバーとクライアント間のラウンド トリップ時間を分析します。
  A. SQL スクリプト [_HANA\_Network\_Clients_](https://launchpad.support.sap.com/#/notes/1969700)__ を実行します。
  
2. ノード間通信を分析します。
  A. SQL スクリプト [_HANA\_Network\_Services_](https://launchpad.support.sap.com/#/notes/1969700)__ を実行します。

3. Linux コマンド **ifconfig** を実行します (出力は、パケット損失が発生しているかどうかを示します)。
4. Linux コマンド **tcpdump** を実行します。

さらに、オープン ソースの [IPERF](https://iperf.fr/) ツール (または同様のツール) を使用して、実際のアプリケーション ネットワーク パフォーマンスを測定します。

トラブルシューティングの詳細な手順については、[SAP HANA トラブルシューティングのネットワークのパフォーマンスと接続の問題](http://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false)に関するサイトを参照してください。

**Storage**

I/O パフォーマンスに問題があると、エンドユーザーの観点からは、アプリケーション (またはシステム全体) が遅い、反応しない、さらには停止していると思われてしまう場合があります。 SAP HANA Studio の **[Volumes (ボリューム)]** タブでは、接続されているボリュームと、各サービスによってどのボリュームが使用されているかを確認できます。

![SAP HANA Studio の [Volumes (ボリューム)] タブでは、接続されているボリュームと、各サービスによってどのボリュームが使用されているかを確認できる](./media/sap-hana-overview-troubleshooting-monitoring/image5-volumes-tab-a.png)

画面下部では、接続されたボリュームの詳細 (ファイルや I/O の統計など) を確認できます。

![画面下部では、接続されたボリュームの詳細 (ファイルや I/O の統計など) を確認できる](./media/sap-hana-overview-troubleshooting-monitoring/image6-volumes-tab-b.png)

トラブルシューティングの詳細な手順については、[SAP HANA トラブルシューティングの I/O 関連の根本原因と解決策](http://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false)に関するサイトと [SAP HANA トラブルシューティングのディスク関連の根本原因と解決策](http://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false)に関するサイトを参照してください。

**診断ツール**

HANA\_Configuration\_Minichecks を使用して SAP HANA Health Check を実行します。 このツールは、SAP HANA Studio で既にアラートとして表示されているはずの潜在的に重大な技術上の問題を返します。

[SAP Note #1969700 の SAP HANA の SQL ステートメント コレクション](https://launchpad.support.sap.com/#/notes/1969700)のページを参照し、そのページに添付されている SQL Statements.zip ファイルをダウンロードしてください。 この .zip ファイルをローカルのハード ドライブに保存します。

SAP HANA Studio の **[System Information (システム情報)]** タブで、**[Name (名前)]** 列を右クリックし、**[Import SQL Statements (SQL ステートメントのインポート)]** を選択します。

![SAP HANA Studio の [System Information (システム情報)] タブで、[Name (名前)] 列を右クリックし、[Import SQL Statements (SQL ステートメントのインポート)] を選択する](./media/sap-hana-overview-troubleshooting-monitoring/image7-import-statements-a.png)

ローカルに保存されている SQL Statements.zip ファイルを選択すると、対応する SQL ステートメントが含まれているフォルダーがインポートされます。 この時点で、これらの SQL ステートメントを使用して、さまざまな診断チェックを実行できます。

たとえば、SAP HANA システム レプリケーションの帯域幅要件をテストするには、SQL コンソールで **[Replication: Bandwidth (レプリケーション: 帯域幅)]** の下の **Bandwidth** ステートメントを右クリックし、**[Open (開く)]** を選択します。

完全な SQL ステートメントが開かれるので、入力パラメーター (変更セクション) を変更し、実行することができます。

![完全な SQL ステートメントが開かれるので、入力パラメーター (変更セクション) を変更し、実行することができる](./media/sap-hana-overview-troubleshooting-monitoring/image8-import-statements-b.png)

別の例では、**[Replication: Overview (レプリケーション: 概要)]** の下のステートメントを右クリックします。 コンテキスト メニューで **[Execute (実行)]** を選択します。

![別の例では、[Replication: Overview (レプリケーション: 概要)] の下のステートメントを右クリックし、 コンテキスト メニューで [Execute (実行)] を選択する](./media/sap-hana-overview-troubleshooting-monitoring/image9-import-statements-c.png)

その結果、次のように、トラブルシューティングに役立つ情報が表示されます。

![その結果、トラブルシューティングに役立つ情報が表示される](./media/sap-hana-overview-troubleshooting-monitoring/image10-import-statements-d.png)

HANA\_Configuration\_Minichecks でも同じことを実行し、_[C]_ (重大度) 列の _X_ マークを確認します。

サンプル出力:

全般的な SAP HANA チェックを示す **HANA\_Configuration\_MiniChecks\_Rev102.01+1**。

![全般的な SAP HANA チェックを示す HANA\_Configuration\_MiniChecks\_Rev102.01+1](./media/sap-hana-overview-troubleshooting-monitoring/image11-configuration-minichecks.png)

SAP HANA サービスの現在の実行内容の概要を示す **HANA\_Services\_Overview**。

![SAP HANA サービスの現在の実行内容の概要を示す HANA\_Services\_Overview](./media/sap-hana-overview-troubleshooting-monitoring/image12-services-overview.png)

SAP HANA のサービス情報 (CPU、メモリなど) を示す **HANA\_Services\_Statistics**。

![SAP HANA のサービス情報を示す HANA\_Services\_Statistics ](./media/sap-hana-overview-troubleshooting-monitoring/image13-services-statistics.png)

SAP HANA インスタンスの全般的な情報を示す **HANA\_Configuration\_Overview\_Rev110+**。

![SAP HANA インスタンスの全般的な情報を示す HANA\_Configuration\_Overview\_Rev110+](./media/sap-hana-overview-troubleshooting-monitoring/image14-configuration-overview.png)

SAP HANA パラメーターをチェックする **HANA\_Configuration\_Parameters\_Rev70+**。

![SAP HANA パラメーターをチェックする HANA\_Configuration\_Parameters\_Rev70+](./media/sap-hana-overview-troubleshooting-monitoring/image15-configuration-parameters.png)


