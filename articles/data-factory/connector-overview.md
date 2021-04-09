---
title: Azure Data Factory コネクタの概要
description: Data Factory でサポートされるコネクタについて説明します。
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: jingwang
ms.openlocfilehash: cfd3376174ec0f7789389988245f7377b9896a00
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015924"
---
# <a name="azure-data-factory-connector-overview"></a>Azure Data Factory コネクタの概要

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory では、コピー、データ フロー、ルックアップ、メタデータの取得、および削除アクティビティを介して、次のデータ ストアおよび形式がサポートされます。 各データ ストアをクリックすると、サポートされている機能と対応する構成の詳細を確認できます。

## <a name="supported-data-stores"></a>サポートされているデータ ストア

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="integrate-with-more-data-stores"></a>より多くのデータ ストアとの統合

Azure Data Factory は、前述のリストよりも広範なデータ ストアに対して使用できます。 Azure Data Factory の組み込みコネクタの一覧に含まれていないデータ ストアとの間でデータを移動する必要がある場合は、次の拡張可能なオプションがあります。
- データベースとデータ ウェアハウスについては、通常、対応する ODBC ドライバを見つけることができ、これと共に[汎用 ODBC コネクタ](connector-odbc.md)を使用することができます。
- SaaS アプリケーションの場合:
    - RESTful API が提供されている場合は、[汎用 REST コネクタ](connector-rest.md)を使用できます。
    - OData フィードがある場合は、[汎用 OData コネクタ](connector-odata.md)を使用できます。
    - SOAP API が提供されている場合は、[汎用 HTTP コネクタ](connector-http.md)を使用できます。
    - ODBC ドライバーがある場合は、[汎用 ODBC コネクタ](connector-odbc.md)を使用できます。
- その他の場合は、ADF でサポートされているデータ ストア (Azure Blob/File/FTP/SFTP など) へのデータの読み込み、またはこれらのデータ ストアとしてのデータの公開を行うことができるかどうかを確認し、ADF でそこから取得できるようにします。 [Azure Function](control-flow-azure-function-activity.md)、[カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)、[Databricks](transform-data-databricks-notebook.md)/[HDInsight](transform-data-using-hadoop-hive.md)、[Web アクティビティ](control-flow-web-activity.md)などを使用して、カスタム データ読み込みメカニズムを呼び出すことができます。

## <a name="supported-file-formats"></a>サポートされるファイル形式

Azure Data Factory では次のファイル形式がサポートされます。 形式ベースの設定については、各記事を参照してください。

- [Avro 形式](format-avro.md)
- [バイナリ形式](format-binary.md)
- [Common Data Model 形式](format-common-data-model.md)
- [区切りテキスト形式](format-delimited-text.md)
- [差分形式](format-delta.md)
- [Excel 形式](format-excel.md)
- [JSON 形式](format-json.md)
- [ORC 形式](format-orc.md)
- [Parquet 形式](format-parquet.md)
- [XML 形式](format-xml.md)

## <a name="next-steps"></a>次のステップ

- [コピー アクティビティ](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [メタデータの取得アクティビティ](control-flow-get-metadata-activity.md)
- [削除アクティビティ](delete-activity.md)
