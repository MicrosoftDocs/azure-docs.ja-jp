---
title: Xamarin Android のシステム ブラウザーに関する考慮事項 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for .NET (MSAL.NET) で Xamarin Android 上のシステム ブラウザーを使用する場合の考慮事項について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4230a194fb18587a209c100a39b0924e6170502d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063469"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>MSAL.NET を使用する場合の Xamarin Android のシステム ブラウザーに関する考慮事項

この記事では、Microsoft Authentication Library for .NET (MSAL.NET) で Xamarin Android 上のシステム ブラウザーを使用する場合に考慮すべきことについて説明します。

MSAL.NET 2.4.0 Preview 以降では、MSAL.NET は Chrome 以外のブラウザーをサポートしています。 認証のために、Android デバイスに Chrome をインストールする必要がなくなりました。

カスタム タブをサポートするブラウザーを使用することをお勧めします。 これらのブラウザーの例をいくつか次に示します。

| カスタム タブをサポートするブラウザー | パッケージ名 |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Brave | com.brave.browser|

Microsoft のテストでは、カスタム タブをサポートするブラウザーが特定されただけでなく、カスタム タブをサポートしていないいくつかのブラウザーも認証用に使用できることが示されました。 これらのブラウザーには、Opera、Opera Mini、InBrowser、Maxthon などがあります。 

## <a name="tested-devices-and-browsers"></a>テストしたデバイスとブラウザー
次の表に、認証の互換性をテストしたブラウザーとデバイスの一覧を示します。

| Device | Browser     |  結果  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome\* | 合格|
| Huawei/One+ | Edge\* | 合格|
| Huawei/One+ | Firefox\* | 合格|
| Huawei/One+ | Brave\* | 合格|
| One+ | Ecosia\* | 合格|
| One+ | Kiwi\* | 合格|
| Huawei/One+ | Opera | 合格|
| Huawei | OperaMini | 合格|
| Huawei/One+ | InBrowser | 合格|
| One+ | Maxthon | 合格|
| Huawei/One+ | DuckDuckGo | ユーザーによって認証が取り消された|
| Huawei/One+ | UC ブラウザー | ユーザーによって認証が取り消された|
| One+ | Dolphin | ユーザーによって認証が取り消された|
| One+ | CM ブラウザー | ユーザーによって認証が取り消された|
| Huawei/One+ | 何もインストールされていない | AndroidActivityNotFound 例外|

\* カスタム タブをサポートする

## <a name="known-issues"></a>既知の問題

ユーザーのデバイス上に有効になっているブラウザーがない場合、MSAL.NET は `AndroidActivityNotFound` 例外をスローします。  
  - **対応策**:ユーザーに自分のデバイスのブラウザーを有効にするように依頼します。 カスタム タブをサポートするブラウザーを推奨します。

認証が失敗した場合 (たとえば、認証が DuckDuckGo で起動する場合)、MSAL.NET は `AuthenticationCanceled MsalClientException` を返します。 
  - **根本原因**:カスタム タブをサポートするブラウザーがデバイスで有効になっていません。 認証を完了できないブラウザーで認証が開始されました。 
  - **対応策**:ユーザーに自分のデバイスのブラウザーを有効にするように依頼します。 カスタム タブをサポートするブラウザーを推奨します。

## <a name="next-steps"></a>次のステップ
詳細情報とコード例については、[埋め込み Web ブラウザーと Xamarin Android のシステム ブラウザーのどちらかを選択する](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)ことに関する記事、および「[埋め込み Web UI とシステム Web UI の比較](msal-net-web-browsers.md#embedded-vs-system-web-ui)」を参照してください。  