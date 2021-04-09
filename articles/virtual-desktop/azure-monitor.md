---
title: Windows Virtual Desktop 向けの Monitor (プレビュー) を使用する - Azure
description: Windows Virtual Desktop 向けの Azure Monitor を使用する方法
author: Heidilohr
ms.topic: how-to
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e9da1071686dafa003a5a49d0864b77644493344
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594461"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment-preview"></a>Windows Virtual Desktop 向けの Azure Monitor を使用してデプロイを監視する (プレビュー)

>[!IMPORTANT]
>Windows Virtual Desktop 向けの Azure Monitor は現在、パブリック プレビュー段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Windows Virtual Desktop 向けの Azure Monitor (プレビュー) は、Azure Monitor のブックに基づいて構築されたダッシュボードで、IT プロフェッショナルが Windows Virtual Desktop 環境を理解するのに役立ちます。 このトピックでは、Windows Virtual Desktop 環境を監視するために Windows Virtual Desktop 向けの Azure Monitor を設定する方法について説明します。

## <a name="requirements"></a>必要条件

Windows Virtual Desktop 向けの Azure Monitor の使用を開始する前に、次の項目を設定する必要があります。

- 監視するすべての Windows Virtual Desktop 環境が、Azure Resource Manager と互換性のある最新リリースの Windows Virtual Desktop に基づいている必要がある。

- 少なくとも 1 つの Log Analytics ワークスペースが構成されている。

- Log Analytics ワークスペースで、次の項目のデータ収集を有効にする。
    - 必要なパフォーマンス カウンター
    - Windows Virtual Desktop 向けの Azure Monitor で使用されるパフォーマンス カウンターまたはイベント
    - 監視する環境内のすべてのオブジェクトに対する診断ツールのデータ
    - 監視する環境内の仮想マシン (VM)

環境に合わせて Windows Virtual Desktop 向けの Azure Monitor を監視するすべてのユーザーには、次の読み取りアクセス許可も必要になります。

- 環境のリソースが配置されているリソース グループへの読み取りアクセス

- 環境のセッション ホストが配置されているリソース グループに対する読み取りアクセス

>[!NOTE]
> 読み取りアクセスでは、管理者はデータの表示のみを行えます。 Windows Virtual Desktop ポータルでリソースを管理するには、さまざまなアクセス許可が必要です。

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Windows Virtual Desktop 向けの Azure Monitor を開く

Windows Virtual Desktop 向けの Azure Monitor は、次のいずれかの方法で開くことができます。

- [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks) にアクセスします。

- Azure portal で **[Windows Virtual Desktop]** を検索して選択し、 **[Insights]** を選択します。

- Azure portal で **[Azure Monitor]** を検索して選択します。 **[Insights]** で **[Insights ハブ]** を選択し、 **[その他]** で **[Windows Virtual Desktop]** を選択して、[Azure Monitor] ページでダッシュボードを開きます。

Windows Virtual Desktop 向けの Azure Monitor を開いたら、監視する環境に基づいて、 **[サブスクリプション]** 、 **[リソース グループ]** 、 **[ホスト プール]** 、 **[時間範囲]** というラベルの付いたチェック ボックスを 1 つ以上選択します。

>[!NOTE]
>現在、Windows Virtual Desktop では、一度に 1 つのサブスクリプション、リソース グループ、ホスト プールの監視のみがサポートされています。 監視する環境が見つからない場合は既知の機能の要求と問題に関する[トラブルシューティング ドキュメント](troubleshoot-azure-monitor.md)をご覧ください。

## <a name="set-up-with-the-configuration-workbook"></a>構成ブックを使用して設定する

Windows Virtual Desktop 向けの Azure Monitor を初めて開くときは、Windows Virtual Desktop リソース用に Azure Monitor を構成する必要があります。 配置を構成するには、次の手順に従います。

1. Azure portal でブックを開きます。
2. **[Open the configuration workbook]\(構成ブックを開く\)** を選択します。

構成ブックによって監視環境が設定され、設定プロセスの完了後に構成を確認できます。 ダッシュボード内の項目が正しく表示されない場合、または追加のデータ ポイントを必要とする更新プログラムが製品グループによって発行される場合は、構成を確認することが重要です。

## <a name="host-pool-diagnostic-settings"></a>ホスト プールの診断設定

この機能をサポートする Windows Virtual Desktop 環境内のすべてのオブジェクトで、Azure Monitor 診断設定を有効にする必要があります。

1. [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks) で Windows Virtual Desktop 向けの Azure Monitor を開き、 **[Configuration Workbook]\(構成ブック\)** を選択します。

2. **[サブスクリプション]** 、 **[リソース グループ]** 、および **[ホスト プール]** で監視する環境を選択します。

3. **[Host Pool Diagnostic Settings]\(ホスト プールの診断設定\)** で、ホスト プールに対して Windows Virtual Desktop 診断が有効になっているかどうかを確認します。 有効になっていない場合は、"選択されたホスト プールの既存の診断構成が見つかりませんでした" というエラー メッセージが表示されます。 
   
   次のテーブルを有効にする必要があります。

    - Checkpoint
    - エラー
    - 管理
    - Connection
    - HostRegistration

    >[!NOTE]
    > エラー メッセージが表示されない場合、手順 4. を実行する必要はありません。

4. **[Configure host pool]\(ホスト プールを構成する\)** を選択します。

5. **[デプロイ]** を選択します。

6. ブックを更新する。

Windows Virtual Desktop 環境のすべてのオブジェクトに対して診断を有効にする方法、または Log Analytics ワークスペースにアクセスする方法の詳細については、[Log Analytics への Windows Virtual Desktop 診断の送信](diagnostics-log-analytics.md)に関するページをご覧ください。

## <a name="configure-log-analytics"></a>Log Analytics の構成

Windows Virtual Desktop 向けの Azure Monitor の使用を開始するには、監視する環境からデータを収集してブックに提供するために、少なくとも 1 つの Log Analytics ワークスペースが必要です。 それが既に設定されている場合は、「[パフォーマンス カウンターを設定する](#set-up-performance-counters)」に進みます。 Windows Virtual Desktop 環境を含む Azure サブスクリプションに対して新しい Log Analytics ワークスペースを設定するには、「[Azure portal で Log Analytics ワークスペースを作成する](../azure-monitor/logs/quick-create-workspace.md)」をご覧ください。

>[!NOTE]
>Log Analytics に対する通常のデータ ストレージ料金が適用されます。 まず、従量課金制モデルを選択し、より多くのデータを取得するよう、デプロイのスケーリングするときに調整することをお勧めします。 詳細については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」をご覧ください。

### <a name="set-up-performance-counters"></a>パフォーマンス カウンターをセットアップする

Log Analytics ワークスペースの対応するサンプル間隔で、コレクションの特定のパフォーマンス カウンターを有効にする必要があります。 これらのパフォーマンス カウンターは、Windows Virtual Desktop を監視するために必要な唯一のカウンターです。 コストを節約するために、他のすべてを無効にできます。

既にパフォーマンス カウンターが有効になっていて、それらを削除する場合は、「[パフォーマンス カウンターの構成](../azure-monitor/agents/data-sources-performance-counters.md)」の手順に従ってパフォーマンス カウンターを再構成します。 この記事では、カウンターを追加する方法について説明していますが、同じ場所でカウンターを削除することもできます。

パフォーマンス カウンターをまだ設定していない場合に、Windows Virtual Desktop 向けの Azure Monitor に対してそれを構成する方法を次に示します。

1. [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks) にアクセスし、ウィンドウの下部にある **[Configuration Workbook]\(構成ブック\)** を選択します。

2. **[Log Analytics Configuration]\(Log Analytics の構成\)** で、サブスクリプションに対して設定したワークスペースを選択します。

3. **[Workspace performance counters]\(ワークスペースのパフォーマンス カウンター\)** に、監視に必要なカウンターのリストが表示されます。 リストの右側で、 **[Missing counters]\(見つからないカウンター\)** リストの項目を確認して、ワークスペースの監視を開始するために必要なカウンターを有効にします。

4. **[パフォーマンス カウンターを構成する]** を選択します。

5. **[構成の適用]** を選択します。

6. 構成ブックを更新し、環境の設定を続行します。

また、サービスが更新され、新しい監視ツールが必要になるたびに、初期構成後に新しいパフォーマンス カウンターを追加することもできます。 必要なすべてのカウンターが有効になっていることを確認するには、 **[Missing counters]\(見つからないカウンター\)** リストでそれらを選択します。

>[!NOTE]
>入力遅延のパフォーマンス カウンターは、Windows 10 RS5 以降または Windows Server 2019 以降とのみ互換性があります。

コレクションに対して既に有効になっていないパフォーマンス カウンターを手動で追加する方法の詳細については、「[パフォーマンス カウンターの構成](../azure-monitor/agents/data-sources-performance-counters.md)」をご覧ください。

### <a name="set-up-windows-events"></a>Windows イベントを設定する

次に、Log Analytics ワークスペースでコレクションの特定の Windows イベントを有効にする必要があります。 このセクションで説明するイベントは、Windows Virtual Desktop 向けの Azure Monitor で必要な唯一のイベントです。 コストを節約するために、他のすべてを無効にできます。

Windows イベントを設定するには、次の手順に従います。

1. Windows イベントが既に有効になっていて、それを削除する場合は、構成ブックを使用して監視に必要な設定を有効にする前に、不要なイベントを削除します。

2. [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks) で Windows Virtual Desktop 向けの Azure Monitor にアクセスし、ウィンドウの下部にある **[Configuration Workbook]\(構成ブック\)** を選択します。

3. **[Windows Events configuration]\(Windows イベントの構成\)** に、監視に必要な Windows イベントのリストが表示されます。 このリストの右側には、 **[Missing events]\(見つからないイベント\)** リストが表示されます。ここには、ワークスペースに対して現在有効になっていない必要なイベント名とイベントの種類が表示されます。 後で使用するために、これらの各名前を記録しておきます。

4. **[Open Workspaces Configuration]\(ワークスペースの構成を開く\)** を選択します。

5. **[データ]** を選択します。

6. **[Windows イベント ログ]** を選択します。

7. 手順 3 の見つからないイベント名と、それぞれに必要なイベントの種類を追加します。

8. 構成ブックを更新し、環境の設定を続行します。

監視ツールの更新で新しいイベントを有効にする必要がある場合は、初期構成の後に新しい Windows イベントを追加できます。 必要なイベントがすべて有効になっていることを確認するには、 **[Missing events]\(見つからないイベント\)** リストに戻り、表示されている見つからないイベントを有効にします。

## <a name="install-the-log-analytics-agent-on-all-hosts"></a>すべてのホストに Log Analytics エージェントをインストールする

最後に、ホストから、選択したワークスペースにデータを送信するために、ホスト プール内のすべてのホストに Log Analytics エージェントをインストールする必要があります。

Log Analytics エージェントをインストールするには:

1. [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks) で Windows Virtual Desktop 向けの Azure Monitor にアクセスし、ウィンドウの下部にある **[Configuration Workbook]\(構成ブック\)** を選択します。

2. ホスト プール内のすべてのホストに対して Log Analytics が構成されていない場合は、Log Analytics 構成セクションの下部に "ホスト プール内の一部のホストで、選択した Log Analytics ワークスペースにデータが送信されていません" というエラー メッセージが表示されます。 **[Add hosts to workspace]\(ワークスペースにホストを追加する\)** を選択して、選択したホストを追加します。 エラー メッセージが表示されない場合は、ここで終了します。

3. 構成ブックを最新の状態に更新します。

>[!NOTE]
>Log Analytics 拡張機能をインストールするには、ホスト マシンが実行されている必要があります。 ホストで自動デプロイが失敗した場合は、いつでもホストに拡張機能を手動でインストールできます。 拡張機能を手動でインストールする方法については、「[Windows 用の Log Analytics 仮想マシン拡張機能](../virtual-machines/extensions/oms-windows.md)」をご覧ください。

## <a name="optional-configure-alerts"></a>省略可能: アラートを構成する

選択したサブスクリプション内で重大な Azure Monitor アラートが発生した場合に通知するように、Windows Virtual Desktop 向けの Azure Monitor を構成できます。 これを行うには、「[Azure Monitor アラートでイベントに応答する](../azure-monitor/alerts/tutorial-response.md)」の手順に従います。

## <a name="diagnostic-and-usage-data"></a>診断と使用状況データ

Microsoft は、お客様による Azure Monitor サービスの使用を通して、使用状況とパフォーマンス データを自動的に収集します。 Microsoft はこのデータを使用して、サービスの品質、セキュリティ、および整合性の向上に努めています。

正確で効率的なトラブルシューティング機能を提供するために、収集されたデータには、ポータル セッション ID、Azure Active Directory ユーザー ID、およびイベントが発生したポータル タブの名前が含まれます。 名前や住所などの連絡先情報は収集されません。

データの収集と使用の詳細については、「[Microsoft オンライン サービスのプライバシーに関する声明](https://privacy.microsoft.com/privacystatement)」を参照してください。

>[!NOTE]
>サービスによって収集された個人データを表示または削除する方法については、[GDPR のための Azure データ サブジェクト要求](/microsoft-365/compliance/gdpr-dsr-azure)に関するページをご覧ください。 GDPR の詳細については、[Service Trust Portal の GDPR に関するセクション](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)をご覧ください。

## <a name="next-steps"></a>次の手順

Windows Virtual Desktop Azure portal を構成したので、次のようなリソースが役に立ちます。

- Windows Virtual Desktop 向けの Azure Monitor に関連する用語と概念の詳細については、[用語集](azure-monitor-glossary.md)をご覧ください。
- 問題が発生した場合は、[トラブルシューティング ガイド](troubleshoot-azure-monitor.md)をご覧ください。