---
title: "Azure Log Analytics での Active Directory レプリケーションの状態の監視 | Microsoft Docs"
description: "Active Directory レプリケーションの状態ソリューション パックは、レプリケーションの失敗がないか Active Directory 環境を定期的に監視し、その結果を OMS ダッシュボードにレポートします。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 1b988972-8e01-4f83-a7f4-87f62778f91d
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a0c8af30fbed064001c3fd393bf0440aa1cb2835
ms.openlocfilehash: ffae86cb963f0df7f879effbace073d7e830cd94
ms.lasthandoff: 02/28/2017


---
# <a name="monitor-active-directory-replication-status-with-log-analytics"></a>Log Analytics で Active Directory レプリケーションの状態を監視する

Active Directory は、エンタープライズ IT 環境の重要なコンポーネントです。 各ドメイン コントローラーには、高い可用性とパフォーマンスを保証するために、Active Directory データベースのコピーが保持されています。 ドメイン コントローラーは、エンタープライズ全体に変更を反映するために、相互にレプリケートを行います。 このレプリケーション処理で失敗すると、エンタープライズ全体でさまざまな問題が発生します。

AD レプリケーションの状態ソリューション パックは、レプリケーションの失敗を Active Directory 環境で定期的に監視し、その結果を OMS ダッシュボードにレポートします。

## <a name="installing-and-configuring-the-solution"></a>ソリューションのインストールと構成
次の情報を使用して、ソリューションをインストールおよび構成します。

* 評価されるドメインのメンバーであるドメイン コントローラーか、AD レプリケーションのデータを OMS に送信するように構成されているメンバー サーバーに、エージェントをインストールする必要があります。 Windows コンピューターを OMS に接続する方法の詳細については、「 [Windows コンピューターを Log Analytics に接続する](log-analytics-windows-agents.md)」を参照してください。 ドメイン コントローラーが既に OMS に接続されている既存の System Center Operations Manager 環境の一部である場合、「 [Operations Manager を Log Analytics に接続する](log-analytics-om-agents.md)」を参照してください。
* Active Directory のレプリケーションの状態を OMS ワークスペースに追加するには、「 [ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」で説明されている手順に従います。  さらに手動で構成する必要はありません。

## <a name="ad-replication-status-data-collection-details"></a>AD レプリケーションの状態データの収集に関する詳細
次の表は、AD レプリケーションの状態のデータの収集手段と、データの収集方法に関する各種情報をまとめたものです。

| プラットフォーム | 直接エージェント | SCOM エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![あり](./media/log-analytics-ad-replication-status/oms-bullet-green.png) |![あり](./media/log-analytics-ad-replication-status/oms-bullet-green.png) |![なし](./media/log-analytics-ad-replication-status/oms-bullet-red.png) |![いいえ](./media/log-analytics-ad-replication-status/oms-bullet-red.png) |![あり](./media/log-analytics-ad-replication-status/oms-bullet-green.png) |5 日ごと |

## <a name="optionally-enable-a-non-domain-controller-to-send-ad-data-to-oms"></a>ドメイン コントローラー以外からの AD データの OMS への送信をオプションで有効にする
いずれのドメイン コントローラーも直接 OMS に接続しない場合、ドメイン内の OMS に接続されている他の任意のコンピューターを使用し、それに AD レプリケーションの状態ソリューション パック用にデータを送信させることができます。

### <a name="to-enable-a-non-domain-controller-to-send-ad-data-to-oms"></a>ドメイン コントローラー以外から AD データを OMS に送信するには
1. コンピューターが、AD レプリケーションの状態ソリューションを使用して監視するドメインのメンバーであることを確認します。
2. まだ接続されていない場合は、[Windows コンピューターを OMS に接続する](log-analytics-windows-agents.md)か、[既存の Operations Manager 環境を使用してそれを OMS に接続します](log-analytics-om-agents.md)。
3. そのコンピューターで、次のレジストリ キーを設定します。

   * キー: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName>\Solutions\ADReplication**
   * 値: **IsTarget**
   * 値のデータ: **true**

   > [!NOTE]
   > これらの変更は、Microsoft Monitoring Agent サービス (HealthService.exe) を再起動するまで反映されません。
   >
   >

## <a name="understanding-replication-errors"></a>レプリケーションの問題を理解する
AD レプリケーションの状態データが OMS に送信されたら、OMS ダッシュボードには現在のレプリケーション エラー数を示す、次のようなタイルが表示されます。  
![AD レプリケーションの状態タイル](./media/log-analytics-ad-replication-status/oms-ad-replication-tile.png)

**重大なレプリケーション エラー** ] は、Active Directory フォレスト内の [ [廃棄 (tombstone) の有効期間](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx)  が 75% を超えるエラーです。

タイルをクリックすると、エラーの詳細をさらに表示できます。
![AD レプリケーションの状態ダッシュボード](./media/log-analytics-ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>移行先サーバーと移行元サーバーの状態
これらのブレードでは、レプリケーション エラーが発生している移行先サーバーと移行元サーバーの状態を示しています。 各ドメイン コントローラー名の後には、そのドメイン コントローラーで発生したレプリケーション エラーの数があります。

トラブルシューティングを実行する場合、移行先サーバーの観点から問題を解決した方が簡単な場合と、移行元サーバーの観点から問題を解決した方が簡単な場合があるので、移行先サーバーと移行元サーバーの両方のエラーが表示されています。

この例では、多数の移行先サーバーのエラーがほぼ同数である中、1 つの移行元サーバー (ADDC35) には他より多くのエラーがあることを確認できます。 ADDC35 に、レプリケーション パートナーへのデータ送信が失敗する原因となる、何らかの問題がある可能性があることがわかります。 その問題を ADDC35 で解決すると、移行先サーバー ブレードに表示されるエラーの多くを解決できる可能性があります。

### <a name="replication-error-types"></a>レプリケーション エラーの種類
このブレードには、エンタープライズ全体で検出されるエラーの種類の情報が示されます。 各エラーには、一意の数値コードと、エラーの根本原因の特定に役立つメッセージがあります。

上部に示されるドーナツでは、環境内でどのエラーが多く、どのエラーが少ないかのイメージを示しています。

ここでは、同じレプリケーション エラーが複数のドメイン コントローラーで発生している場合にそれが明確になります。 その場合、1 つのドメイン コントローラーで解決策を特定し、同じエラーが発生している他のドメイン コントローラーでそれを繰り返します。

### <a name="tombstone-lifetime"></a>廃棄 (tombstone) の有効期間
廃棄 (tombstone) の有効期間は、廃棄と呼ばれる削除されたオブジェクトが Active Directory データベースに保持される期間を決定します。 削除されたオブジェクトは、廃棄 (tombstone) の有効期間を過ぎると、ガベージ コレクション処理によって Active Directory データベースから自動的に削除されます。

既定の廃棄 (tombstone) の有効期間は、最新バージョンの Windows では 180 日間ですが、古いバージョンでは 60 日でした。これは、Active Directory 管理者が明示的に変更することができます。

廃棄 (tombstone) の有効期間が近づいている、またはそれが経過したレプリケーション エラーは把握しておく必要があります。 廃棄 (tombstone) の有効期間が経過しても&2; つのドメイン コントローラー間でレプリケーション エラーが続いている場合、基になるレプリケーション エラーを修正しても、その&2; つのドメイン コントローラー間でのレプリケーションは無効となります。

廃棄 (tombstone) の有効期間のブレードを使用すると、これが発生する危険性ある個所を特定できます。 [ **TSL 100% 超** ] カテゴリ内の各エラーは、少なくともフォレストの廃棄 (tombstone) の有効期間、移行元サーバーと移行先サーバーでレプリケーションが行われていないパーティションを表しています。

このような場合、レプリケーション エラーを修正するだけでは不十分です。 レプリケーションを再開するには、手動で調べて、長引いているオブジェクトを特定してそれをクリーンアップする必要があります。 場合によっては、ドメイン コントローラーの使用を停止する必要があります。

廃棄 (tombstone) の有効期間が経過しても続くレプリケーション エラーを識別するだけでなく、[**TSL&50; ～&75;%**] または [**TSL&75; ～&100;%**] のカテゴリのすべてのエラーにも注意を払う必要があります。

これらは、明らかに長引いている一時的なエラーではないため、介入して解決する必要があります。 さいわいにも、それらは廃棄 (tombstone) の有効期間には到達していません。 これらの問題を、廃棄 (tombstone) の有効期間に *達する前に* 迅速に解決した場合、最小限の介入でレプリケーションを再開できます。

前述のとおり、AD レプリケーションの状態ソリューションのダッシュボード タイルには、廃棄 (tombstone) の有効期間の 75% を経過した、環境内の *重大な* レプリケーション エラーが示されています (これには、TSL の 100% を超えるエラーも含みます)。 この数字は 0 に維持できるよう努めてください。

> [!NOTE]
> 廃棄 (tombstone) の有効期間のすべてのパーセンテージの計算は、Active Directory フォレストの実際の廃棄 (tombstone) の有効期限に基づいています。したがって廃棄 (tombstone) の有効期間の値をカスタマイズしている場合でも、それらのパーセンテージは正確であると信頼できます。
>
>

### <a name="ad-replication-status-details"></a>AD レプリケーションの状態の詳細
一覧のいずれかの任意の項目をクリックすると、ログ検索にその詳細を表示できます。 その項目に関連するエラーのみが表示されるよう、結果はフィルター処理されます。 たとえば、[ **移行先サーバーの状態 (ADDC02)**] 下の最初のドメイン コントローラーをクリックすると、移行先サーバーであるそのドメイン コントローラーのエラーがフィルター処理された検索結果が表示されます。

![検索結果内の AD レプリケーションの状態エラー](./media/log-analytics-ad-replication-status/oms-ad-replication-search-details.png)

ここで、さらにフィルタリングを行い、検索クエリなどの修正を行います。 ログ検索の使用方法の詳細については、「 [ログ検索](log-analytics-log-searches.md)」を参照してください。

[ **HelpLink** ] フィールドには、その特定のエラーの追加情報を含む TechNet ページの URL が表示されます。 このエラーをトラブルシューティングして修正するには、このリンクをブラウザー ウィンドウにコピーして貼り付け、情報を確認します。

[ **エクスポート** ] をクリックして、結果を Excel にエクスポートすることもできます。 これを行うと、希望する方法でレプリケーション エラーを視覚化できます。

![Excel にエクスポートされた AD レプリケーションの状態エラー](./media/log-analytics-ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>AD レプリケーションの状態の FAQ
**Q: AD レプリケーションの状態データはどのような頻度で更新されますか?**
A: この情報は 5 日おきに更新されます。

**Q: このデータの更新頻度を構成する方法がありますか?**
A: 現時点ではありません。

**Q: レプリケーションの状態の確認用に、OMS ワークスペースにすべてのドメイン コントローラーを追加する必要がありますか?**
A: いいえ。ドメイン コントローラーは&1; つのみ追加します。 OMS ワークスペースにドメイン コントローラーが複数ある場合、それらのすべてのデータが OMS に送信されます。

**Q: OMS ワークスペースに、ドメイン コントローラーを追加したくありません。それでも AD レプリケーションの状態ソリューションを使用できますか?**
A: はい。 レジストリ キー値を設定して、これを有効にできます。 「[ドメイン コントローラー以外から AD データを OMS に送信するには](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms)」を参照してください。

**Q: データ収集を行うプロセスの名前は何ですか?**
A: AdvisorAssessment.exe です。

**Q: データの収集にはどれくらいの時間がかかりますか?**
A: Active Directory 環境の規模によって異なりますが、データの収集時間は通常は 15 分未満です。

**Q: どのような種類のデータが収集されますか?**
A: LDAP 経由でレプリケーションの情報は収集されます。

**Q: データが収集されるタイミングを構成する方法はありますか?**
A: 現時点ではありません。

**Q: データを収集するには、どのようなアクセス許可が必要ですか?**
A: Active Directory への標準のアクセス許可で通常は十分です。

## <a name="troubleshoot-data-collection-problems"></a>データの収集の問題のトラブルシューティング
AD レプリケーションの状態ソリューション パックでデータを収集する場合、OMS ワークスペースには少なくとも&1; つのドメイン コントローラーが接続されている必要があります。 この操作を行うまで、[ **データはまだ収集中です**] というメッセージが表示されます。

1 つのドメイン コントローラーの接続に支援が必要な場合は、「 [Windows コンピューターを Log Analytics に接続する](log-analytics-windows-agents.md)」のドキュメントを参照してください。 または、ドメイン コントローラーが既存の System Center Operations Manager 環境に接続されている場合は、「 [System Center Operations Manager を Log Analytics に接続する](log-analytics-om-agents.md)」のドキュメントを参照してください。

ドメイン コントローラーを OMS または SCOM に直接接続しない場合は、「 [ドメイン コントローラー以外から AD データを OMS に送信するには](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms)」を参照してください。

## <a name="next-steps"></a>次のステップ
* Active Directory のレプリケーション状態の詳細データを参照するには、「 [Log Analytics におけるログの検索](log-analytics-log-searches.md) 」を使用してください。

