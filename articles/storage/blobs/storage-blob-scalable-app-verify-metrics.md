---
title: ストレージ アカウントのスループットと待機時間のメトリックを Azure Portal で確認する | Microsoft Docs
description: ストレージ アカウントのスループットと待機時間のメトリックをポータルで確認する方法を説明します。
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: 1bec42dabde64fed8126e47e39f19536b28014f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714835"
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>ストレージ アカウントのスループットと待機時間のメトリックの確認

このチュートリアルはシリーズの第 4 部であり、かつ最後の部分です。 これまでのチュートリアルでは、大量のランダム データを Azure Storage アカウントにアップロードする方法と Azure Storage アカウントからダウンロードする方法を学習しました。 このチュートリアルでは、Azure Portal でメトリックを使用して、スループットと待機時間を確認する方法を示します。

シリーズの第 4 部では、次の方法を学習します。

> [!div class="checklist"]
> * Azure ポータルでのグラフの構成
> * スループットと待機時間のメトリックの確認

[Azure Storage のメトリック](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)は、Azure Monitor を使用して、ストレージ アカウントのパフォーマンスと可用性に関する統合ビューを提供します。

## <a name="configure-metrics"></a>メトリックを構成する

ストレージ アカウントの **[設定]** にある **[メトリック]** に移動します。

**[SUB SERVICE]\(サブ サービス\)** ドロップダウンから BLOB を選択します。

**[メトリック]** で、次の表のメトリックのいずれかを選択します。

以下のメトリックにより、アプリケーションの待機時間とスループットを確認できます。 ポータルで構成するメトリックは、1 分間の平均値です。 1 分間の途中でトランザクションが終了した場合は、その分のデータを半分にして平均値が計算されます。 アプリケーションでは、アップロード操作とダウンロード操作の時間が計測され、ファイルのアップロードおよびダウンロードにかかった実際の時間が出力されます。 この情報をポータルのメトリックと組み合わせて使用することで、スループットを完全に把握できます。

|メトリック|定義|
|---|---|
|**成功した場合の E2E 待機時間**|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|
|**成功した場合のサーバー待機時間**|Azure Storage による成功した要求の平均処理時間。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。 |
|**トランザクション**|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 この例では、ブロック サイズが 100 MB に設定されています。 この場合、各 100 MB ブロックがトランザクションと見なされます。|
|**イングレス**|イングレス データの量。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。 |
|**エグレス**|エグレス データの量。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。 |

**[時間]** の横の **[Last 24 hours (Automatic)]\(過去 24 時間 (自動)\)** を選択します。 **[時間の粒度]** で **[過去 1 時間]** と **[分]** を選択し、 **[適用]** をクリックします。

![ストレージ アカウントのメトリック](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

グラフには複数のメトリックを割り当てることができますが、複数のメトリックを割り当てるとディメンションによるグループ化の機能が無効になります。

## <a name="dimensions"></a>Dimensions

[ディメンション](./monitor-blob-storage-reference.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions)は、グラフをさらに詳しく調べて詳細な情報を得るために使用します。 メトリックごとにさまざまなディメンションがあります。 利用可能なディメンションの 1 つに、 **[API 名]** ディメンションがあります。 このディメンションは、グラフを各 API 呼び出しごとに分割します。 下の 1 番目の図は、ストレージ アカウントの合計トランザクション数のグラフの例を示しています。 2 番目の図も同じグラフですが、[API 名] ディメンションを選択した場合を示しています。 ご覧のとおり、各トランザクションが一覧表示され、API 名ごとの呼び出し回数を詳細に把握できます。

![ストレージ アカウントのメトリック - ディメンションを指定しない場合のトランザクション数](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![ストレージ アカウントのメトリック - トランザクション数](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、リソース グループ、仮想マシン、すべての関連リソースを削除します。 そのためには、VM のリソース グループを選択し、[削除] をクリックします。

## <a name="next-steps"></a>次のステップ

シリーズの第 4 部では、サンプル ソリューションのメトリックを確認する方法について学びました。

> [!div class="checklist"]
> * Azure ポータルでのグラフの構成
> * スループットと待機時間のメトリックの確認

事前に作成されたストレージ サンプルを確認するには、次のリンクに従ってください。

> [!div class="nextstepaction"]
> [Azure Storage のサンプル スクリプト](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md