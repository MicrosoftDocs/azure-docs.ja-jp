---
title: SAP HANA on Azure (L インスタンス) での HANA 側からの監視とトラブルシューティング | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) での HANA 側からの監視とトラブルシューティング
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83743a6985bef8ce6c03e01ed8d10aa740852106
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668810"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>HANA 側からの監視とトラブルシューティング

SAP HANA on Azure (L インスタンス) に関連した問題を効果的に分析するには、問題の根本原因を絞り込むことが役立ちます。 SAP は、役に立つ大量のドキュメントを公開しています。

SAP HANA のパフォーマンスに関連した FAQ は、以下の SAP Note で参照できます。

- [SAP Note #2222200 – FAQ: SAP HANA のネットワーク](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP Note #2100040 – FAQ: SAP HANA の CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP Note #199997 – FAQ: SAP HANA のメモリ](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP Note #200000 – FAQ: SAP HANA のパフォーマンス最適化](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP Note #199930 – FAQ: SAP HANA の I/O 分析](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP Note #2177064 – FAQ: SAP HANA のサービスの再起動とクラッシュ](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>SAP HANA アラート

最初の手順として、現在の SAP HANA アラート ログを確認します。 SAP HANA Studio で、 **[Administration Console (管理コンソール)]、[Alerts (アラート)]、[Show (表示)]、[all alerts (すべてのアラート)]** の順に移動します。 このタブには、設定されている最小および最大しきい値から外れる特定の値 (空き物理メモリ、CPU 使用率など) のすべての SAP HANA アラートが表示されます。 既定では、チェックが 15 分ごとに自動更新されます。

![SAP HANA Studio で、[Administration Console (管理コンソール)]、[Alerts (アラート)]、[Show (表示)]、[all alerts (すべてのアラート)] の順に移動する](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

しきい値の設定が不適切であるためにトリガーされたアラートの場合、解決方法は、既定値またはより適切なしきい値に再設定することです。

![既定値またはより適切なしきい値に再設定する](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

以下のアラートは、CPU リソースの問題を示している場合があります。

- ホスト CPU 使用率 (アラート 5)
- 最新のセーブポイント操作 (アラート 28)
- セーブポイント期間 (アラート 54)

次のいずれかによって、SAP HANA データベースで CPU 使用率が高いことに気付くことがあります。

- 現在または過去の CPU 使用率で、アラート 5 (ホスト CPU 使用率) が発生する
- 概要画面に表示されている CPU 使用率

![概要画面に表示されている CPU 使用率](./media/troubleshooting-monitoring/image3-cpu-usage.png)

[Load (負荷)] のグラフには、高い CPU 使用率や、過去の高い使用率が示される場合があります。

![[Load (負荷)] のグラフに高い CPU 使用率や過去の高い使用率が示される場合がある](./media/troubleshooting-monitoring/image4-load-graph.png)

高い CPU 使用率が原因でトリガーされるアラートには、いくつかの理由が考えられます。たとえば、特定のトランザクションの実行、データの読み込み、応答していないジョブ、SQL ステートメントの長時間実行、クエリの低パフォーマンス (例: HANA キューブでの BW の場合) などです。

トラブルシューティングの詳細な手順については、[SAP HANA トラブルシューティングの CPU 関連の原因と解決策](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false)に関するサイトを参照してください。

## <a name="operating-system"></a>オペレーティング システム

SAP HANA on Linux に対する最も重要なチェックの 1 つとして、Transparent Huge Pages が無効になっていることを確認します。[SAP Note #2131662 の SAP HANA サーバーでの Transparent Huge Pages (THP)](https://launchpad.support.sap.com/#/notes/2131662)に関するページを参照してください。

- Transparent Huge Pages が有効になっているかどうかを確認するには、**cat /sys/kernel/mm/transparent\_hugepage/enabled** という Linux コマンドを使用します。
- _always_ が角かっこに囲まれて [always] madvise never のようになっている場合、Transparent Huge Pages は有効になっています。_never_ が角かっこに囲まれて always madvise [never] のようになっている場合、Transparent Huge Pages は無効になっています。

Linux コマンドの **rpm -qa | grep ulimit** は何も返しません。 _ulimit_ がインストールされていることがわかった場合は、すぐにアンインストールしてください。

## <a name="memory"></a>メモリ

SAP HANA データベースによって割り当てられたメモリの量が予想よりも大きいことに気付く場合があります。 以下のアラートは、メモリ使用量が高いことに関連した問題を示しています。

- ホスト物理メモリ使用量 (アラート 1)
- ネーム サーバーのメモリ使用量 (アラート 12)
- 列ストア テーブルの合計メモリ使用量 (アラート 40)
- サービスのメモリ使用量 (アラート 43)
- 列ストア テーブルのメイン ストレージのメモリ使用量 (アラート 45)
- ランタイム ダンプ ファイル (アラート 46)

トラブルシューティングの詳細な手順については、[SAP HANA トラブルシューティングのメモリの問題](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false)に関するサイトを参照してください。

## <a name="network"></a>ネットワーク

[SAP Note #2081065 – SAP HANA ネットワークのトラブルシューティング](https://launchpad.support.sap.com/#/notes/2081065)のページを参照して、この SAP Note に記載されているネットワークに関するトラブルシューティングの手順を実行してください。

1. サーバーとクライアント間のラウンド トリップ時間を分析します。
  A. SQL スクリプト [_HANA\_Network\_Clients_](https://launchpad.support.sap.com/#/notes/1969700) を実行します。
  
2. ノード間通信を分析します。
  A. SQL スクリプト [_HANA\_Network\_Services_](https://launchpad.support.sap.com/#/notes/1969700) を実行します。

3. Linux コマンド **ifconfig** を実行します (出力は、パケット損失が発生しているかどうかを示します)。
4. Linux コマンド **tcpdump** を実行します。

さらに、オープン ソースの [IPERF](https://iperf.fr/) ツール (または同様のツール) を使用して、実際のアプリケーション ネットワーク パフォーマンスを測定します。

トラブルシューティングの詳細な手順については、[SAP HANA トラブルシューティングのネットワークのパフォーマンスと接続の問題](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false)に関するサイトを参照してください。

## <a name="storage"></a>ストレージ

I/O パフォーマンスに問題がある場合、エンドユーザーからは、アプリケーション (またはシステム全体) が遅い、反応しない、さらには反応が停止しているように感じられる場合があります。 SAP HANA Studio の **[Volumes (ボリューム)]** タブでは、接続されているボリュームと、各サービスによってどのボリュームが使用されているかを確認できます。

![SAP HANA Studio の [Volumes (ボリューム)] タブでは、接続されているボリュームと、各サービスによってどのボリュームが使用されているかを確認できる](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

画面下部では、接続されたボリュームの詳細 (ファイルや I/O の統計など) を確認できます。

![画面下部では、接続されたボリュームの詳細 (ファイルや I/O の統計など) を確認できる](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

トラブルシューティングの詳細な手順については、[SAP HANA トラブルシューティングの I/O 関連の根本原因と解決策](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false)に関するサイトと [SAP HANA トラブルシューティングのディスク関連の根本原因と解決策](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false)に関するサイトを参照してください。

## <a name="diagnostic-tools"></a>診断ツール

HANA\_Configuration\_Minichecks を使用して SAP HANA Health Check を実行します。 このツールは、SAP HANA Studio で既にアラートとして表示されているはずの潜在的に重大な技術上の問題を返します。

[SAP Note #1969700 の SAP HANA の SQL ステートメント コレクション](https://launchpad.support.sap.com/#/notes/1969700)のページを参照し、そのページに添付されている SQL Statements.zip ファイルをダウンロードしてください。 この .zip ファイルをローカルのハード ドライブに保存します。

SAP HANA Studio の **[System Information (システム情報)]** タブで、 **[Name (名前)]** 列を右クリックし、 **[Import SQL Statements (SQL ステートメントのインポート)]** を選択します。

![SAP HANA Studio の [System Information (システム情報)] タブで、[Name (名前)] 列を右クリックし、[Import SQL Statements (SQL ステートメントのインポート)] を選択する](./media/troubleshooting-monitoring/image7-import-statements-a.png)

ローカルに保存されている SQL Statements.zip ファイルを選択すると、対応する SQL ステートメントが含まれているフォルダーがインポートされます。 この時点で、これらの SQL ステートメントを使用して、さまざまな診断チェックを実行できます。

たとえば、SAP HANA システム レプリケーションの帯域幅要件をテストするには、SQL コンソールで **[Replication: Bandwidth (レプリケーション: 帯域幅)]** の下の **Bandwidth** ステートメントを右クリックし、**[Open (開く)]** を選択します。

完全な SQL ステートメントが開かれるので、入力パラメーター (変更セクション) を変更し、実行することができます。

![完全な SQL ステートメントが開かれるので、入力パラメーター (変更セクション) を変更し、実行することができる](./media/troubleshooting-monitoring/image8-import-statements-b.png)

別の例では、 **[Replication: Overview (レプリケーション: 概要)]** の下のステートメントを右クリックします。 コンテキスト メニューで **[Execute (実行)]** を選択します。

![別の例では、[Replication: Overview (レプリケーション: 概要)] の下のステートメントを右クリックし、 コンテキスト メニューで [Execute (実行)] を選択する](./media/troubleshooting-monitoring/image9-import-statements-c.png)

その結果、次のように、トラブルシューティングに役立つ情報が表示されます。

![その結果、トラブルシューティングに役立つ情報が表示される](./media/troubleshooting-monitoring/image10-import-statements-d.png)

HANA\_Configuration\_Minichecks でも同じことを実行し、_[C]_ (重大度) 列の _X_ マークを確認します。

サンプル出力:

全般的な SAP HANA チェックを示す **HANA\_Configuration\_MiniChecks\_Rev102.01+1**。

![全般的な SAP HANA チェックを示す HANA\_Configuration\_MiniChecks\_Rev102.01+1](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

SAP HANA サービスの現在の実行内容の概要を示す **HANA\_Services\_Overview**。

![SAP HANA サービスの現在の実行内容の概要を示す HANA\_Services\_Overview](./media/troubleshooting-monitoring/image12-services-overview.png)

SAP HANA のサービス情報 (CPU、メモリなど) を示す **HANA\_Services\_Statistics**。

![SAP HANA のサービス情報を示す HANA\_Services\_Statistics](./media/troubleshooting-monitoring/image13-services-statistics.png)

SAP HANA インスタンスの全般的な情報を示す **HANA\_Configuration\_Overview\_Rev110+**。

![SAP HANA インスタンスの全般的な情報を示す HANA\_Configuration\_Overview\_Rev110+](./media/troubleshooting-monitoring/image14-configuration-overview.png)

SAP HANA パラメーターをチェックする **HANA\_Configuration\_Parameters\_Rev70+** 。

![SAP HANA パラメーターをチェックする HANA\_Configuration\_Parameters\_Rev70+](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**次の手順**

- 「[STONITH を使用した SUSE での高可用性のセットアップ](ha-setup-with-stonith.md)」を参照してください。