---
title: Azure Data Lake Storage Gen1 のパフォーマンス チューニング - PowerShell
description: Azure Data Lake Storage Gen1 で Azure PowerShell を使用するときにパフォーマンスを改善する方法に関するヒント。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/09/2018
ms.author: twooley
ms.openlocfilehash: 4ac2bbb21fd1a987d544a536d0f52628824e0bf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723798"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 で PowerShell を使用するためのパフォーマンス チューニング ガイダンス

この記事では、PowerShell を使用して Data Lake Storage Gen1 を操作する際のパフォーマンスを改善するために調整できるプロパティについて説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>パフォーマンス関連のプロパティ

| プロパティ            | Default | 説明 |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | このパラメーターを使用すると、各ファイルをアップロードまたはダウンロードする場合の並列スレッドの数を選択できます。 この数値は、1 ファイルに割り当てることができる最大スレッド数を表しますが、シナリオに応じてスレッドが少なくなる場合があります (たとえば、1 KB のファイルをアップロードする場合、20 個のスレッドを要求してもスレッドは 1 つになります)。  |
| ConcurrentFileCount | 10      | このパラメーターは、ファイルのアップロードまたはダウンロード専用です。 このパラメーターによって、同時にアップロードまたはダウンロードできるファイルの数が決まります。 この数値は、一度にアップロードまたはダウンロードできるコンカレント ファイルの最大数を表しますが、シナリオに応じてコンカレンシーの数が少なくなる場合があります (たとえば、2 つのファイルをアップロードしている場合、15 を要求しても同時に行われるファイルのアップロードは 2 つになります)。 |

**例:**

次のコマンドは、1 ファイルにつき 20 個のスレッドと、100 個の同時実行ファイルを使用して、ファイルを Data Lake Storage Gen1 からユーザーのローカル ドライブにダウンロードします。

```PowerShell
Export-AzDataLakeStoreItem -AccountName "Data Lake Storage Gen1 account name" `
    -PerFileThreadCount 20 `
    -ConcurrentFileCount 100 `
    -Path /Powershell/100GB `
    -Destination C:\Performance\ `
    -Force `
    -Recurse
```

## <a name="how-to-determine-property-values"></a>プロパティ値を決定する方法

次の考慮事項は、パフォーマンスに関連するプロパティに指定する値を決める方法です。 使用できるガイダンスがいくつかあります。

* **手順 1: 合計スレッド数を決める** - まず、使用する合計スレッド数を計算します。 一般的なガイドラインとして、各物理コアに 6 個のスレッドを使用する必要があります。

    `Total thread count = total physical cores * 6`

    **例:**

    16 コアが搭載された D14 VM から PowerShell コマンドを実行しているとします。

    `Total thread count = 16 cores * 6 = 96 threads`

* **手順 2: PerFileThreadCount を計算する** - ファイルのサイズに基づいて PerFileThreadCount を計算します。 2.5 GB 未満のファイルの場合、既定値の 10 で十分なため、このパラメーターを変更する必要はありません。 2.5 GB を超えるファイルの場合、最初の 2.5 GB に基準として 10 個のスレッドを使用し、ファイル サイズが 256 MB 増加するたびにスレッドを 1 つ追加する必要があります。 コピーするフォルダーにさまざまなファイル サイズが含まれている場合は、似たようなファイル サイズに分類することを検討してください。 異なるファイル サイズがあると、最適なパフォーマンスを得られない可能性があります。 似たようなファイル サイズをグループ化できない場合は、最も大きいファイル サイズに基づいて PerFileThreadCount を設定する必要があります。

    `PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size`

    **例:**

    1 GB から 10 GB までさまざまなファイルが 100 個あると仮定して、式には最大ファイル サイズとして 10 GB を使用します。式は次のようになります。

    `PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads`

* **手順 3: ConcurrentFilecount を計算する** - 合計スレッド数と PerFileThreadCount を使用して、次の式に基づいて ConcurrentFileCount を計算します。

    `Total thread count = PerFileThreadCount * ConcurrentFileCount`

    **例:**

    ここまでに使用してきた例の値に基づきます。

    `96 = 40 * ConcurrentFileCount`

    結果として、**ConcurrentFileCount** は **2.4** になります。これは、**2** に丸めることができます。

## <a name="further-tuning"></a>さらなる調整

作業対象のファイル サイズがさまざまであるため、さらなる調整が必要になる場合があります。 上記の計算は、すべてのファイルまたはほとんどのファイルが 10 GB を超えているか 10 GB に近い場合に適しています。 そうではなく、多くのファイルがもっと小さく、ファイル サイズが多様な場合は、PerFileThreadCount を小さくすることができます。 PerFileThreadCount を小さくすると、ConcurrentFileCount を大きくすることができます。 そのため、ファイルの大半が 5 GB より小さいと想定した場合、次のように計算をやり直すことができます。

`PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20`

結果として、**ConcurrentFileCount** は 96/20 (つまり 4.8) になり、**4** に丸められます。

ファイル サイズの分布に応じて **PerFileThreadCount** を増減することで、引き続きこれらの設定を調整できます。

### <a name="limitation"></a>制限事項

* **ファイルの数が ConcurrentFileCount より少ない**: アップロードしているファイルの数が、計算した **ConcurrentFileCount** より小さい場合、ファイル数と等しくなるように **ConcurrentFileCount** を小さくする必要があります。 残りのスレッドすべてを使用して、**PerFileThreadCount** を大きくすることができます。

* **スレッドが多すぎる**: クラスターのサイズを大きくせずにスレッド数を増やしすぎると、パフォーマンスの低下というリスクがあります。 CPU でのコンテキスト切り替え時に競合の問題が発生する可能性があります。

* **コンカレンシーが不十分**: コンカレンシーが十分でない場合、クラスターが小さすぎる可能性があります。 クラスター内のノード数を増やすことで、コンカレンシーを増加させることができます。

* **調整エラー**: コンカレンシーがあまりに多くなると、調整エラーが表示される場合があります。 調整エラーが表示される場合は、コンカレンシーを減らすか、Microsoft にお問い合わせください。

## <a name="next-steps"></a>次のステップ

* [Data Lake Storage Gen1 を使用してビッグ データの要件に対応する](data-lake-store-data-scenarios.md) 
* [Data Lake Storage Gen1 でのデータのセキュリティ保護](data-lake-store-secure-data.md)
* [Data Lake Storage Gen1 で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Storage Gen1 で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)

