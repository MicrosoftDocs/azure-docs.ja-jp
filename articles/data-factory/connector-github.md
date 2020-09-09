---
title: GitHub に接続する
description: GitHub を使用して Common Data Model エンティティ参照を指定する
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 0e17580524d6a67934aed83c6f745583b92e2422
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84771038"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>GitHub を使用して Common Data Model エンティティ参照を読み取る

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory の GitHub コネクタは、マッピング データ フローで [Common Data Model](format-common-data-model.md) 形式のエンティティ参照スキーマを受け取る目的でのみ使用されます。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

GitHub のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **GitHub** に設定する必要があります。 | はい
| userName | GitHub ユーザー名 | はい |
| password | GitHub パスワード | はい |

## <a name="next-steps"></a>次の手順

マッピング データ フローで[ソース データセット](data-flow-source.md)を作成します。