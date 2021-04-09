---
title: V3 API クエリ文字列パラメーター
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 14380dd781fc1eebfe7edb0a816ff8af0f2f17b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91309432"
---
V3 API クエリ文字列パラメーターには、以下が含まれます。

|Query parameter (クエリ パラメーター)|LUIS ポータル名|Type|Version|Default|目的|
|--|--|--|--|--|--|
|`log`|ログの保存|boolean|V2 および V3|false|ログ ファイルにクエリを格納します。 既定値は false です。|
|`query`|-|string|V3 のみ|既定値なし - GET 要求では必須|**V2 では**、予測される発話は `q` パラメーター内にあります。 <br><br>**V3 では**、この機能は `query` パラメーターで渡されます。|
|`show-all-intents`|すべての意図のスコアを含める|boolean|V3 のみ|false|すべての意図と対応するスコアを **prediction.intents** オブジェクトに返します。 意図は、親の `intents` オブジェクト内のオブジェクトとして返されます。 これにより、配列 `prediction.intents.give` 内で意図を探す必要はなく、プログラムによるアクセスが可能になります。 V2 では、これらは配列で返されていました。 |
|`verbose`|さらなるエンティティの詳細を含める|boolean|V2 および V3|false|**V2 では**、true に設定した場合、予測されたすべての意図が返されていました。 予測されたすべての意図が必要な場合は、V3 の `show-all-intents` パラメーターを使用します。<br><br>**V3 では**、このパラメーターではエンティティ予測のエンティティ メタデータの詳細のみが提供されます。  |
|`timezoneOffset`|-|string|V2|-|datetimeV2 エンティティに適用されるタイムゾーン。|
|`datetimeReference`|-|string|V3|-|datetimeV2 エンティティに適用される[タイムゾーン](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)。 V2 の `timezoneOffset` を置き換えます。|