---
title: 固有表現認識でサポートされるカテゴリ
titleSuffix: Azure Cognitive Services
description: Text Analytics API でサポートされているエンティティのカテゴリについて説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: f66ed9a7d26a0c2d7def7d17820379b8e3460460
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373073"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Text Analytics API v3 でサポートされているエンティティのカテゴリ

この記事では、[固有表現認識 ](how-tos/text-analytics-how-to-entity-linking.md) (NER) から取得できるエンティティのカテゴリを紹介しています。 NER v3.1 のプレビューも提供されており、そちらには、個人情報 (`PII`) と医療情報 (`PHI`) を検出する機能が備わっています。 また、 **[正常性]** タブをクリックして、Text Analytics で正常性に対してサポートされているカテゴリの一覧を表示します。

## <a name="entity-categories"></a>エンティティのカテゴリ

#### <a name="general"></a>[全般](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="personal"></a>[個人](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[正常性](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>次のステップ

* [Text Analytics で固有表現抽出
を使用する方法](how-tos/text-analytics-how-to-entity-linking.md)
