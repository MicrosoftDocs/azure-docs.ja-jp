---
title: Azure Monitor for Storage で Azure Storage サービスを監視する | Microsoft Docs
description: この記事では、ストレージ管理者が Azure Storage アカウントのパフォーマンスと使用状況の問題をすばやく把握できる Azure Monitor for Storage の機能について説明します。
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 05/11/2020
ms.openlocfilehash: 0baaca8df8865061b51f1629da14c3b18a527047
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708069"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage"></a>Azure Monitor for Storage でストレージ サービスを監視する

Azure Monitor for Storage では、Azure Storage サービスのパフォーマンス、容量、可用性の統合されたビューが提供され、Azure Storage アカウントを包括的に監視できます。 ストレージの容量とパフォーマンスは、ストレージ アカウントから直接表示する方法と、Azure Monitor からストレージ アカウントのグループ全体を表示する方法の 2 つの方法で観察できます。 

この記事は、Azure Monitor for Storage のエクスペリエンスを理解するのに役立ちます。規模に合わせたストレージ アカウントの正常性とパフォーマンスについての実用的な知識を引き出すことができ、ホットスポットに注目し、待ち時間、スロットリング、可用性の問題を診断できる機能もあります。

## <a name="introduction-to-azure-monitor-for-storage"></a>Azure Monitor for Storage の概要

エクスペリエンスについて詳しく調べる前に、情報が提供および視覚化される方法を理解する必要があります。 ストレージ機能をストレージ アカウントから直接選択するか、Azure Monitor から選択するかにかかわらず、Azure Monitor for Storage では一貫したエクスペリエンスが得られます。 

次のものの組み合わせが提供されます。

* **規模に応じたパースペクティブ**: ストレージ サービスまたは API 操作の正常性に基づいた可用性、ストレージ サービスが受け取る要求の合計数を示す使用状況、ストレージ サービスまたは API 操作の種類で要求の処理にかかる平均時間を示す待機時間のスナップショット ビューが示されます。 BLOB、ファイル、テーブル、キューごとに容量を表示することもできます。

* **ドリルダウン分析**: 特定のストレージ アカウントについて、カテゴリ (可用性、パフォーマンス、障害、容量) ごとに問題を診断したり詳細な分析を実行したりするのに役立ちます。 これらのオプションのいずれかを選択すると、メトリックの詳細が表示されます。  

* **カスタマイズ可能**: 表示するメトリックの変更したり、制限に合わせたしきい値の変更または設定、独自のブックとして保存することができます。 ブック内のグラフは、Azure ダッシュボードにピン留めできます。  

この機能を使用するために有効にしたり構成したりする必要があるものはなく、ストレージ メトリックはストレージ アカウントから既定で収集されます。 Azure Storage で使用できるメトリックについて詳しく理解したい場合は、[Azure Storage メトリック](../../storage/blobs/monitor-blob-storage.md)に関する記事で Azure Storage のメトリックの説明と定義を確認してください。

>[!NOTE]
>この機能へのアクセスに対して料金はかからず、構成または有効にした Azure Monitor の基本機能に対してのみ課金されます。[Azure Monitor の価格の詳細](https://azure.microsoft.com/pricing/details/monitor/)に関するページをご覧ください。


## <a name="view-from-azure-monitor"></a>Azure Monitor から表示する

Azure Monitor では、ご利用のサブスクリプション内の複数のストレージ アカウントのトランザクション、待機時間、容量の詳細を表示でき、パフォーマンス、容量の問題、および障害を特定するのに役立ちます。

所有するすべてのサブスクリプションのストレージ アカウントの使用状況と可用性を確認するには、次の手順のようにします。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. Azure portal の左側のペインで **[モニター]** を選択し、 **[分析情報]** セクションの **[ストレージ アカウント]** を選択します。

    ![複数のストレージ アカウントのビュー](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>[概要] ブック

選択したサブスクリプションの **[概要]** ブックでは、サブスクリプション内でグループ化されている最大 5 個のストレージ アカウントについて、ストレージ メトリックとサービスの可用性の状態がテーブルに対話的に表示されます。 次のドロップダウン リストから選択するオプションに基づいて、結果をフィルター処理できます。

* **[サブスクリプション]** - ストレージ アカウントを持つサブスクリプションのみが表示されます。  

* **[ストレージ アカウント]** - 既定では、5 個のストレージ アカウントがあらかじめ選択されています。 スコープ セレクターですべてまたは複数のストレージ アカウントを選択すると、最大 200 個のストレージ アカウントが返されます。 たとえば、選択した 3 つのサブスクリプションに全部で 573 個のストレージ アカウントがある場合は、200 個のアカウントのみが表示されます。 

* **[時間の範囲]** - 既定では、選択結果に応じて過去 4 時間の情報が表示されます。

ドロップダウン リストのカウンター タイルには、サブスクリプション内のストレージ アカウントの総数がロールアップされ、選択した合計数が反映されます。 ブックの列には、条件に基づく色分け (ヒートマップ) が示され、トランザクションのメトリックやエラーが報告されます。 最も濃い色は最大値で、薄い色は最小値に基づいています。 エラー ベースの列の値は赤で、メトリック ベースの列の値は青で示されます。

**[可用性]** 、 **[E2E Latency]\(E2E 待機時間\)** 、 **[Server Latency]\(サーバー待機時間\)** 、 **[transaction error type/Errors]\(<トランザクション エラーの種類>/エラー\)** の列で値を選択すると、ストレージ アカウントに対して選択した列と一致する、特定の種類のストレージ メトリックに合わせて調整されたレポートに移動します。 各カテゴリのブックの詳細については、後述の「[詳細なストレージ ブック](#detailed-storage-workbooks)」セクションを参照してください。 

>[!NOTE]
>レポートに表示できるエラーの詳細については、[応答の種類のスキーマ](../../storage/blobs/monitor-blob-storage-reference.md#metrics-dimensions)に関する記事で、**ServerOtherError**、**ClientOtherError**、**ClientThrottlingError** などの応答の種類を参照してください。 選択したストレージ アカウントに応じて、3 種類より多くのエラーが報告される場合、他のすべてのエラーは **[その他]** のカテゴリの下に表示されます。

既定の **[可用性]** のしきい値は次のとおりです。

* 警告 - 99%
* 重大 - 90%

観察結果または要件に基づいて可用性のしきい値を設定するには、「[可用性のしきい値を変更する](#modify-the-availability-threshold)」を参照してください。 

### <a name="capacity-workbook"></a>[容量] ブック

ページの上部にある **[容量]** を選択すると、 **[容量]** ブックが開きます。 そこには、アカウントで使用されているストレージの合計量と、アカウント内の各データ サービスによって使用されている容量が示され、過剰または過少に使用されているストレージを特定するのに役立ちます。

![複数のストレージ アカウントの [容量] ブック](./media/storage-insights-overview/storage-account-capacity-02.png) 

ブックの列には、条件に基づく色分け (ヒートマップ) が示され、容量のメトリックは青色の値で報告されます。 最も濃い色は最大値で、薄い色は最小値に基づいています。

ブック内のいずれかの列の値を選択すると、ストレージ アカウントの **[容量]** ブックにドリルダウンします。 ドリルダウン レポートの詳細については、後述の「[詳細なストレージ ブック](#detailed-storage-workbooks)」セクションを参照してください。 

## <a name="view-from-a-storage-account"></a>ストレージ アカウントから表示する

ストレージ アカウントから VM インサイトに直接アクセスするには、次のようにします。

1. Azure portal で [ストレージ アカウント] を選択します。

2. 一覧からストレージ アカウントを選択します。 [監視] セクションで、[分析情報] を選択します。

    ![ストレージ アカウントの [概要] ブック ページを示すスクリーンショット。](./media/storage-insights-overview/storage-account-direct-overview-01.png)

ストレージアカウントの **[概要]** ブックには、複数のストレージ パフォーマンス メトリックが表示され、次の情報をすばやく評価するのに役立ちます。

* ストレージ サービスの正常性。 **[概要]** 列に表示され、自分では制御できない問題によってデプロイ先のリージョンのストレージ サービスが影響を受けているかどうかをすぐに確認できます。

* 対話型のパフォーマンス グラフ。ストレージ容量、可用性、トランザクション、待機時間に関連する最も重要な詳細情報が示されます。  

* メトリック タイルと状態タイル。サービスの可用性、ストレージ サービスに対するトランザクションの合計数、E2E 待機時間、サーバー待機時間が明示されます。

**[Failures]\(エラー数\)** 、 **[パフォーマンス]** 、 **[可用性]** 、 **[容量]** のいずれかのボタンを選択すると、それぞれのブックが開きます。 

![選択されたストレージ アカウントの [概要] ページ](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>詳細なストレージ ブック

複数のストレージ アカウントの **[概要]** ブックから **[可用性]** 、 **[E2E Latency]\(E2E 待機時間\)** 、 **[Server Latency]\(サーバー待機時間\)** 、 **[transaction error type/Errors]\(<トランザクション エラーの種類>/エラー\)** の列の値を選択した場合でも、または特定のストレージ アカウントの **[概要]** ブックで **[Failures]\(エラー数\)** 、 **[パフォーマンス]** 、 **[可用性]** 、 **[容量]** のいずれかのボタンを選択した場合でも、そのカテゴリに合わせて調整された対話形式のストレージ関連情報のセットがそれぞれ提供されます。  

* **[可用性]** から、 **[可用性]** ブックを開きます。 Azure Storage サービスの現在の正常性状態、ストレージ アカウントで定義されたデータ サービスごとに分類された各オブジェクトの使用可能な正常性状態と選択した時間範囲を表すトレンド ラインが示されたテーブル、およびアカウント内の各データ サービスの可用性トレンド グラフが表示されます。  

    ![可用性レポートの例](./media/storage-insights-overview/storage-account-availability-01.png)

* **[E2E Latency]\(E2E 待機時間\)** と **[Server Latency]\(サーバー待機時間\)** から、 **[パフォーマンス]** ブックを開きます。 E2E 待機時間とサーバー待機時間を示すロールアップ状態タイル、E2E 待機時間とサーバー待機時間のパフォーマンス グラフ、およびストレージ アカウントで定義されたデータ サービスによって分類された API による成功した呼び出しの待機時間の詳細を示すテーブルが含まれます。

    ![パフォーマンス レポートの例](./media/storage-insights-overview/storage-account-performance-01.png)

* グリッドに表示されているエラー カテゴリのいずれかを選択して、 **[Failure]\(エラー\)** ブックを開きます。 レポートには、説明されているものと成功した要求を除く他のすべてのクライアント側エラーのメトリック タイル、ClientOtherError 属性に固有のトランザクション **応答の種類** ディメンション メトリックのパフォーマンス グラフ、および **[Transactions by API name]\(API 名別トランザクション\)** と **[Transactions by Response type]\(応答の種類別トランザクション\)** の 2 つのテーブルが表示されます。

   ![エラー レポートの例](./media/storage-insights-overview/storage-account-failures-01.png)

* **[容量]** から、 **[容量]** ブックを開きます。 アカウント内の各ストレージ データ オブジェクトに使用されているストレージの総量がタイルとグラフで示され、アカウントに格納されているデータ オブジェクトの数が表示されます。  

    ![選択されたストレージ アカウントの [容量] ページ](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>ピン留めとエクスポート

セクションの右上にある画びょうアイコンを選択すると、メトリックのどのセクションでも Azure ダッシュボードにピン留めできます。

![メトリック セクションのダッシュボードへのピン留めの例](./media/storage-insights-overview/workbook-pin-example.png)

複数サブスクリプションおよびストレージ アカウントの **[概要]** または **[容量]** ブックでは、画びょうアイコンの右側にある下矢印アイコンを選択すると、結果を Excel 形式でエクスポートできます。

![ブックのグリッド結果のエクスポートの例](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage"></a>Azure Monitor for Storage をカスタマイズする

このセクションでは、データ分析のニーズに合わせてブックを編集してカスタマイズする一般的なシナリオについて説明します。

* 特定のサブスクリプションまたはストレージ アカウントが常に選択されるようにブックのスコープを設定する
* グリッドでメトリックを変更する
* 可用性のしきい値を変更する
* 色の表示を変更する

カスタマイズはカスタム ブックに保存されるため、発行されたブックで既定の構成が上書きされることはありません。 ブックは、リソース グループ内の、ユーザー個人の **[個人用レポート]** セクション、またはリソース グループにアクセスできるユーザーであればだれでもアクセスできる **[共有レポート]** セクションのいずれかに保存されます。 カスタム ブックを保存した後は、ブック ギャラリーに移動して起動する必要があります。

![コマンド バーからブック ギャラリーを起動する](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>サブスクリプションまたはストレージ アカウントの指定

複数サブスクリプションおよびストレージ アカウントの **[概要]** または **[容量]** ブックを、実行のたびに特定のサブスクリプションまたはストレージ アカウントを対象とするように構成できます。次の手順を行います。

1. ポータルで **[モニター]** を選択し、左側のペインで **[ストレージ アカウント]** を選択します。

2. **[概要]** ブックで、コマンド バーから **[編集]** を選択します。

3. **[サブスクリプション]** ドロップダウン リストから、既定にするサブスクリプションを 1 つ以上選択します。 1 つのブックで、最大 10 個のサブスクリプションを選択できます。  

4. **[ストレージ アカウント]** ドロップダウン リストから、既定にするアカウントを 1 つ以上選択します。 1 つのブックで、最大 200 個のストレージ アカウントを選択できます。 

5. コマンド バーから **[名前を付けて保存]** を選択して、カスタマイズしたブックのコピーを保存した後、 **[編集完了]** をクリックして読み取りモードに戻ります。  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>ブックのメトリックと色を変更する

作成済みのブックにはメトリック データが含まれており、どの視覚エフェクトも変更または削除することができ、チーム固有のニーズに合わせてカスタマイズすることができます。

この例では、複数サブスクリプションとストレージ アカウントの容量ブックを使用して、次の方法を示します。

* メトリックを削除する
* 色の表示を変更する

作成済みの **[Failures]\(エラー数\)** 、 **[パフォーマンス]** 、 **[可用性]** 、 **[容量]** のどのブックでも、同じ変更を実行できます。

1. ポータルで **[モニター]** を選択し、左側のペインで **[ストレージ アカウント]** を選択します。

2. **[容量]** を選択して容量ブックに切り替え、コマンド バーから **[編集]** を選択します。

    ![[編集] を選択してブックを変更する](./media/storage-insights-overview/workbook-edit-workbook.png)

3. メトリック セクションの横にある **[編集]** を選択します。

    ![[編集] を選択して容量ブックのメトリックを変更する](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. **[Account used capacity timeline]\(アカウント使用済み容量タイムライン\)** 列を削除するため、メトリック グリッドで **[列の設定]** を選択します。

    ![列の設定を編集する](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. **[列の設定の編集]** ウィンドウの **[列]** セクションで **[microsoft.storage/storageaccounts-Capacity-UsedCapacity Timeline$|Account used capacity Timeline$]** を選択し、 **[列レンダラー]** ドロップダウン リストで **[非表示]** を選択します。

6. **[保存して閉じる]** を選択して、変更をコミットします。

次に、レポートの容量メトリックの配色テーマを、青ではなく緑を使うように変更してみましょう。

1. メトリック グリッドで **[列の設定]** を選択します。

2. **[列の設定の編集]** ウィンドウの **[列]** セクションで **microsoft.storage/storageaccounts-Capacity-UsedCapacity$`|`microsoft.storage/storageaccounts/blobservices-Capacity-BlobCapacity$`|`microsoft.storage/storageaccounts/fileservices-Capacity-FileCapacity$`|`microsoft.storage/storageaccounts/queueservices-Capacity-QueueCapacity$`|`microsoft.storage/storageaccounts/tableservices-Capacity-TableCapacity$** を選択します。 **[カラー パレット]** ドロップダウン リストで、 **[緑]** を選択します。

3. **[保存して閉じる]** を選択して、変更をコミットします。

4. コマンド バーから **[名前を付けて保存]** を選択して、カスタマイズしたブックのコピーを保存した後、 **[編集完了]** をクリックして読み取りモードに戻ります。  

### <a name="modify-the-availability-threshold"></a>可用性のしきい値を変更する

この例では、ストレージ アカウントの容量ブックを操作し、可用性のしきい値を変更する方法を示します。 既定では、タイルとグリッドでレポートされる可用性のパーセントは、最小しきい値が 90、最大しきい値が 99 で構成されています。 ここでは、 **[Availability by API name]\(API 名別可用性\)** グリッドの **[可用性 %]** の最小しきい値を 85% に変更します。これは、しきい値が 85% 未満の場合は正常性状態がクリティカルに変更されることを意味します。 

1. ポータルから **[ストレージ アカウント]** を選択し、一覧からストレージ アカウントを選択します。

2. 左側のペインで **[分析情報]** を選択します。

3. ブックで、 **[可用性]** を選択して可用性ブックに切り替えてから、コマンド バーの **[編集]** を選択します。 

4. ページの下端までスクロールし、 **[Availability by API]\(API 別可用性\)** グリッドの左側にある **[編集]** を選択します。

    ![API 名別可用性グリッドの設定を編集する](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. **[列の設定]** を選択し、 **[列の設定の編集]** ウィンドウで、 **[列]** セクションの **[Availability (%) (Thresholds + Formatted)]\(可用性 (%) (しきい値 + 書式設定済み)\)** を選択します。

6. **[クリティカル]** 正常性状態の値を **90** から **85** に変更し、 **[保存して閉じる]** をクリックします。

    ![クリティカル状態の可用性しきい値を変更する](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. コマンド バーから **[名前を付けて保存]** を選択して、カスタマイズしたブックのコピーを保存した後、 **[編集完了]** をクリックして読み取りモードに戻ります。

## <a name="troubleshooting"></a>トラブルシューティング

一般的なトラブルシューティングのガイダンスについては、専用のブックベースの分析情報の[トラブルシューティングに関する記事](troubleshoot-workbooks.md)を参照してください。

このセクションは、Azure Monitor for Storage を使用するときに発生する可能性があるいくつかの一般的な問題を診断し、トラブルシューティングするのに役立ちます。 以下のリストを使用して、特定の問題に関連する情報を見つけてください。

### <a name="resolving-performance-capacity-or-availability-issues"></a>パフォーマンス、容量、または可用性の問題の解決

Azure Monitor for Storage で発生するストレージ関連の問題のトラブルシューティングについては、Azure Storage の[トラブルシューティング ガイダンス](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)に関するページを参照してください。  

### <a name="why-can-i-only-see-200-storage-accounts"></a>200 個のストレージ アカウントしか表示できないのはなぜですか。

選択されているサブスクリプションの数には関係なく、選択されるストレージ アカウントの数には 200 の制限があります。

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>可用性の色としきい値を変更するにはどうしたらよいですか。

可用性の色としきい値を変更する方法に関する詳細な手順については、「[可用性のしきい値を変更する](#modify-the-availability-threshold)」のセクションを参照してください。

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Azure Monitor for Storage に表示されているデータを分析およびトラブルシューティングするにはどうしたらよいですか。

 Azure Monitor for Storage に表示されている Azure Storage データを分析およびトラブルシューティングする方法の詳細については、「[Microsoft Azure Storage の監視、診断、およびトラブルシューティング](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)」の記事を参照してください。

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>メトリックにすべての種類のエラーが表示されないのはなぜですか。

現在、最大 3 種類のエラーが表示され、残りのエラーは 1 つのバケットにグループ化されます。 これは splitByLimit を使用して制御され、変更が可能です。 このプロパティを変更するには、次の操作を行います。

1. [edit workbook] (ブックの編集) をクリックします。
2. メトリックに移動し、編集をクリックしてから、 **[Transactions, Sum] (トランザクション、合計)** または編集する任意のメトリックを選択します。

    ![メトリックに移動し、編集をクリックしてから、[Transactions, Sum] (トランザクション、合計) をクリックする](./media/storage-insights-overview/fqa7.png)

3. 次に、[Number of Splits] (分割数) を変更します。

    ![[Select Metric Parameters] (メトリック パラメーターの選択)](./media/storage-insights-overview/fqa7-2.png)

n 種類のエラーを表示する場合は、splitByLimit を n+1 として指定し、残りのエラーのために 1 を追加します。

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>あるストレージ アカウントで自分のブックを保存しました。 今それが見つからないのはなぜですか。

各ブックは、それを保存したときのストレージ アカウントに保存されます。 ユーザーがそのブックを保存した特定のストレージ アカウントを探してみてください。 それ以外に、リソース (ストレージ アカウント) がわからずに特定のブックを見つける方法はありません。

## <a name="next-steps"></a>次のステップ

* [メトリック アラート](../alerts/alerts-metric.md)と[サービス正常性通知](../../service-health/alerts-activity-log-service-notifications-portal.md)を構成して、問題の検出に役立つ自動アラートを設定します。

* ブックがサポートするように設計されているシナリオ、新規レポートの作成方法と既存レポートのカスタマイズ方法などについては、「[Azure Monitor ブックを使用した対話型レポートの作成](../visualize/workbooks-overview.md)」で学習してください。

* Storage Analytics や他のツールを使用した Azure Storage 関連の問題の特定、診断、トラブルシューティングに関する詳しいガイドについては、「[Microsoft Azure Storage の監視、診断、およびトラブルシューティング](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)」をご覧ください。
