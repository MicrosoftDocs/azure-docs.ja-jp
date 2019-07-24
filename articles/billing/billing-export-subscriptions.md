---
title: Azure サブスクリプションの最上位レベルの情報をエクスポートする | Microsoft Docs
description: アカウントに関連付けられたすべての Azure サブスクリプション ID を表示する方法について説明します。
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 8a3d1a3a6b1dce1d729942715bbe5962837ff093
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57532135"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>最上位レベルのサブスクリプション情報のエクスポートと表示
ユーザーの資格情報に関連付けられたサブスクリプション ID のセットを表示する必要がある場合は、[Azure アカウント センターからサブスクリプション情報を含む .json ファイルをダウンロード](https://account.azure.com/subscriptions/download)します。

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

ダウンロードした .json ファイルには、次の情報が含まれています。
- 電子メール:アカウントに関連付けられているメール アドレス。
- Puid:請求先アカウントに関連付けられている一意の識別子。
- SubscriptionIds:アカウントに属するサブスクリプションの一覧。サブスクリプション ID 別に列挙されます。

### <a name="subscriptionsjson-sample"></a>subscriptions.json のサンプル

```json
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
```
