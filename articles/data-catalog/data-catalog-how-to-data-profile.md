---
title: Azure Data Catalog でデータ プロファイリングのデータ ソースを使用する方法
description: この記事では、Azure Data Catalog でデータ ソースを登録するときにテーブル レベルのデータ プロファイルと列レベルのデータ プロファイルを含める方法と、データ プロファイルを基にデータ ソースについて理解する方法について説明します。
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 473ba69fa30542dfa41dfc5f19002ac36b56b482
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674956"
---
# <a name="how-to-data-profile-data-sources-in-azure-data-catalog"></a>Azure Data Catalog でデータ ソースをデータ プロファイリングする方法

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>はじめに

**Microsoft Azure Data Catalog** は、フル マネージドのクラウド サービスであり、エンタープライズ データ ソースの登録のシステムと検出のシステムとして機能します。 つまり、 **Azure Data Catalog** を使用すると、ユーザーはデータ ソースを検出、理解、使用でき、組織は既存のデータからより多くの価値を引き出すことができます。 データ ソースが **Azure Data Catalog** に登録されると、そのメタデータはサービスによってコピーされてインデックスが付けられます。ただし、これで終わりではありません。

**Azure Data Catalog** の **データのプロファイリング** 機能は、カタログでサポートされているデータ ソースからのデータを分析し、そのデータに関する統計と情報を収集します。 データ資産のプロファイルは簡単に追加できます。 データ資産を登録する際に、データ ソース登録ツールで **[データ プロファイルを含める]** を選択してください。

## <a name="what-is-data-profiling"></a>データのプロファイリングとは

データのプロファイリングとは、登録されているデータ ソース内のデータを分析し、そのデータに関する統計と情報を収集する処理です。 これらの統計情報は、データ ソースの検出時に、ビジネス上の問題解決に向けたデータの適合性を判断する際に役立てることができます。

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

データのプロファイリングは、次のデータ ソースでサポートされます。

* SQL Server (Azure SQL DB と Azure Synapse Analytics 含む) のテーブルとビュー
* Oracle のテーブルとビュー
* Teradata のテーブルとビュー
* Hive のテーブル

データ資産の登録時にデータ プロファイルを含めることで、データ ソースについて次の点が明らかになります。

* ビジネス上の問題解決に利用できるか。
* データが特定の標準やパターンに従っているか。
* データ ソースの不規則性。
* データをアプリケーションに統合するうえでどのような課題が考えられるか。

> [!NOTE]
> アプリケーションに対してどのようにデータを統合するかについて記述するドキュメントを資産に追加することもできます。 [データ ソースの文書化の方法](data-catalog-how-to-documentation.md)を参照してください。
>

<a name="howto"></a>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>データ ソースの登録時にデータ プロファイルを含める方法

データ ソースのプロファイルは簡単に追加できます。 データ ソースを登録するときに、データ ソース登録ツールの **[登録されるオブジェクト]** パネルで **[データ プロファイルを含める]** を選択します。

![[データ プロファイルを含める] チェック ボックス](media/data-catalog-data-profile/data-catalog-register-profile.png)

データ ソースを登録する方法の詳細については、「[データ ソースの登録方法](data-catalog-how-to-register.md)」と「[Azure Data Catalog の概要](data-catalog-get-started.md)」を参照してください。

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>データ プロファイルを含んだデータ資産をフィルターで抽出する

データ プロファイルを含んだデータ資産を検出するために、検索語の 1 つとして `has:tableDataProfiles` または `has:columnsDataProfiles` を追加できます。

> [!NOTE]
> データソース登録ツールで **[データ プロファイルを含める]** を選択すると、テーブル レベルと列レベルのプロファイル情報の両方が含まれます。 ただし、Data Catalog API では、1 つのプロファイル情報セットのみを含むデータ資産を登録できます。
>

## <a name="viewing-data-profile-information"></a>データ プロファイル情報の表示

プロファイルを含んだ適切なデータ ソースが見つかったら、そのデータ プロファイルの詳細を表示できます。 データ プロファイルを表示するには、[Data Catalog ポータル] ウィンドウでデータ資産を選択し、 **[データ プロファイル]** を選択します。

![[データ プロファイル] タブ](media/data-catalog-data-profile/data-catalog-view.png)

**Azure Data Catalog** のデータ プロファイルに、テーブルと列のプロファイル情報が表示されます。それぞれ表示される情報は以下のとおりです。

### <a name="object-data-profile"></a>オブジェクト データ プロファイル

* [行数]
* テーブルのサイズ
* オブジェクトが最後に更新されたのはいつか

### <a name="column-data-profile"></a>列データ プロファイル

* 列のデータ型
* 個別の値の数
* NULL 値を含んだ行の数
* 列の値の最小、最大、平均、標準偏差

## <a name="summary"></a>まとめ

登録されているデータ資産についての統計と情報は、データのプロファイリングを通じて得ることができます。ユーザーはそれを基に、ビジネス上の問題解決に向けたデータの適合性を判断することができます。 データ プロファイルは、データ ソースの注釈付けや文書化と共に、データについての理解を深める手段となります。

## <a name="see-also"></a>参照

* [データ ソースの登録方法](data-catalog-how-to-register.md)
* [Azure Data Catalog の概要](data-catalog-get-started.md)
