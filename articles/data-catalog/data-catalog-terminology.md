---
title: Azure Data Catalog の用語集
description: この記事では、Azure Data Catalog のドキュメントで使用される概念と用語について説明します。
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 008d0f106b07d61f0989a479457b12398000b5d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674575"
---
# <a name="azure-data-catalog-terminology"></a>Azure Data Catalog の用語集

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

この記事では、Azure Data Catalog のドキュメントで使用される概念と用語について説明します。

## <a name="catalog"></a>Catalog

Azure Data Catalog は、データ ソースとデータ資産を登録できるクラウドベースのメタデータ リポジトリです。 カタログは、データ ソースから抽出された構造メタデータとユーザーが追加した記述メタデータの一元的な保存場所です。

## <a name="data-source"></a>データ ソース

データ ソースは、データ資産を管理するシステムまたはコンテナーです。 たとえば、SQL Server データベース、Oracle データベース、SQL Server Analysis Services データベース (表形式または多次元)、SQL Server Reporting Services サーバーなどです。

## <a name="data-asset"></a>データ資産

データ資産は、カタログに登録することができるデータ ソース内のオブジェクトです。 たとえば、SQL Server のテーブルとビュー、Oracle のテーブルとビュー、SQL Server Analysis Services のメジャー、次元と KPI、SQL Server Reporting Services のレポートなどです。

## <a name="data-asset-location"></a>データ資産の場所

カタログには、データ ソースまたはデータ資産の場所が格納されています。クライアント アプリケーションから、この場所を使用してソースに接続できます。 場所の形式と詳細は、データ ソースの種類によって変わります。 たとえば、SQL Server テーブルは、サーバー名、データベース名、スキーマ名、オブジェクト名という 4 つの名前で識別できます。SQL Server Reporting Services のレポートは、URL で識別できます。

## <a name="structural-metadata"></a>構造メタデータ

構造メタデータは、データ ソースから抽出され、データ資産の構造を説明するメタデータです。 資産の場所、オブジェクトの名前と種類、その他の種類に固有に特性などが含まれます。 たとえば、テーブルとビューの構造メタデータには、オブジェクトの列の名前とデータ型が含まれます。

## <a name="descriptive-metadata"></a>記述メタデータ

記述メタデータは、データ資産の目的または意図を説明するメタデータです。 通常、記述メタデータは、カタログ ユーザーが Azure Data Catalog ポータルを使用して追加しますが、登録時にデータ ソースから抽出することもできます。 たとえば、SQL Server Analysis Services と SQL Server Reporting Services の場合は Description プロパティ、SQL Server データベースの場合は [ms_description 拡張プロパティ](/previous-versions/sql/sql-server-2008-r2/ms190243(v=sql.105))から、Azure Data Catalog 登録ツールで説明を抽出します (これらのプロパティに値が設定されている場合)。

## <a name="request-access"></a>アクセスの要求

データ資産の説明用のメタデータには、データ資産またはデータ ソースへのアクセスを要求する方法に関する情報を含めることができます。 この情報にはデータ資産の場所が示され、次のオプションの 1 つ以上を含めることができます。

* データ ソースへのアクセス権の付与を担当するユーザーまたはチームの電子メール アドレス。
* ユーザーがデータ ソースにアクセスするために従う必要があるプロセスを示すドキュメントの URL。
* データ ソースにアクセスするために使用できる ID およびアクセス管理ツール (Microsoft Identity Manager など) の URL。
* ユーザーがデータ ソースにアクセスする方法が示された自由書式のエントリ。

## <a name="preview"></a>プレビュー

Azure Data Catalog のプレビューは、最大 20 レコードのスナップショットです。登録時にデータ ソースから抽出し、データ資産メタデータと共にカタログに格納できます。 プレビューを使用すると、データ資産の機能と目的を理解しやすくなります。 言い換えると、列名とデータ型だけではなく、サンプル データも確認できるので、わかりやすくなります。
テーブルとビューのプレビューのみがサポートされています。登録時にユーザーが明示的にプレビューを選択する必要があります。

## <a name="data-profile"></a>データ プロファイル

Azure Data Catalog のデータ プロファイルとは、登録済みのデータ資産のテーブル レベルおよび列レベルのメタデータのスナップショットです。登録時にデータ ソースから抽出し、データ資産メタデータと共にカタログに格納できます。 データ プロファイルを使用すると、データ資産の機能と目的を理解しやすくなります。 データ プロファイルは、プレビューのように、登録時にユーザーから明示的に選択される必要があります。

> [!NOTE]
> 大規模なテーブルおよびビューでデータ プロファイルを抽出する場合、リソースは大量に消費され、データ ソースの登録に必要な時間が大幅に増加する可能性があります。


## <a name="user-perspective"></a>ユーザーの観点

Azure Data Catalog では、登録されているデータ資産を説明するメタデータを指定できます。 すべてのユーザーは、データとその使用方法について独自の観点を持っています。 たとえば、サーバーを担当する管理者は、サービス レベル アグリーメント (SLA) またはバックアップの詳細情報を指定し、データ スチュワードは、データがサポートするビジネス プロセスに関するドキュメントのリンクを指定し、アナリストは、他の分析に最も関連性が高く、データを検索して理解する必要があるユーザーにとって最も価値がある用語で記述を指定することができます。

このような各観点は本質的に価値があり、各ユーザーは Azure Data Catalog を使用して、自分にとって意味がある情報を指定できます。また、すべてのユーザーは、その情報を使用して、データとその用途を理解することができます。

## <a name="expert"></a>Expert

エキスパートとは、データ資産について知識がある "専門家" の観点を持つ人物と認められたユーザーです。 すべてのユーザーは、自分または他のユーザーを資産のエキスパートとして追加することができます。 エキスパートに登録された場合でも、Azure Data Catalog に追加の特権は付与されませんが、観点を簡単に特定できるようになります。たとえば、資産の記述メタデータを確認するときに役に立ちます。

## <a name="owner"></a>所有者

所有者は、Azure Data Catalog のデータ資産を管理する追加の特権を持つユーザーです。 ユーザーは、登録したデータ資産の所有権を得ることができます。また、所有者は他のユーザーを共同所有者として追加することができます。 詳細については、「[データ資産を管理する方法](data-catalog-how-to-manage.md)」を参照してください  

> [!NOTE]
> 所有権と管理は、Azure Data Catalog の Standard Edition でのみ使用できます。

## <a name="registration"></a>登録

登録は、データ資産メタデータをデータ ソースから抽出し、Azure Data Catalog サービスにコピーする操作です。 登録されたデータ資産は、注釈を付けて検索することができます。

## <a name="next-steps"></a>次のステップ

[クイック スタート:Azure データ カタログを作成する](data-catalog-get-started.md)