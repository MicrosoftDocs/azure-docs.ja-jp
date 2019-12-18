---
title: Xamarin Android のシステム ブラウザーに関する考慮事項 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: .NET 用 Microsoft 認証ライブラリ (MSAL.NET) で Xamarin Android 上のシステム ブラウザーを使用する場合の固有の考慮事項について説明します。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d3ea2554fac8654b052e3e38633af23e7c778b3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915461"
---
#  <a name="xamarin-android-system-browser-considerations-with-msalnet"></a>MSAL.NET での Xamarin Android のシステム ブラウザーに関する考慮事項

この記事では、.NET 用 Microsoft 認証ライブラリ (MSAL.NET) で Xamarin Android 上のシステム ブラウザーを使用する場合の固有の考慮事項について説明します。

MSAL.NET 2.4.0-preview 以降、MSAL.NET では Chrome 以外のブラウザーがサポートされていて、認証のために Android デバイスに Chrome をインストールする必要がなくなりました。

次のような、カスタム タブをサポートするブラウザーを使用することをお勧めします。

| カスタム タブをサポートするブラウザー | パッケージ名 |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Brave | com.brave.browser|

テストによると、カスタム タブをサポートするブラウザーだけでなく、カスタム タブをサポートしていないいくつかのブラウザー (Opera、Opera Mini、InBrowser、および Maxthon) も認証用に使用できます。 詳細については、[テスト結果の表](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested)をご覧ください。

## <a name="known-issues"></a>既知の問題

- ユーザーのデバイス上に有効になっているブラウザーがない場合、MSAL.NET は `AndroidActivityNotFound` 例外をスローします。 
  - **対応策**:自分のデバイス上のブラウザー (可能であればカスタム タブをサポートするブラウザー) を有効にする必要があることを、ユーザーに通知してください。

- 認証が失敗した場合 (たとえば、 認証が DuckDuckGo で起動する場合)、MSAL.NET は `AuthenticationCanceled MsalClientException` を返します。 
  - **根本原因**:カスタム タブをサポートするブラウザーが、デバイスで有効になっていませんでした。 認証は別のブラウザーで起動し、これにより認証を完了できませんでした。 
  - **対応策**:自分のデバイス上のブラウザー (可能であればカスタム タブをサポートするブラウザー) をインストールする必要があることを、ユーザーに通知してください。

## <a name="devices-and-browsers-tested"></a>テストしたデバイスとブラウザー
次の表に、テストしたブラウザーとデバイスの一覧を示します。

| | ブラウザー&ast;     |  結果  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome&ast; | 合格|
| Huawei/One+ | Edge&ast; | 合格|
| Huawei/One+ | Firefox&ast; | 合格|
| Huawei/One+ | Brave&ast; | 合格|
| One+ | Ecosia&ast; | 合格|
| One+ | Kiwi&ast; | 合格|
| Huawei/One+ | Opera | 合格|
| Huawei | OperaMini | 合格|
| Huawei/One+ | InBrowser | 合格|
| One+ | Maxthon | 合格|
| Huawei/One+ | DuckDuckGo | ユーザーにより認証がキャンセルされる|
| Huawei/One+ | UC ブラウザー | ユーザーにより認証がキャンセルされる|
| One+ | Dolphin | ユーザーにより認証がキャンセルされる|
| One+ | CM ブラウザー | ユーザーにより認証がキャンセルされる|
| Huawei/One+ | 何もインストールされていない | AndroidActivityNotFound ex|

&ast; カスタム タブをサポートする

## <a name="next-steps"></a>次の手順
コード スニペットと Xamarin Android でのシステム ブラウザーの使用に関する追加情報については、この[ガイド](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)をご覧ください。  