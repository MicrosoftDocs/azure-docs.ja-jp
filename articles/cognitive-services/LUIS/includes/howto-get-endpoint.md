---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 01d102de24185eba789e041d8534d6fdce8c260b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "87375379"
---
**[Azure リソース]** ページ (左側メニュー) の **[管理]** セクション (右上メニュー) で、**サンプルクエリ**の URL をコピーし、新しいブラウザー タブに貼り付けます。

エンドポイント URL は次の形式になります。YOUR-CUSTOM-SUBDMAIN、APP-ID、KEY-ID は、自分のカスタム サブドメイン、アプリ ID、エンドポイント キーに置き換えます。

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```