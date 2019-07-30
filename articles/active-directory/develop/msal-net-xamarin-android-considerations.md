---
title: Xamarin Android に関する考慮事項 (Microsoft Authentication Library for .NET) | Azure
description: .NET 用 Microsoft 認証ライブラリ (MSAL.NET) で Xamarin Android を使用する場合の固有の考慮事項について説明します。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 357c83cfd0ae3fed8b13419e72f50fcb90c04186
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550652"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>MSAL.NET での Xamarin Android に固有の考慮事項
この記事では、.NET 用 Microsoft 認証ライブラリ (MSAL.NET) で Xamarin Android を使用する場合の固有の考慮事項について説明します。

この記事は MSAL.NET 3.x 用です。 MSAL.NET 2.x に関心がある場合は、[MSAL.NET 2.x ので Xamarin Android の詳細](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics-2x)に関するページをご覧ください。

## <a name="set-the-parent-activity"></a>親アクティビティを設定する

Xamarin.Android では、対話が行われたらトークンが戻るように親アクティビティを設定する必要があります。

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>認証フローの対話部分が終了したら確実に制御が MSAL に戻るようにする
Android では、`Activity` の `OnActivityResult` メソッドをオーバーライドして、AuthenticationContinuationHelper MSAL クラスの SetAuthenticationContinuationEventArgs メソッドを呼び出す必要があります。

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```
この行により、認証フローの対話部分が終了したら、制御が MSAL に戻ることが保証されます。

## <a name="update-the-android-manifest"></a>Android マニフェストを更新する
`AndroidManifest.xml` には次の値が含まれている必要があります。
```csharp
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msal{client_id}" android:host="auth" />
         </intent-filter>
</activity>
```

## <a name="use-the-embedded-web-view-optional"></a>埋め込み Web ビューを使用する (省略可能)

既定では、MSAL.NET はシステム Web ブラウザーを使用します。これにより、Web アプリケーションやその他のアプリとの間で SSO が可能になります。 まれなケースですが、埋め込み Web ビューの使用を指定する必要が生じる場合があります。 詳細については、[MSAL.NET での Web ブラウザーの使用](msal-net-web-browsers.md)に関するページおよび [Android システム ブラウザー](msal-net-system-browser-android-considerations.md)に関するページを参照してください。

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>トラブルシューティング
新しい Xamarin.Forms アプリケーションを作成して、MSAL.Net NuGet パッケージへの参照を追加すると適切に機能します。
ただし、既存の Xamarin.Forms アプリケーションを MSAL.NET プレビュー 1.1.2 以降にアップグレードすると、ビルドの問題が発生する可能性があります。

これらの問題をトラブルシューティングするには、次の操作を行う必要があります。
- 既存の MSAL.NET NuGet パッケージを MSAL.NET プレビュー 1.1.2 以降に更新します
- Xamarin.Forms が自動的にバージョン 2.5.0.122203 に更新されたことを確認します (更新されていない場合は、このバージョンに更新してください)
- Xamarin.Android.Support.v4 が自動的にバージョン 25.4.0.2 に更新されたことを確認します (更新されていない場合は、このバージョンに更新してください)
- すべての Xamarin.Android.Support パッケージがバージョン 25.4.0.2 をターゲットにしている必要があります
- クリーン/リビルド
- Visual Studio で並列プロジェクトの最大ビルド数を 1 に設定してください ([オプション]->[プロジェクトとソリューション]->[ビルドおよび実行]->[Maximum number of parallel projects builds]\(並行プロジェクトのビルドの最大数\))
- または、コマンド ラインからビルドを行っていて、コマンドで /m を使用している場合は、これを削除してください。


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>エラー:名前 "AuthenticationContinuationHelper" は現在のコンテキストに存在しません

これは、Visual Studio が Android.csproj* ファイルを正しく更新していないために発生した可能性があります。 場合によっては、 **\<HintPath>** ファイルパスに、**monoandroid90** の代わりに誤って netstandard13 が含まれていることがあります。

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>次の手順

詳細とサンプルについては、次に示すサンプルの readme.md ファイルの「[Android Specific Considerations (Android に固有の考慮事項)](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)」の段落を参照してください。

| サンプル | プラットフォーム | 説明 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | AADD v2.0 エンドポイント経由で MSA と Azure AD を認証し、結果のトークンで Microsoft Graph にアクセスするための MSAL の使用方法を示す、単純な Xamarin Forms アプリ。 <br>![トポロジ](media/msal-net-xamarin-android-considerations/topology.png) |