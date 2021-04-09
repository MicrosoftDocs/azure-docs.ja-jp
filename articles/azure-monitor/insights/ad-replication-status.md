---
title: Active Directory レプリケーションの状態を監視する
description: Active Directory レプリケーションの状態ソリューション パックは、レプリケーションの失敗がないか Active Directory 環境を定期的に監視します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/24/2018
ms.openlocfilehash: c99ad16e119c4262aa6d9d645b2457cdd46061b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700679"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Azure Monitor で Active Directory のレプリケーション状態を監視する

![AD レプリケーションの状態のシンボル](./media/ad-replication-status/ad-replication-status-symbol.png)

Active Directory は、エンタープライズ IT 環境の重要なコンポーネントです。 各ドメイン コントローラーには、高い可用性とパフォーマンスを保証するために、Active Directory データベースのコピーが保持されています。 ドメイン コントローラーは、エンタープライズ全体に変更を反映するために、相互にレプリケートを行います。 このレプリケーション処理で失敗すると、エンタープライズ全体でさまざまな問題が発生します。

AD レプリケーションの状態ソリューションは、レプリケーションの失敗を Active Directory 環境で定期的に監視します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>ソリューションのインストールと構成
次の情報を使用して、ソリューションをインストールおよび構成します。

### <a name="prerequisites"></a>前提条件

* AD レプリケーションの状態ソリューションを使用するには、各コンピューターに (Microsoft Monitoring Agent (MMA) とも呼ばれる) Windows 用の Log Analytics エージェントがインストールされ、4.6.2 以降のサポートされているバージョンの .NET Framework がインストールされている必要があります。  このエージェントは、System Center 2016 (Operations Manager および Operations Manager 2012 R2) と Azure Monitor に使用されます。
* このソリューションは、Windows Server 2008 および 2008 R2、Windows Server 2012 および 2012 R2、および Windows Server 2016 を実行するドメイン コントローラーをサポートしています。
* Azure Portal で Azure Marketplace から Active Directory 正常性チェック ソリューションを追加する Log Analytics ワークスペース。 追加の構成は必要ありません。


### <a name="install-agents-on-domain-controllers"></a>ドメイン コントローラーにエージェントをインストールする
評価されるドメインのメンバーであるドメイン コントローラーに、エージェントをインストールする必要があります。 または、メンバー サーバーにエージェントをインストールし、AD レプリケーションのデータを Azure Monitor に送信するように、そのエージェントを構成する必要があります。 Windows コンピューターを Azure Monitor に接続する方法について詳しくは、[Azure Monitor への Windows コンピューターの接続](../agents/agent-windows.md)に関する記事をご覧ください。 ドメイン コントローラーが、Azure Monitor に接続する既存の System Center Operations Manager 環境の一部である場合は、[Azure Monitor への Operations Manager の接続](../agents/om-agents.md)に関する記事をご覧ください。

### <a name="enable-non-domain-controller"></a>非ドメイン コントローラーを有効にする
いずれのドメイン コントローラーも直接 Azure Monitor に接続しない場合、Azure Monitor に接続されているドメイン内の他の任意のコンピューターを使用して AD Replication Status ソリューション パック用にデータを収集し、そのデータを送信させることができます。

1. コンピューターが、AD レプリケーションの状態ソリューションを使用して監視するドメインのメンバーであることを確認します。
2. まだ接続されていない場合は、[Windows コンピューターを Azure Monitor に接続する](../agents/om-agents.md)か、[既存の Operations Manager 環境を使用してそれを Azure Monitor に接続します](../agents/om-agents.md)。
3. そのコンピューターで、次のレジストリ キーを設定します。<br>キー:**HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName>\Solutions\ADReplication**<br>値:**IsTarget**<br>値のデータ: **true**

   > [!NOTE]
   > こうした変更は、Microsoft Monitoring Agent サービス (HealthService.exe) を再起動するまで反映されません。
   > ### <a name="install-solution"></a>ソリューションをインストールする
   > 「[監視ソリューションをインストールする](solutions.md#install-a-monitoring-solution)」で説明されている手順に従って、**Active Directory Replication Status** ソリューションをお使いの Log Analytics ワークスペースに追加します。 さらに手動で構成する必要はありません。


## <a name="ad-replication-status-data-collection-details"></a>AD レプリケーションの状態データの収集に関する詳細
次の表は、AD レプリケーションの状態のデータの収集手段と、データの収集方法に関する各種情報をまとめたものです。

| プラットフォーム | 直接エージェント | SCOM エージェント | Azure Storage | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |5 日ごと |



## <a name="understanding-replication-errors"></a>レプリケーションの問題を理解する

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

AD Replication Status タイルには、現在発生しているレプリケーション エラーの数が表示されます。 **重大なレプリケーション エラー** は、Active Directory フォレストの [廃棄 (tombstone) の有効期間](/previous-versions/windows/it-pro/windows-server-2003/cc784932(v=ws.10))が 75% 以上のエラーです。

![AD レプリケーションの状態タイル](./media/ad-replication-status/oms-ad-replication-tile.png)

タイルをクリックすると、エラーの詳細を確認できます。
![AD レプリケーションの状態ダッシュボード](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>移行先サーバーと移行元サーバーの状態
こうした列では、レプリケーション エラーが発生している移行先サーバーと移行元サーバーの状態を示しています。 各ドメイン コントローラー名の後には、そのドメイン コントローラーで発生したレプリケーション エラーの数があります。

トラブルシューティングを実行する場合、移行先サーバーの観点から問題を解決した方が簡単な場合と、移行元サーバーの観点から問題を解決した方が簡単な場合があるので、移行先サーバーと移行元サーバーの両方のエラーが表示されています。

この例では、多数の移行先サーバーのエラーがほぼ同数である中、1 つの移行元サーバー (ADDC35) には他より多くのエラーがあることを確認できます。 ADDC35 に、レプリケーション パートナーへのデータ送信が失敗する原因となる、何らかの問題がある可能性があることがわかります。 その問題を ADDC35 で解決すると、移行先サーバー領域に表示されるエラーの多くを解決できる可能性があります。

### <a name="replication-error-types"></a>レプリケーション エラーの種類
この領域には、エンタープライズ全体で検出されるエラーの種類の情報が示されます。 各エラーには、一意の数値コードと、エラーの根本原因の特定に役立つメッセージがあります。

上部に示されるドーナツでは、環境内でどのエラーが多く、どのエラーが少ないかのイメージを示しています。

ここでは、同じレプリケーション エラーが複数のドメイン コントローラーで発生している場合にそれが明確になります。 その場合、1 つのドメイン コントローラーで解決策を検出または特定し、同じエラーが発生している他のドメイン コントローラーでそれを繰り返します。

### <a name="tombstone-lifetime"></a>廃棄 (tombstone) の有効期間
廃棄 (tombstone) の有効期間は、廃棄と呼ばれる削除されたオブジェクトが Active Directory データベースに保持される期間を決定します。 削除されたオブジェクトは、廃棄 (tombstone) の有効期間を過ぎると、ガベージ コレクション処理によって Active Directory データベースから自動的に削除されます。

既定の廃棄 (tombstone) の有効期間は、最新バージョンの Windows では 180 日間ですが、古いバージョンでは 60 日でした。これは、Active Directory 管理者が明示的に変更することができます。

廃棄 (tombstone) の有効期間が近づいている、またはそれが経過したレプリケーション エラーは把握しておく必要があります。 廃棄 (tombstone) の有効期間が経過しても 2 つのドメイン コントローラー間でレプリケーション エラーが続いている場合、基になるレプリケーション エラーを修正しても、その 2 つのドメイン コントローラー間でのレプリケーションは無効となります。

廃棄 (tombstone) の有効期間の領域を使用すると、これが発生する危険性ある無効のレプリケーションがある場所を特定できます。 **[TSL 100% 超]** カテゴリ内の各エラーは、少なくともフォレストの廃棄 (tombstone) の有効期間、移行元サーバーと移行先サーバーでレプリケーションが行われていないパーティションを表しています。

このような場合、レプリケーション エラーを修正するだけでは不十分です。 レプリケーションを再開するには、手動で調べて、残留オブジェクトを特定し、クリーンアップする必要があります。 場合によっては、ドメイン コントローラーの使用を停止する必要があります。

廃棄 (tombstone) の有効期間が経過しても続くレプリケーション エラーを特定するだけでなく、**TSL 50 ～ 75%** または **TSL 75 ～ 100%** カテゴリに分類されるすべてのエラーに注意を払う必要があります。

これらは、明らかに長引いている一時的なエラーではないため、介入して解決する必要があります。 さいわいにも、それらは廃棄 (tombstone) の有効期間には到達していません。 これらの問題を、廃棄 (tombstone) の有効期間に *達する前に* 迅速に解決した場合、最小限の介入でレプリケーションを再開できます。

前述のとおり、AD レプリケーションの状態ソリューションのダッシュボード タイルには、廃棄 (tombstone) の有効期間の 75% を経過した、環境内の *重大な* レプリケーション エラーが示されています (これには、TSL の 100% を超えるエラーも含みます)。 この数字は 0 に維持できるよう努めてください。

> [!NOTE]
> 廃棄 (tombstone) の有効期間のすべてのパーセンテージの計算は、Active Directory フォレストの実際の廃棄 (tombstone) の有効期限に基づいています。したがって廃棄 (tombstone) の有効期間の値をカスタマイズしている場合でも、それらのパーセンテージは正確であると信頼できます。
>
>

### <a name="ad-replication-status-details"></a>AD レプリケーションの状態の詳細
一覧のいずれかの任意の項目をクリックすると、ログ クエリにその詳細を表示できます。 その項目に関連するエラーのみが表示されるよう、結果はフィルター処理されます。 たとえば、 **[宛先サーバーの状態 (ADDC02)]** の最初のドメイン コントローラーをクリックすると、クエリ結果がフィルター処理されて、エラーと、宛先サーバーとして示されているそのドメイン コントローラーが表示されます。

![クエリ結果内の AD レプリケーションの状態エラー](./media/ad-replication-status/oms-ad-replication-search-details.png)

ここで、さらにフィルタリングを行い、ログ クエリなどの修正を行います。 Azure Monitor でのログ クエリの使用について詳しくは、「[Azure Monitor でログ データを分析する](../logs/log-query-overview.md)」をご覧ください。

**[HelpLink]** フィールドには、その特定のエラーの追加情報を含む TechNet ページの URL が表示されます。 このエラーをトラブルシューティングして修正するには、このリンクをブラウザー ウィンドウにコピーして貼り付け、情報を確認します。

**[エクスポート]** をクリックして、結果を Excel にエクスポートすることもできます。 データのエクスポートは、求めている方法でレプリケーション エラー データを視覚化するうえで役に立ちます。

![Excel にエクスポートされた AD レプリケーションの状態エラー](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>AD レプリケーションの状態の FAQ
**Q:AD レプリケーションの状態データはどのような頻度で更新されますか?**
A:この情報は 5 日おきに更新されます。

**Q:このデータの更新頻度を構成する方法がありますか?**
A:現時点ではありません。

**Q:レプリケーションの状態の確認用に、Log Analytics ワークスペースにすべてのドメイン コントローラーを追加する必要がありますか?**
A:いいえ。ドメイン コントローラーは 1 つのみ追加します。 Log Analytics ワークスペースにドメイン コントローラーが複数ある場合、それらのすべてのデータが Azure Monitor に送信されます。

**Q:Log Analytics ワークスペースに、ドメイン コントローラーを追加したくありません。それでも AD レプリケーションの状態ソリューションを使用できますか?**

A:はい。 レジストリ キー値を設定して、これを有効にできます。 「[非ドメイン コントローラーを有効にする](#enable-non-domain-controller)」をご覧ください。

**Q:データ収集を行うプロセスの名前は何ですか?**
A:AdvisorAssessment.exe です。

**Q:データの収集にはどれくらいの時間がかかりますか?**
A:Active Directory 環境の規模によって異なりますが、データの収集時間は通常は 15 分未満です。

**Q:どのような種類のデータが収集されますか?**
A:LDAP 経由でレプリケーションの情報は収集されます。

**Q:データが収集されるタイミングを構成する方法はありますか?**
A:現時点ではありません。

**Q:データを収集するには、どのようなアクセス許可が必要ですか?**
A:Active Directory への標準のアクセス許可で十分です。

## <a name="troubleshoot-data-collection-problems"></a>データの収集の問題のトラブルシューティング
AD レプリケーションの状態ソリューション パックでデータを収集する場合、Log Analytics ワークスペースには少なくとも 1 つのドメイン コントローラーが接続されている必要があります。 ドメイン コントローラーに接続するまで、**データをまだ収集している** ことを示すメッセージが表示されます。

1 つのドメイン コントローラーの接続に支援が必要な場合は、[Azure Monitor への Windows コンピューターの接続](../agents/om-agents.md)に関する記事をご覧ください。 または、ドメイン コントローラーが既存の System Center Operations Manager 環境に接続されている場合は、[Azure Monitor への System Center Operations Manager の接続](../agents/om-agents.md)に関する記事をご覧ください。

ドメイン コントローラーを Azure Monitor または System Center Operations Manager に直接接続しない場合は、「[非ドメイン コントローラーを有効にする](#enable-non-domain-controller)」をご覧ください。

## <a name="next-steps"></a>次のステップ
* Active Directory のレプリケーション状態の詳細データを見るには、[Azure Monitor のログ クエリ](../logs/log-query-overview.md)を使用します。

