---
title: Azure Sentinel クイック スタート - Azure Sentinel プレビューの概要 | Microsoft Docs
description: Azure Sentinel クイック スタート - Azure Sentinel の概要
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5a4ae93c-d648-41fb-8fb8-96a025d2f73e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: 75bfdce02313ab7e799f6535f247407c239653b8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619805"
---
# <a name="quickstart-get-started-with-azure-sentinel-preview"></a>クイック スタート:Azure Sentinel プレビューの概要

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


このクイック スタートでは、Azure Sentinel を使用して、環境全体で発生していることをすばやく表示および監視できるようにする方法について説明します。 データ ソースを Azure Sentinel に接続すると、すぐにデータを視覚化して分析できるため、接続されているすべてのデータ ソースで発生していることを把握できます。 Azure Sentinel には、Azure で既に使用できるツールをフルに活用できるダッシュボードと、ログとクエリの分析機能を提供するために組み込まれているテーブルとグラフが用意されています。 組み込みのダッシュボードを使用するか、最初から、または既存のダッシュボードに基づいて新しいダッシュボードを簡単に作成できます。 

## <a name="get-visualization"></a>視覚化する

環境で発生していること視覚化して分析するには、まず概要ダッシュボードを見て、組織のセキュリティ体制を把握します。 このようなタイルの各要素をクリックして、それらが作成された元の生データにドリルダウンすることができます。 ノイズを減らし、確認および調査する必要があるアラートの数を最小限に抑えるために、Azure Sentinel は融合手法を使用してアラートをケースに関連付けます。 **ケース**は、調査して解決できる対応可能なインシデントをまとめた、関連するアラートのグループです。

- Azure portal で Azure Sentinel を選択してから、監視するワークスペースを選択します。

  ![Azure Sentinel の概要](./media/qs-get-visibility/overview.png)

- 上部のツール バーには、選択した期間中に取得したイベントの数が表示され、過去 24 時間のイベントと比較されます。 ツール バーには、このようなイベントからトリガーされたアラートが表示されます (過去 24 時間の変化を表す小さい数字)。また、そのようなイベントの開いている数、進行中の数、閉じた数も表示されます。 イベント数の大幅な増減がないことを確認してください。 急落がある場合は、接続が Azure Sentinel へのレポートを停止した可能性があります。 増加がある場合は、何か疑わしいことが起こった可能性があります。 新しいアラートがあるかどうかを確認してください。

   ![Azure Sentinel のじょうご](./media/qs-get-visibility/funnel.png)

概要ページの本文から、ワークスペースのセキュリティ状態を一目で確認できます。

- **イベントとアラート (時系列)** :イベント数と、それらのイベントから作成されたアラートの数が一覧表示されます。 通常とは異なる急上昇がある場合は、アラートが表示されるはずです。イベントの急上昇があってもアラートが表示されないという通常とは異なることが発生した場合は、問題が発生してる可能性があります。

- **悪意のある可能性があるイベント**:悪意のあることがわかっているソースからのトラフィックが検出されると、Azure Sentinel はマップ上で警告します。 オレンジ色が表示される場合は受信トラフィックです。誰かが既知の悪意のある IP アドレスから組織にアクセスしようとしています。 送信 (赤) アクティビティが表示される場合は、ネットワークからのデータが組織から既知の悪意のある IP アドレスにストリーミングされていることを意味します。

   ![Azure Sentinel マップ](./media/qs-get-visibility/map.png)


- **最近のケース**:最近のケース、その重大度、そのケースに関連するアラートの数を表示します。 特定の種類のアラートで突然のピークが発生した場合は、現在実行中のアクティブな攻撃があることを意味している可能性があります。 たとえば、Azure ATP からの 20 回の Pass-the-hash イベントの突然のピークが発生した場合、現在、誰かが攻撃しようとしている可能性があります。

- **データ ソースの異常**:Microsoft のデータ アナリストは、データ ソースのデータから常に異常を探すモデルを作成しました。 異常がなければ、何も表示されません。 異常が検出された場合は、詳しく調べて発生したことを確認してください。 たとえば、Azure アクティビティの急上昇をクリックします。 **[グラフ]** をクリックして急上昇が発生した時期を確認し、その期間に発生したアクティビティをフィルター処理して、急上昇の原因を確認できます。

   ![Azure Sentinel マップ](./media/qs-get-visibility/anomolies.png)

## 組み込みのダッシュボードを使用する<a name="dashboards"></a>

組み込みのダッシュボードには、接続されたデータ ソースから統合されたデータが表示されるので、そのようなサービスで生成されたイベントを詳しく調べることができます。 組み込みのダッシュボードには、Azure ID、Azure アクティビティ イベント、およびオンプレミスのデータが表示されます。たとえば、サーバーからの Windows イベントのデータ、ファースト パーティ アラートのデータ、ファイアウォール トラフィック ログ、Office 365、Windows イベントに基づく安全ではないプロトコルを含むサード パーティからのデータです。

1. **[設定]** で、 **[ダッシュボード]** を選択します。 **[インストール済み]** には、インストールされているすべてのダッシュボードが表示されます。 **[すべて]** には、インストールできる組み込みのダッシュボードのギャラリー全体が表示されます。 
2. 特定のダッシュボードを検索して、各オファーの一覧全体と説明を確認してください。 
3. Azure AD を使用する場合、Azure Sentinel を使い始めるには、少なくとも次のダッシュボードをインストールすることをお勧めします。
   - **Azure AD**:次のいずれかまたは両方を使用します。
       - **Azure AD サインイン**は、時間の経過と共にサインインを分析し、異常があるかどうかを表示します。 このダッシュボードには、アプリケーション、デバイス、および場所ごとに失敗したサインインが表示されるため、異常が発生した場合は一目でわかります。 複数の失敗したサインインに注意してください。 
       - **Azure AD 監査ログ**では、ユーザーの変更 (追加、削除など)、グループの作成、変更などの管理アクティビティを分析します。  

   - ファイアウォール用のダッシュボードを追加します。 たとえば、Palo Alto ダッシュボードを追加します。 このダッシュボードでは、ファイアウォールのトラフィックを分析し、ファイアウォールのデータと脅威イベントの間の相関関係を表示し、エンティティ全体の疑わしいイベントを強調表示します。 ダッシュボードには、トラフィックの傾向に関する情報が表示され、結果をドリル ダウンしてフィルター処理することができます。 

      ![Pal Alto ダッシュボード](./media/qs-get-visibility/palo-alto-week-query.png)


メイン クエリ ![ボタン](./media/qs-get-visibility/edit-query-button.png)を編集してダッシュボードをカスタマイズできます。 ボタン ![ボタン](./media/qs-get-visibility/go-to-la-button.png) をクリックして [Log Analytics に移動し、そこでクエリを編集](../azure-monitor/log-query/get-started-portal.md)できます。また、省略記号 (...) を選択し、 **[タイル データのカスタマイズ]** を選択して、メイン タイム フィルターを編集したり、ダッシュボードから特定のタイルを削除したりすることができます。

クエリの使用方法の詳細については、「[チュートリアル:Log Analytics でのデータの視覚化](../azure-monitor/learn/tutorial-logs-dashboards.md)」を参照してください

### <a name="add-a-new-tile"></a>新しいタイルを追加する

新しいタイルを追加する場合は、既存のダッシュボード (自分で作成したダッシュボードまたは Azure Sentinel の組み込みのダッシュボード) に追加できます。 
1. Log Analytics で、「[チュートリアル:Log Analytics でのデータの視覚化](../azure-monitor/learn/tutorial-logs-dashboards.md)」の手順に従ってタイルを作成します。 
2. タイルが作成されたら、 **[ピン留め]** でタイルを表示するダッシュボードを選択します。

## <a name="create-new-dashboards"></a>ダッシュボードの新規作成
新しいダッシュボードは、最初から作成するか、組み込みのダッシュボードを新しいダッシュボードの基礎として使用することができます。

1. 最初から新しいダッシュボードを作成するには、 **[ダッシュボード]** 、 **[+ 新しいダッシュボード]** の順に選択します。
2. ダッシュボードを作成したサブスクリプションを選択し、わかりやすい名前を付けます。 各ダッシュボードは他のダッシュボードと同様に Azure リソースであり、アクセスできるユーザーを定義および制限するためにロールを割り当てることができます (RBAC)。 
3. ダッシュボードに表示して視覚化をピン留めできるようにするには、共有する必要があります。 **[共有]** 、 **[ユーザーの管理]** の順にクリックします。 
 
1. 他の Azure リソースと同様に、 **[Check access]\(アクセスの確認\)** および **[ロールの割り当て]** を使用します。 詳細については、「[RBAC を使用した Azure ダッシュボードの共有](../azure-portal/azure-portal-dashboard-share-access.md)」を参照してください。


## <a name="new-dashboard-examples"></a>新しいダッシュボードの例

次のサンプル クエリを使用すると、数週間にわたるトラフィックの傾向を比較できます。 クエリを実行するデバイスの製造元とデータ ソースを簡単に切り替えることができます。 この例では Windows の SecurityEvent を使用していますが、他のファイアウォールの AzureActivity または CommonSecurityLog に対して実行するために切り替えることができます。

     |where DeviceVendor = = "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


複数のソースからのデータを組み込んだクエリを作成することをお勧めします。 作成されたばかりの新しいユーザーの Azure Active Directory 監査ログを確認し、Azure ログをチェックして作成後 24 時間以内にユーザーがロールの割り当てを変更し始めたかどうかを確認するクエリを作成できます。 その疑わしいアクティビティは、このダッシュボードに表示されます。

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

データを見るユーザーのロール必要なものに基づいて、さまざまなダッシュボードを作成できます。 たとえば、ファイアウォール データを含むネットワーク管理者用のダッシュボードを作成できます。 また、ダッシュボードを確認する頻度、毎日確認することがあるかどうか、1 時間に 1 回確認する必要がある項目などに基づいてダッシュボードを作成することもできます。たとえば、Azure AD のサインを毎時確認し、異常を検索することができます。 

## <a name="create-new-detections"></a>新しい検出を作成する

[Azure Sentinel に接続したデータ ソース](connect-data-sources.md)に対し、組織内の脅威を調査するための検出を生成します。

新しい検出を作成する際は、Microsoft のセキュリティ研究員によって作成されたビルトインの検出を活用しましょう。接続先のデータ ソースに合わせてカスタマイズされています。

1. [GitHub コミュニティ](https://github.com/Azure/Azure-Sentinel/tree/master/Detections)で **Detections** フォルダーに移動し、対象となるフォルダーを選択します。
   ![対象となるフォルダー](./media/qs-get-visibility/detection-folders.png)
 
3.  **[分析]** タブに移動して **[追加]** を選択します。
   ![Log Analytics でルールを作成する](./media/qs-get-visibility/query-params.png)

3.  そのルールにすべてのパラメーターをコピーして、 **[作成]** をクリックします。
   ![アラート ルールを作成する](./media/qs-get-visibility/create-alert-rule.png)

 
## <a name="next-steps"></a>次の手順
このクイック スタートでは、Azure Sentinel を使い始める方法について説明しました。 [脅威の検出方法](tutorial-detect-threats.md)のチュートリアルに進んでください。
> [!div class="nextstepaction"]
> [脅威を検出](tutorial-detect-threats.md)して脅威に対する対応を自動化します。

