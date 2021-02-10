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
ms.date: 01/22/2021
ms.author: aahi
ms.openlocfilehash: 883c5a20612f4dab44c0d06776ee287b27174ab9
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097305"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Text Analytics API v3 でサポートされているエンティティのカテゴリ

この記事では、[固有表現認識 ](how-tos/text-analytics-how-to-entity-linking.md) (NER) から取得できるエンティティのカテゴリを紹介しています。 NER では、予測モデルを実行して、入力ドキュメントから固有表現を識別して分類します。

NER v3.1 のプレビューも提供されており、そちらには、個人情報 (`PII`) と医療情報 (`PHI`) を検出する機能が備わっています。 また、 **[正常性]** タブをクリックして、Text Analytics で正常性に対してサポートされているカテゴリの一覧を表示します。 

[移行ガイド](migration-guide.md?tabs=named-entity-recognition)で、バージョン 2.1 で返される種類の一覧を確認できます。

## <a name="entity-categories"></a>エンティティのカテゴリ

#### <a name="general"></a>[全般](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[PII](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[正常性](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

**_

## <a name="next-steps"></a>次の手順

_ [Text Analytics で固有表現認識を使用する方法](how-tos/text-analytics-how-to-entity-linking.md)
