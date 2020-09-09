---
title: Xamarin Android に関する考慮事項 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Xamarin Android を Microsoft Authentication Library for .NET (MSAL.NET) と共に使用する際の考慮事項について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 601a501114d754dc82991a3c19b977c0c63c5bc0
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165738"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>MSAL.NET と共に Xamarin Android を使用する際の考慮事項
この記事では、Xamarin Android を Microsoft Authentication Library for .NET (MSAL.NET) と共に使用する際の考慮事項について説明します。

## <a name="set-the-parent-activity"></a>親アクティビティを設定する

Xamarin Android では、対話後にトークンが戻るように親アクティビティを設定します。 次にコード例を示します。

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

MSAL 4.2 以降では、この機能を `PublicClientApplication` レベルで設定することもできます。 これを行うには、コールバックを使用します。

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

[CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin) を使用する場合、`PublicClientApplication` ビルダーのコードは次の例のようになります。

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>MSAL に確実に制御が戻るようにする 
認証フローの対話部分が終了したら、MSAL に確実に制御が戻るようにします。 Android では、`Activity` の `OnActivityResult` メソッドをオーバーライドします。 次に、`AuthenticationContinuationHelper` MSAL クラスの `SetAuthenticationContinuationEventArgs` メソッドを呼び出します。 

次に例を示します。

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

この行により、認証フローの対話部分の最後に、MSAL に確実に制御が戻ります。

## <a name="update-the-android-manifest"></a>Android マニフェストを更新する
*AndroidManifest.xml* ファイルには、次の値が含まれている必要があります。

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
```
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
     <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
     </intent-filter>
 </activity>
```

`android:host=` 値を、Azure portal で登録したパッケージ名に置き換えます。 `android:path=` 値を、Azure portal で登録したキー ハッシュに置き換えます。 署名ハッシュは、URL エンコードであっては "*なりません*"。 署名ハッシュの先頭にスラッシュ (`/`) があることを確認します。

または、*AndroidManifest.xml* を手動で編集するのではなく、[コード でアクティビティを作成](/xamarin/android/platform/android-manifest#the-basics)します。 コードでアクティビティを作成するには、まず、`Activity` 属性と `IntentFilter` 属性を含むクラスを作成します。 

XML ファイルの値を表すクラスの例を次に示します。

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>Xamarin.Forms 4.3.X マニフェスト

Xamarin.Forms 4.3.x では、`package` 属性を `com.companyname.{appName}` に設定するコードが *AndroidManifest.xml* 内に生成されます。 `DataScheme` を `msal{client_id}` として使用する場合は、値を `MainActivity.cs` 名前空間の値と同じになるように変更することをお勧めします。

## <a name="use-the-embedded-web-view-optional"></a>埋め込み Web ビューを使用する (省略可能)

既定では、MSAL.NET ではシステム Web ブラウザーが使用されます。 このブラウザーを使用すると、Web アプリケーションやその他のアプリを使用してシングル サインオン (SSO) を取得できます。 まれなケースですが、システムに埋め込み Web ビューを使用させることもできます。 

このコード例は、埋め込み Web ビューを設定する方法を示しています。

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

詳細については、「[MSAL.NET で Web ブラウザーを使用する](msal-net-web-browsers.md)」と「[Xamarin Android のシステム ブラウザーに関する考慮事項](msal-net-system-browser-android-considerations.md)」を参照してください。


## <a name="troubleshoot"></a>トラブルシューティング
新しい Xamarin.Forms アプリケーションを作成し、MSAL.NET NuGet パッケージに参照を追加できます。
ただし、既存の Xamarin.Forms アプリケーションを MSAL.NET プレビュー 1.1.2 以降にアップグレードすると、ビルドの問題が発生する可能性があります。

ビルドの問題のトラブルシューティングを行うには:

- 既存の MSAL.NET NuGet パッケージを MSAL.NET プレビュー 1.1.2 以降に更新します。
- Xamarin.Forms が自動的にバージョン 2.5.0.122203 に更新されたことを確認します。 必要に応じて、Xamarin.Forms をこのバージョンに更新します。
- Xamarin.Android.Support.v4 が自動的にバージョン 25.4.0.2 に更新されたことを確認します。 必要に応じて、バージョン 25.4.0.2 に更新します。
- すべての Xamarin.Android.Support パッケージでバージョン 25.4.0.2 がターゲットになっていることを確認します。
- アプリケーションをクリーニングしてリビルドします。
- Visual Studio で、並列プロジェクトのビルドの最大数を 1 に設定します。 これを行うには、 **[オプション]**  >  **[プロジェクトとソリューション]**  >  **[ビルドして実行]**  >  **[Maximum number of parallel projects builds]\(並列プロジェクトのビルドの最大数\)** を選択します。
- コマンド ラインからビルドしているときに、コマンドで `/m` を使用している場合は、コマンドからこの要素を削除してみてください。

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>エラー:現在のコンテキストに AuthenticationContinuationHelper という名前が存在しません

現在のコンテキストに `AuthenticationContinuationHelper` が存在しないことを示すエラーが発生した場合は、Visual Studio による Android.csproj* ファイルの更新が間違っている可能性があります。 場合によっては、 *\<HintPath>* ファイルパスに、*monoandroid90* の代わりに誤って *netstandard13* が含まれていることがあります。

この例には、正しいファイル パスが含まれています。

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>次のステップ

詳細については、[Microsoft ID プラットフォームを使用する Xamarin モバイル アプリケーション](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)のサンプルを参照してください。 次の表は、README ファイルの関連情報をまとめたものです。

| サンプル | プラットフォーム | 説明 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS、Android、UWP | MSAL を使用して、Azure AD 2.0 エンドポイントを介して Microsoft の個人用アカウントおよび Azure AD を認証する方法を示すシンプルな Xamarin.Forms アプリ。 このアプリでは、Microsoft Graph にアクセスする方法と、結果として生成されるトークンについても示されてます。 <br>![トポロジ](media/msal-net-xamarin-android-considerations/topology.png) |