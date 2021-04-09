---
title: サポートされているデータ ソースとファイルの種類
description: この記事では、Purview でサポートされているデータ ソースとファイルの種類に関する概念について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 3b19fab33d0c8f53025605fd14fe65f08e660392
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677928"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Azure Purview でサポートされているデータ ソースとファイルの種類

この記事では、Purview でサポートされているデータ ソース、ファイルの種類、およびスキャンの概念について説明します。

## <a name="supported-data-sources"></a>サポートされるデータ ソース

Azure Purview では、次のソースがサポートされています。

| ストアの種類 | サポートされる認証の種類 | UX または PowerShell を使用したスキャンの設定 |
| ---------- | ------------------- | ------------------------------ |
| オンプレミスの SQL Server                   | SQL 認証                        | UX                                |
| Azure Synapse Analytics (旧称 SQL DW)            | SQL 認証、サービス プリンシパル、MSI               | UX                             |
| Azure SQL Database (DB)                  | SQL 認証、サービス プリンシパル、MSI               | UX |
| Azure SQL Database Managed Instance      | SQL 認証、サービス プリンシパル、MSI               | UX    |
| Azure Blob Storage                       | アカウント キー、サービス プリンシパル、MSI | UX            |
| Azure Data Explorer                      | サービス プリンシパル                              | UX            |
| Azure Data Lake Storage Gen1 (ADLS Gen1) | サービス プリンシパル、MSI                              | UX            |
| Azure Data Lake Storage Gen2 (ADLS Gen2) | アカウント キー、サービス プリンシパル、MSI            | UX            |
| Azure Cosmos DB                          | アカウント キー                                    | UX            |


> [!Note]
> Azure Data Lake Storage Gen2 の一般提供が開始されました。 今すぐ使用を開始することをお勧めします。 詳細については、[製品に関するページ](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/)を参照してください。

## <a name="file-types-supported-for-scanning"></a>スキャンがサポートされているファイルの種類

次のファイルの種類は、スキャンと、該当する場合はスキーマの抽出と分類がサポートされています。

- 拡張子でサポートされる構造化ファイル形式: AVRO、ORC、PARQUET、CSV、JSON、PSV、SSV、TSV、TXT、XML、GZIP
- 拡張子でサポートされるドキュメント ファイル形式: DOC、DOCM、DOCX、DOT、ODP、ODS、ODT、PDF、POT、PPS、PPSX、PPT、PPTM、PPTX、XLC、XLS、XLSB、XLSM、XLSX、XLT
- Purview では、カスタム ファイル拡張子とカスタム パーサーもサポートされています。
 
> [!Note]
> すべての Gzip ファイルは、内部の 1 つの csv ファイルにマップする必要があります。 Gzip ファイルは、システムおよびカスタムの分類ルールの対象となります。 現在、内部の複数のファイルにマップされた gzip ファイル、または csv 以外の任意のファイルの種類のスキャンはサポートされていません。 

## <a name="sampling-within-a-file"></a>ファイル内のサンプリング

Purview の用語では、
- L1 スキャン: ファイル名、サイズ、完全修飾名などの基本的な情報とメタ データが抽出されます
- L2 スキャン: 構造化されたファイルの種類およびデータベース テーブルのスキーマが抽出されます
- L3 スキャン: 可能な場合スキーマが抽出され、サンプリングされたファイルにシステムおよびカスタムの分類ルールが適用されます

すべての構造化されたファイル形式について、Purview スキャナーによって次のようにファイルがサンプリングされます。

- 構造化されたファイルの種類の場合、各列の 128 行または 1 MB のいずれか小さい方がサンプリングされます。
- ドキュメント ファイル形式の場合、各ファイルにつき 20 MB がサンプリングされます。
    - ドキュメント ファイルが 20 MB を超える場合は、詳細スキャンの対象にはなりません (分類の対象になります)。 この場合、Purview によって、ファイル名や完全修飾名などの基本的なメタ データのみがキャプチャされます。

## <a name="resource-set-file-sampling"></a>リソース セット ファイルのサンプリング

フォルダーまたはパーティション ファイルのグループは、システムのリソース セット ポリシーまたは顧客定義のリソース セット ポリシーと一致する場合、"*リソース セット*" として Purview で検出されます。 リソース セットが検出されると、それに含まれている各フォルダーが Purview によってサンプリングされます。 リソース セットの詳細については、[こちら](concept-resource-sets.md)を参照してください。

ファイルの種類別のリソース セットのファイル サンプリングは次のとおりです。

- **区切られたファイル (CSV、PSV、SSV、TSV)** - 'リソース セット' と見なされるフォルダーまたはパーティション ファイルのグループ内で、100 個中 1 個のファイルがサンプリングされます (L3 スキャン)。
- **Data Lake ファイルの種類 (Parquet、Avro、Orc)** - "*リソース セット*" と見なされるフォルダーまたはパーティション ファイルのグループ内で、18446744073709551615 (long の最大値) 個中 1 個のファイルがサンプリングされます (L3 スキャン)。
- **その他の構造化されたファイルの種類 (JSON、XML、TXT)** - 'リソース セット' と見なされるフォルダーまたはパーティション ファイルのグループ内で、100 個中 1 個のファイルがサンプリングされます (L3 スキャン)。
- **SQL オブジェクトと CosmosDB エンティティ** - 各ファイルが L3 スキャンされます。
- **ドキュメント ファイルの種類** - 各ファイルが L3 スキャンされます。 リソース セットのパターンは、これらのファイルの種類には適用されません。

## <a name="scan-regions"></a>スキャンのリージョン
Purview スキャナーが実行されるすべての Azure データ ソース (データセンター) リージョンの一覧を次に示します。 Azure データ ソースがこの一覧にないリージョンにある場合、スキャナーは Purview インスタンスのリージョンで実行されます。
 
### <a name="purview-scanner-regions"></a>Purview スキャナーのリージョン

- EastUs
- EastUs2 
- SouthCentralUS
- WestUs
- WestUs2
- SoutheastAsia
- 西ヨーロッパ
- NorthEurope
- UkSouth
- AustraliaEast
- CanadaCentral
- BrazilSouth
- CentralIndia
- JapanEast
- SouthAfricaNorth
- FranceCentral

## <a name="classification"></a>分類

105 個すべてのシステム分類ルールが、構造化されたファイル形式に適用されます。 MCE 分類ルールのみがドキュメント ファイルの種類に適用されます (データ スキャン ネイティブの正規表現パターンではなく、ブルーム フィルターベースの検出)。 サポートされている分類の詳細については、「[Azure Purview でサポートされている分類](supported-classifications.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [チュートリアル: スタート キットの実行とデータのスキャン](tutorial-scan-data.md)
- [Azure Purview でデータ ソースを管理する (プレビュー)](manage-data-sources.md)