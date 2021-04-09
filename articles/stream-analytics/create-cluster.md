---
title: Azure Stream Analytics クラスターの作成のクイックスタート
description: Azure Stream Analytics クラスターの作成方法を説明します。
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 6b7a7c5feb6165f5b2c7c0ea052318a255e8025c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600226"
---
# <a name="quickstart-create-a-dedicated-azure-stream-analytics-cluster-using-azure-portal"></a>クイック スタート:Azure portal を使用して専用の Azure Stream Analytics クラスターを作成する

Azure portal を使用して Azure Stream Analytics クラスターを作成します。 [Stream Analytics クラスター](cluster-overview.md)はシングル テナント デプロイであり、複雑で要求の厳しいストリーミング ユース ケースに使用できます。 Stream Analytics クラスター上で、複数の Stream Analytics ジョブを実行できます。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 次の項目の完了: 「[クイックスタート: Azure portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)」。

## <a name="create-a-stream-analytics-cluster"></a>Stream Analytics クラスターの作成

このセクションでは、Stream Analytics クラスター リソースを作成します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[リソースの作成]** を選択します。 *[Marketplace を検索]* 検索ボックスで、「**Stream Analytics クラスター**」と入力して選択します。 その後、 **[追加]** を選択します。

   :::image type="content" source="./media/create-cluster/search-result.png" alt-text="Stream Analytics クラスターの検索結果。":::

1. **[Create Stream Analytics cluster]\(Stream Analytics クラスターの作成\)** ページで、新しいクラスターの基本設定を入力します。

   |設定|値|説明 |
   |---|---|---|
   |サブスクリプション|サブスクリプション名|この Stream Analytics クラスターに使用する Azure サブスクリプションを選択します。 |
   |リソース グループ|リソース グループ名|リソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの一意の名前を入力します。 |
   |クラスター名|一意の名前|Stream Analytics クラスターを識別するための名前を入力します。|
   |場所|データ ソースとシンクに最も近いリージョン|Stream Analytics クラスターをホストする地理的な場所を選択します。 低待機時間分析のために、データ ソースおよびシンクに最も近い場所を使用します。|
   |ストリーミング ユニット容量|36 から 216 |実行する予定の Stream Analytics ジョブの数と、ジョブに必要な SU の総数を見積もることによって、クラスターのサイズを決定します。 36 の SU から開始し、必要に応じて後でスケールアップまたはスケールダウンすることができます。|

   ![クラスターの作成](./media/create-cluster/create-cluster.png)

1. **[Review + create]\(レビュー + 作成\)** を選択します。 **[タグ]** セクションはスキップできます。

1. クラスターの設定を確認し、 **[作成]** を選択します。 クラスターの作成は実行時間の長い操作であり、完了するまでに約 60 分かかることがあります。 ポータル ページに "**デプロイが完了しました**" と表示されるまで待ちます。 その間、このクラスター上で実行する [Stream Analytics ジョブ](stream-analytics-quick-create-portal.md#create-a-stream-analytics-job)を作成し、開発することができます (まだインストールしていない場合)。

1. **[リソースに移動]** を選択し、[Stream Analytics クラスター] ページにアクセスします。

## <a name="delete-your-cluster"></a>クラスターを削除する

Stream Analytics ジョブを実行する予定がない場合は、Stream Analytics クラスターを削除できます。 Azure portal 上の手順に従って、クラスターを削除します。

1. **[設定]** の下の **[Stream Analytics ジョブ]** にアクセスし、実行中のすべてのジョブを停止します。

1. クラスターの **[概要]** に移動します。 **[削除]** を選択し、指示に従ってクラスターを削除します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Stream Analytics クラスターを作成する方法について説明しました。 クラスター上で Stream Analytics ジョブを実行する方法を学習するには、次の記事に進んでください。

> [!div class="nextstepaction"]
> [Stream Analytics クラスター内で Stream Analytics ジョブを管理する](manage-jobs-cluster.md)
