---
title: Azure Data Factory コネクタの概要
description: Data Factory でサポートされるコネクタについて説明します。
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 334d5b5113dba17c5abc2b4f2520bde0d16e4c06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87007447"
---
# <a name="azure-data-factory-connector-overview"></a>Azure Data Factory コネクタの概要

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory では、コピー、データ フロー、ルックアップ、メタデータの取得、および削除アクティビティを介して、次のデータ ストアおよび形式がサポートされます。 各データ ストアをクリックすると、サポートされている機能と対応する構成の詳細を確認できます。

## <a name="supported-data-stores"></a>サポートされているデータ ストア

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

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
