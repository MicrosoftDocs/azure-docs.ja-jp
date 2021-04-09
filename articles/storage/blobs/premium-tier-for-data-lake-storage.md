---
title: Azure Data Lake Storage の Premium サービス レベル | Microsoft Docs
description: Azure Data Lake Storage Gen2 で Premium パフォーマンス レベルを使用する
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/30/2020
ms.author: normesta
ms.openlocfilehash: be440407fb6f4d9715ba80c584af023a5f662394
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93324225"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Azure Data Lake Storage の Premium サービス レベル

Azure Data Lake Storage Gen2 では、[Premium パフォーマンス レベル](storage-blob-performance-tiers.md#premium-performance)がサポートされるようになりました。 Premium パフォーマンス レベルは、一貫性のある低遅延を必要とし、トランザクション数が多い、ビッグ データ分析のアプリケーションやワークロードに最適です。

## <a name="workloads-that-can-benefit-from-the-premium-performance-tier"></a>Premium パフォーマンス レベルのメリットが得られるワークロード

ワークロードの例として、対話型ワークロード、IoT、ストリーミング分析、人工知能、機械学習などがあります。 

**対話型ワークロード** 

これらのワークロードには、即時の更新とユーザーからのフィードバックが必要です (eコマースやマッピング アプリケーション、対話型ビデオ アプリケーションなど)。たとえば、eコマース アプリケーションでは、表示頻度の低い項目はキャッシュされない可能性があります。 ただし、要求時には顧客に瞬時に表示される必要があります。 もう 1 つの例として、データ科学者、アナリスト、開発者は、Premium パフォーマンス レベルを使用するアカウントに格納されているデータに対してクエリを実行することで、時間的な制約のある分析情報をより迅速に得ることができます。 

**IoT/ストリーミング分析** 

IoT シナリオでは、毎秒、小さな書き込み操作がクラウドにプッシュされる可能性があります。 大量のデータが取り込まれ、分析のために集計された後、ほぼ瞬時に削除される場合があります。 Premium パフォーマンス レベルの高度なインジェスト機能を利用すると、この種類のワークロードに効率的に対応できます。 

**人工知能/機械学習 (AI/ML)** 

AI/ML では、ビジュアル、音声、テキストなどのさまざまなデータの種類の使用と処理を扱います。 このハイ パフォーマンス コンピューティング型のワークロードでは大量のデータが処理されますが、データ分析のために迅速な応答と効率的なインジェスト時間が必要です。 

## <a name="cost-effectiveness"></a>コスト効率

Premium パフォーマンス レベルでは、Standard パフォーマンス レベルと比較してストレージ コストは高くなりますが、トランザクション コストは低くなります。 アプリケーションとワークロードで多数のトランザクションが実行される場合、Premium パフォーマンス レベルはコスト効率が高くなります。

次の表は、Azure Data Lake Storage の Premium サービス レベルのコスト効率を示しています。 各列は、1 か月のトランザクション数を表します。  各行は、読み取りトランザクションであるトランザクションのパーセンテージを表します。 表内の各セルは、読み取りトランザクションのパーセンテージと実行されたトランザクション数に関連付けたコスト削減率を示しています。 

たとえば、お使いのアカウントが米国東部 2 リージョンにあり、そのアカウントでのトランザクションの数が 90M を超えていて、それらのトランザクションの 70% が読み取りトランザクションである場合、Premium パフォーマンス レベルの方がコスト効率が高くなります。

> [!div class="mx-imgBorder"]
> ![画像がここに入ります](./media/premium-tier-for-data-lake-storage/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE] 
> データ 1 TB ごとの 1 秒あたりのトランザクション数に基づいてコスト効率を評価する場合は、表の下部に示されている列見出しを使用できます。

価格の詳細については、「[Azure Data Lake Storage Gen2 の価格](https://azure.microsoft.com/pricing/details/storage/data-lake/)」ページを参照してください。

## <a name="feature-availability"></a>使用可能な機能 

一部の BLOB ストレージ機能は、Premium パフォーマンス レベルでは利用できないか、その一部のみがサポートされている場合があります。 完全な一覧については、「[Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能](data-lake-storage-supported-blob-storage-features.md)」を参照してください。 その後に、[既知の問題](data-lake-storage-known-issues.md)の一覧を確認して、機能のギャップを評価します。

## <a name="enabling-the-premium-performance-tier"></a>Premium パフォーマンス レベルを有効にする 

Azure Data Lake Storage の Premium サービス レベルを使用するには、 **[階層型名前空間]** 設定を **有効** にして BlockBlobStorage アカウントを作成します。 詳細なガイダンスについては、「[BlockBlobStorage アカウントを作成する](storage-blob-create-account-block-blob.md)」を参照してください。

アカウントを作成するときは、必ず **[Premium]** パフォーマンスのオプションと **[BlockBlobStorage]** アカウントの種類を選択してください。

> [!div class="mx-imgBorder"]
> ![BlockBlobStorage アカウントの作成](./media/premium-tier-for-data-lake-storage/create-block-blob-storage-account.png)

**[ストレージ アカウントの作成]** ページの **[詳細]** タブの **[階層型名前空間]** 設定を有効にします。 この設定は、アカウントを作成するときに有効にする必要があります。 後で有効にすることはできません。

次の図は、 **[ストレージ アカウントの作成]** ページのこの設定を示しています。

> [!div class="mx-imgBorder"]
> ![階層型名前空間の設定](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="regional-availability"></a>リージョン別の提供状況

Azure Data Lake Storage の Premium サービス レベルは次のリージョンで利用できます。

|リージョン|冗長性|
|--|--|
|米国東部|LRS、ZRS|
|米国東部 2|LRS、ZRS|
|米国中部|LRS|
|米国西部|LRS|
|米国西部 2|LRS、ZRS|
|米国中西部|LRS|
|米国中南部|LRS|
|カナダ中部|LRS|
|カナダ東部|LRS|
|北ヨーロッパ|LRS、ZRS|
|西ヨーロッパ|LRS、ZRS|
|英国南部|LRS|
|英国西部|LRS|
|フランス中部|LRS|
|東アジア|LRS|
|韓国中部|LRS|
|韓国南部|LRS|
|インド中部|LRS|
|インド西部|LRS|
|アラブ首長国連邦北部|LRS|
|東日本|LRS|
|西日本|LRS|
|東南アジア|LRS、ZRS|
|オーストラリア東部|LRS、ZRS|
|オーストラリア東南部|LRS|
|ブラジル南部|LRS|

## <a name="next-steps"></a>次のステップ

Azure Data Lake Storage の Premium サービス レベルを、Azure Databricks、Azure HDInsight、Azure Synapse Analytics などの任意の分析サービスで使用します。 

- [チュートリアル:Azure Data Lake Storage Gen2、Azure Databricks、および Spark](data-lake-storage-use-databricks-spark.md) 
- [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) HDInsight は現在、高速書き込みが有効になっている HBase クラスターと併せて Premium パフォーマンス レベルを使用するアカウントをサポートしています。
- [クイック スタート:Synapse ワークスペースを作成する](../../synapse-analytics/quickstart-create-workspace.md)

