---
title: チュートリアル:Azure Synapse Analytics の使用を開始する - Power BI でワークスペース データを視覚化する
description: このチュートリアルでは、Power BI ワークスペースの作成方法、Azure Synapse ワークスペースのリンク方法、Azure Synapse ワークスペースのデータを利用する Power BI データ セットの作成方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 40a2cd5898ede7fc30db9a8475c5ab7cc68095ff
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337638"
---
# <a name="visualize-data-with-power-bi"></a>Power BI でデータを視覚化する

このチュートリアルでは、Power BI ワークスペースの作成方法、Azure Synapse ワークスペースのリンク方法、Azure Synapse ワークスペースのデータを利用する Power BI データ セットの作成方法について説明します。 

## <a name="overview"></a>概要

NYC タクシーのデータから、2 つのテーブルに集約されたデータセットを作成しました。
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

Power BI ワークスペースを Azure Synapse ワークスペースにリンクすることができます。 この機能により、Power BI ワークスペースに簡単にデータを取り込むことができます。 Power BI レポートは、Azure Synapse ワークスペースで直接編集できます。

### <a name="create-a-power-bi-workspace"></a>Power BI ワークスペースを作成する

1. [powerbi.microsoft.com](https://powerbi.microsoft.com/) にサインインします。
1. **NYCTaxiWorkspace1** という名前の新しい Power BI ワークスペースを作成します。

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Azure Synapse ワークスペースを新しい Power BI ワークスペースにリンクする

1. Synapse Studio で、 **[管理]**  >  **[リンクされたサービス]** に移動します。
1. **[新規]**  >  **[Power BI アカウントに接続する]** を選択して、次のフィールドを選択します。

    |設定 | 推奨値 | 
    |---|---|
    |**名前**|**NYCTaxiWorkspace1**|
    |**ワークスペース名**|**NYCTaxiWorkspace1**|

1. **［作成］** を選択します

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Azure Synapse ワークスペース内のデータを使用する Power BI データセットを作成する

1. Synapse Studio で、 **[開発]**  >  **[Power BI]** に移動します。
1. **[NYCTaxiWorkspace1]**  >  **[Power BI データセット]** に移動し、 **[New Power BI dataset]\(新しい Power BI データセット\)** を選択します。
1. **SQLDB1** データベースの上にマウス ポインターを移動し、 **[Download .pbids file]\(.pbids ファイルのダウンロード\)** を選択します。
1. ダウンロードした **.pbids** ファイルを開きます。 Power BI Desktop が開いて、Azure Synapse ワークスペース内の **SQLDB1** に自動的に接続します。
1. **[SQL Server データベース]** というダイアログ ボックスが表示された場合は、次の手順を実行します。
    1. **[Microsoft アカウント]** を選択します。
    1. **[サインイン]** を選択してアカウントにサインインします。
    1. **[接続]** を選択します。
1. **[ナビゲーター]** ダイアログ ボックスが開いたら、**PassengerCountStats** テーブルをチェックし、 **[読み込み]** を選択します。
1. **[接続の設定]** ダイアログ ボックスが表示されたら、 **[DirectQuery]**  >  **[OK]** をクリックします。
1. 左側の **[レポート]** ボタンを選択します。
1. レポートに**折れ線グラフ**を追加します。
    1. **[PassengerCount]** 列を **[視覚化]**  >  **[軸]** にドラッグします。
    1. **SumTripDistance** 列と **AvgTripDistance** 列を **[視覚化]**  >  **[値]** にドラッグします。
1. **[ホーム]** タブで **[発行]** を選択します。
1. **[保存]** を選択して変更を保存します。
1. **PassengerAnalysis.pbix** というファイル名を選択し、 **[保存]** を選択します。
1. **[宛先を選択してください]** で **[NYCTaxiWorkspace1]** を選択し、 **[選択]** をクリックします。
1. 発行が完了するまで待ちます。

### <a name="configure-authentication-for-your-dataset"></a>データセットの認証を構成する

1. [powerbi.microsoft.com](https://powerbi.microsoft.com/) を開き、**サインイン**します。
1. 左側の **[ワークスペース]** で、**NYCTaxiWorkspace1** ワークスペースを選択します。
1. ワークスペース内で、**Passenger Analysis** というデータセットと **Passenger Analysis** というレポートを探します。
1. **PassengerAnalysis** データセットにマウス ポインターを合わせ、省略記号 (...) ボタンを選択して、 **[設定]** を選択します。
1. **[データ ソースの資格情報]** で、 **[認証方法]** を **[OAuth2]** に設定し、 **[サインイン]** を選択します。

### <a name="edit-a-report-in-synapse-studio"></a>Synapse Studio でレポートを編集する

1. Synapse Studio に戻り、 **[Close and refresh]\(閉じて更新\)** を選択します。
1. **[開発]** ハブに移動します。
1. **Power BI** の上にマウス ポインターを移動し、 **[Power BI レポート]** ノードの更新を選択します。
1. **[Power BI]** に次のものが表示されます。
    * **[NYCTaxiWorkspace1]**  >  **[Power BI データセット]** に、**PassengerAnalysis** という新しいデータセットが表示されます。
    * **[NYCTaxiWorkspace1]**  >  **[Power BI レポート]** に、**PassengerAnalysis** という新しいレポートが表示されます。
1. **PassengerAnalysis** レポートを選択します。 レポートが開き、Synapse Studio 内で直接レポートを編集することができます。

## <a name="monitor-activities"></a>モニター活動

1. Synapse Studio で **[監視]** ハブに移動します。
1. この場所には、ワークスペースで発生したすべてのアクティビティの履歴と、現在アクティブになっているアクティビティが表示されます。
1. **[パイプラインの実行]** 、 **[Apache Spark applications]\(Apache Spark アプリケーション\)** 、 **[SQL requests]\(SQL 要求\)** を調べ、ワークスペースで既に実行した内容を確認します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [監視](get-started-monitor.md)
                                 

