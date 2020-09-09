---
title: ユーザー設定を格納する
titleSuffix: Azure Cognitive Services
description: この記事では、ユーザーの設定を格納する方法について説明します。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: beb053551dc1fa28672c488b31dfb29ca3b53651
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486875"
---
# <a name="how-to-store-user-preferences"></a>ユーザー設定を格納する方法

この記事では、ユーザーの設定を格納する方法を示します。 この機能は、Cookie の使用が望ましくない場合や実現できない場合に、ユーザーの設定を保存するための代替手段として使用されることを目的としています。

## <a name="how-to-enable-storing-user-preferences"></a>ユーザー設定の格納を有効にする方法

`options` パラメーターには `onPreferencesChanged` コールバックが含まれています。 ユーザーが設定を変更するたびに、この関数が呼び出されます (たとえばテキスト サイズ、テーマの色、フォントなどを変更した場合)。 `value` パラメーターには、ユーザーの現在の設定を表す文字列が含まれています。 任意のメカニズムを使用して、この文字列を格納できます。

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>ユーザー設定をイマーシブ リーダーに読み込む方法

`preferences` パラメーターを使用して、ユーザーの設定をイマーシブ リーダーに渡します。 ユーザーの設定を格納して読み込む単純な例を次に示します。

```typescript
let userPreferences = null;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
    }
};
```

## <a name="next-steps"></a>次のステップ

* [イマーシブ リーダー SDK リファレンス](./reference.md)を参照する