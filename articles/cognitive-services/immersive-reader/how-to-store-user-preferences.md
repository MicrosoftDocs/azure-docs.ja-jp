---
title: ユーザー設定を格納する
titleSuffix: Azure Cognitive Services
description: この記事では、ユーザーの設定を格納する方法について説明します。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 16ecd2166604d29fbc2242229f625b30ffd684e5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617694"
---
# <a name="how-to-store-user-preferences"></a>ユーザー設定を格納する方法

この記事では、[-preferences](./reference.md#options) および [-onPreferencesChanged](./reference.md#options) イマーシブ リーダー SDK オプションを使用して、ユーザーの UI 設定 (以前は **ユーザー設定** と呼ばれていたもの) を格納する方法について説明します。

[CookiePolicy](./reference.md#cookiepolicy-options) SDK オプションを "*有効*" に設定すると、イマーシブ リーダー アプリケーションによって **ユーザー設定** (テキスト サイズ、テーマの色、フォントなど) が Cookie に格納されます。これは、特定のブラウザーおよびデバイスにローカルなものです。 ユーザーが同じブラウザーおよびデバイスでイマーシブ リーダーを起動するたびに、そのデバイスでの最後のセッションのユーザーの設定で開きます。 しかし、ユーザーが別のブラウザーまたはデバイスでイマーシブ リーダーを開くと、設定は最初にイマーシブ リーダーの既定の設定で構成され、ユーザーは設定をもう一度行う必要があります。使用するデバイスごとに同様のことが必要です。 `-preferences` および `-onPreferencesChanged` イマーシブ リーダー SDK オプションを使用することにより、アプリケーションによってさまざまなブラウザーおよびデバイスでユーザーの設定をローミングできるようになり、ユーザーがどこでアプリケーションを使用しても一貫性のあるエクスペリエンスが得られます。

まず、イマーシブ リーダー アプリケーションの起動時に `-onPreferencesChanged` コールバック SDK オプションを指定すると、イマーシブ リーダー セッション中にユーザーが設定を変更するたびに、イマーシブ リーダーから `-preferences` 文字列がホスト アプリケーションに送り返されます。 すると、ユーザー設定が、ホスト アプリケーションの独自のシステムに格納されます。 その後、同じユーザーがイマーシブ リーダーを再び起動すると、ホスト アプリケーションによってそのユーザーの設定がストレージから取得され、イマーシブ リーダー アプリケーションの起動時に `-preferences` 文字列 SDK オプションとして指定されます。こうして、ユーザーの設定が復元されます。

この機能は、Cookie の使用が望ましくない場合や実現できない場合に、**ユーザー設定** を格納するための代替手段として使用することもできます。

> [!CAUTION]
> **重要** イマーシブ リーダー アプリケーションとの間で送受信される `-preferences` の文字列の値を、プログラムで変更しようとしないでください。予期しない動作が発生し、顧客のユーザー エクスペリエンスが低下する可能性があります。 ホスト アプリケーションによってカスタム値を割り当てたり、`-preferences` 文字列を操作したりしないでください。 `-preferences` 文字列オプションを使用する場合は、`-onPreferencesChanged` コールバック オプションから返された正確な値のみを使用してください。

## <a name="how-to-enable-storing-user-preferences"></a>ユーザー設定の格納を有効にする方法

イマーシブ リーダー SDK [launchAsync](./reference.md#launchasync) `options` パラメーターには `-onPreferencesChanged` コールバックが含まれます。 ユーザーが設定を変更すると、この関数が必ず呼び出されます。 `value` パラメーターには、ユーザーの現在の設定を表す文字列が含まれています。 その後、この文字列はそのユーザー用としてホスト アプリケーションによって格納されます。

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>ユーザー設定をイマーシブ リーダーに読み込む方法

`-preferences` オプションを使用して、ユーザーの設定をイマーシブ リーダーに渡します。 ユーザーの設定を格納して読み込む単純な例を次に示します。

```typescript
const storedUserPreferences = localStorage.getItem("USER_PREFERENCES");
let userPreferences = storedUserPreferences === null ? null : storedUserPreferences;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
        localStorage.setItem("USER_PREFERENCES", userPreferences);
    }
};
```

## <a name="next-steps"></a>次のステップ

* [イマーシブ リーダー SDK リファレンス](./reference.md)を参照する