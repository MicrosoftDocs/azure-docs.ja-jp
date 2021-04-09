---
title: リソース セットについて
description: この記事では、リソース セットの概要と、Azure Purview でそれらが作成される仕組みについて説明します。
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: cbf070dce056795ad8e4a5f3e4d609e7d36d631e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200813"
---
# <a name="understanding-resource-sets"></a>リソース セットについて

この記事は、Azure Purview がリソース セットを使用してデータ資産を論理リソースにマップする方法を理解するのに役立ちます。
## <a name="background-info"></a>背景情報

大規模なデータ処理システムでは、通常、1 つのテーブルが複数のファイルとしてディスクに格納されます。 Azure Purview では、この概念がリソース セットを使用して表されます。 リソース セットは、カタログにおいて、ストレージ内の多数の資産を表す単一のオブジェクトです。

たとえば、Spark クラスターで DataFrame が Azure Data Lake Storage (ADLS) Gen2 データ ソースに永続化されたとします。 Spark では、テーブルは 1 つの論理リソースのように見えますが、ディスク上には Parquet ファイルが多数存在し、それぞれが DataFrame のコンテンツ全体のパーティションを表す可能性があります。 IoT データと Web ログ データには同じ課題があります。 たとえば、ログフ ァイルを 1 秒間に数回出力するセンサーがあるとします。 この 1 つのセンサーから数千のログ ファイルが取得されるのに時間はかかりません。

大量のデータ資産を 1 つの論理リソースにマッピングするという課題に対処するため、Azure Purview ではリソース セットを使用します。

## <a name="how-azure-purview-detects-resource-sets"></a>Azure Purview がリソース セットを検出する方法

Azure Purview では、Azure Blob Storage、ADLS Gen1、および ADLS Gen2 でのリソース セットの検出がサポートされています。

Azure Purview では、スキャン時に自動的にリソース セットが検出されます。 この機能は、スキャンによって取り込まれたすべてのデータを調べ、定義済みのパターンのセットと比較します。

たとえば、URL が `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` のデータ ソースをスキャンするとします。 Azure Purview はパス セグメントを参照し、それらが組み込みのパターンに一致するかどうかを判断します。 GUID、数値、日付形式、ローカライズ コード (en-us など) などのパターンが組み込まれています。 この場合、数値パターンが *23* と一致します。 Azure Purview では、このファイルが `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` という名前のリソース セットの一部であると見なされます。

また、`https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` のような URL の場合、Azure Purview はローカライズ パターンと数値パターンの両方を一致させて、`https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` という名前のリソース セットを生成します。

この戦略を使用すると、Azure Purview は次のリソースを同じリソース セット `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` にマップします。

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Azure Purview がリソース セットとして検出しないファイルの種類

Purview では意図的に、Word、Excel、PDF など、ほとんどの種類のドキュメント ファイルはリソース セットとして分類されません。 CSV 形式は一般的なパーティション分割されたファイル形式のため、例外です。

## <a name="how-azure-purview-scans-resource-sets"></a>Azure Purview がリソース セットをスキャンする方法

リソース セットの一部であると思われるリソースを検出すると、Azure Purview はフル スキャンからサンプル スキャンに切り替えます。 サンプル スキャンでは、リソース セットに含まれていると見なされるファイルのサブセットのみが開かれます。 開くファイルごとに、そのスキーマが使用され、分類子が実行されます。 その後 Azure Purview は、開いているリソースの中で最も新しいリソースを見つけ、そのリソースのスキーマと分類を、カタログでのリソース セット全体のエントリで使用します。

## <a name="what-azure-purview-stores-about-resource-sets"></a>Azure Purview でリソース セットに関して格納される情報

Azure Purview では、単一のスキーマと分類に加えて、リソース セットに関する次の情報が格納されます。

- ディープ スキャンを行った最新のパーティション リソースのデータ。
- リソース セットを構成するパーティション リソースに関して集めた情報。
- 検出されたパーティション リソースの数を示すパーティション数。
- ディープ スキャンを行ったサンプル セットで見つかった一意のスキーマの数を示すスキーマ数。 この値は、1 から 5 の間の数値か、5、5 以上の値です。
- 複数のパーティションの種類がリソース セットに含まれている場合のパーティションの種類のリスト。 たとえば、IoT センサーは XML ファイルと JSON ファイルの両方を出力する場合がありますが、どちらも論理的には同じリソース セットに含まれます。

## <a name="built-in-resource-set-patterns"></a>組み込みのリソース セット パターン

Azure Purview では、次のリソース セット パターンがサポートされています。 これらのパターンは、ディレクトリ内の名前として、またはファイル名の一部として表示されます。
### <a name="regex-based-patterns"></a>正規表現ベースのパターン

| パターン名 | 表示名 | 説明 |
|--------------|--------------|-------------|
| Guid         | {GUID}       | [RFC 4122](https://tools.ietf.org/html/rfc4122) に定義されているグローバル一意識別子です。 |
| Number       | {N}          | 1 つまたは複数の数字 |
| 日付時刻形式 | {Year}{Month}{Day}{N}     | さまざまな日付時刻形式がサポートされていますが、すべて {Year}[区切り文字]{Month}[区切り文字]{Day} または一連の {N} で表されます。 |
| 4ByteHex     | {HEX}        | 4 桁の 16 進数値。 |
| ローカライズ | {LOC}        | [BCP 47](https://tools.ietf.org/html/bcp47) に定義されている言語タグ。- と _ の両方の名前がサポートされています (たとえば、en_ca と en-ca) |

### <a name="complex-patterns"></a>複雑なパターン

| パターン名 | 表示名 | 説明 |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Spark パーティション ファイル識別子 |
| Date(yyyy/mm/dd)InPath  | {Year}/{Month}/{Day} | 複数のフォルダーにまたがる年/月/日パターン |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Azure Purview カタログでのリソース セットの表示方法

Azure Purview では、資産グループをリソース セットに一致させるときに、カタログでの表示名として使用するのに最も有用な情報の抽出が試行されます。 適用される既定の名前付け規則の例を次に示します。 

### <a name="example-1"></a>例 1

修飾名: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

表示名: "name of spark output"

### <a name="example-2"></a>例 2

修飾名: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

表示名: "my partitioned data"

### <a name="example-3"></a>例 3

修飾名: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

表示名: "data"

## <a name="known-issues-with-resource-sets"></a>リソース セットに関する既知の問題

リソース セットは、ほとんどの場合、適切に機能しますが、Azure Purview で次のような問題が発生する可能性があります。

- 資産をリソース セットとして誤ってマークする
- 資産を間違ったリソース セットに配置する
- 資産をリソース セットではないとして誤ってマークする

## <a name="next-steps"></a>次のステップ

Azure Purview の使用を開始するには、[Azure Purview アカウントの作成](create-catalog-portal.md)に関するクイックスタートを参照してください。
