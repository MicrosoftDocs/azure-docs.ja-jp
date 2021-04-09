---
title: 専用 SQL プールにデータを取り込む
description: Azure Synapse Analytics の専用 SQL プールにデータを取り込む方法について説明します
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/03/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: a02abff712fc26f653307108fcc3bb284444d0c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676650"
---
# <a name="ingest-data-into-a-dedicated-sql-pool"></a>専用 SQL プールにデータを取り込む

この記事では、Azure Data Lake Gen 2 ストレージ アカウントから Azure Synapse Analytics の専用 SQL プールにデータを取り込む方法について説明します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* **Azure ストレージ アカウント**: "*ソース*" データ ストアとして Azure Data Lake Storage Gen 2 を使用します。 ストレージ アカウントがない場合の作成手順については、「[Azure Storage アカウントの作成](../../storage/common/storage-account-create.md)」を参照してください。
* **Azure Synapse Analytics**:専用 SQL プールを "*シンク*" データ ストアとして使用します。 Azure Synapse Analytics インスタンスをお持ちでない場合、[専用 SQL プールの作成](../../azure-sql/database/single-database-create-quickstart.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関するページでその作成手順を参照してください。

## <a name="create-linked-services"></a>リンクされたサービスを作成します

Azure Synapse Analytics で、リンクされたサービスとは、他のサービスへの接続情報を定義した場所です。 このセクションでは、Azure Synapse Analytics および Azure Data Lake Storage Gen2 のリンクされたサービスを追加します。

1. Azure Synapse Analytics UX を開き、 **[管理]** タブに移動します。
1. **[外部接続]** で、 **[リンクされたサービス]** を選択します。
1. リンクされたサービスを追加するには、 **[新規]** を選択します。
1. リストから [Azure Data Lake Storage Gen2] タイルを選択し、 **[続行]** を選択します。
1. ご利用の認証資格情報を入力します。 アカウント キー、サービス プリンシパル、およびマネージド ID が現在サポートされている認証の種類です。 [接続のテスト] を選択して、ご利用の資格情報が正しいことを確認します。 入力し終えたら **[作成]** を選択します。
1. 手順 3 から 5 を繰り返します。ただし、[Azure Data Lake Storage Gen2] ではなく、[Azure Synapse Analytics] タイルを選択し、対応する接続資格情報を入力します。 Azure Synapse Analytics では、SQL 認証、マネージド ID、およびサービス プリンシパルが現在サポートされています。

## <a name="create-pipeline"></a>パイプラインの作成

パイプラインには、一連のアクティビティを実行するための論理フローが含まれています。 このセクションでは、ADLS Gen2 から専用 SQL プールにデータを取り込むコピー アクティビティを含んだパイプラインを作成します。

1. **[統合]** タブに移動します。パイプライン ヘッダーの横にあるプラス符号のアイコンを選択し、 **[パイプライン]** を選択します。
1. アクティビティ ウィンドウの **[Move and Transform]\(移動と変換\)** で、 **[データ コピー]** をパイプライン キャンバス上にドラッグします。
1. [コピー アクティビティ] を選択し、 **[ソース]** タブに移動します。 **[新規]** を選択して、新しいソース データセットを作成します。
1. 使用するデータ ストアとして、[Azure Data Lake Storage Gen2] を選択し、[続行] を選択します。
1. 使用する形式として [DelimitedText] を選択し、[続行] を選択します。
1. [プロパティの設定] ウィンドウで、作成した ADLS のリンクされたサービスを選択します。 使用するソース データのファイル パスを指定し、最初の行にヘッダーを含めるかどうかを指定します。 ファイル ストアまたはサンプル ファイルからスキーマをインポートできます。 終わったら [OK] を選択します。
1. **[シンク]** タブに移動します。 **[新規]** を選択して、新しいシンク データセットを作成します。
1. データ ストアとして [Azure Synapse Analytics] を選択し、[続行] を選択します。
1. [プロパティの設定] ウィンドウで、作成した Azure Synapse Analytics のリンクされたサービスを選択します。 既存のテーブルに書き込む場合は、ドロップダウンから選択します。 それ以外の場合は、 **[編集]** を確認し、新しいテーブル名を入力します。 完了したら、[OK] を選択します。
1. テーブルを作成する場合は、[テーブル オプション] フィールドで **[テーブルの自動作成]** を有効にします。

## <a name="debug-and-publish-pipeline"></a>パイプラインのデバッグと発行

パイプラインの構成が完了したら、成果物を発行する前にデバッグを実行することで、すべてが正しいことを確認できます。

1. パイプラインをデバッグするには、ツール バーで **[デバッグ]** を選択します。 ウィンドウ下部の **[出力]** タブにパイプラインの実行の状態が表示されます。 
1. パイプラインを適切に実行できたら、上部のツール バーで **[すべて発行]** を選択します。 この操作により、作成したエンティティ (データセットとパイプライン) が Synapse Analytics サービスに発行されます。
1. **[正常に発行されました]** というメッセージが表示されるまで待機します。 通知メッセージを表示するには、右上にあるベル ボタンを選択します。 


## <a name="trigger-and-monitor-the-pipeline"></a>パイプラインをトリガーして監視する

この手順では、前の手順で発行したパイプラインを手動でトリガーします。 

1. ツール バーの **[トリガーの追加]** を選択し、 **[Trigger Now]\(今すぐトリガー\)** を選択します。 **[Pipeline Run]\(パイプラインの実行\)** ページで **[完了]** を選択します。  
1. 左側のサイドバーにある **[監視]** タブに移動します。 手動トリガーによってトリガーされたパイプラインの実行が表示されます。 **[アクション]** 列のリンクを使用して、アクティビティの詳細を表示したりパイプラインを再実行したりできます。
1. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、 **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクを選択します。 この例では、アクティビティが 1 つだけなので、一覧に表示されるエントリは 1 つのみです。 コピー操作の詳細を確認するために、 **[アクション]** 列にある **[詳細]** リンク (眼鏡アイコン) を選択します。 再度パイプラインの実行ビューに移動するには、一番上にある **[Pipeline Runs]\(パイプラインの実行\)** を選択します。 表示を更新するには、 **[最新の情報に更新]** を選択します。
1. データが専用 SQL プールに正しく書き込まれていることを確認します。


## <a name="next-steps"></a>次のステップ

Azure Synapse Analytics におけるデータ統合の詳細については、記事「[Azure Data Lake Storage Gen2 へのデータの取り込み](data-integration-data-lake.md)」を参照してください。