---
title: "Azure AD v2 Windows デスクトップの概要 - セットアップ | Microsoft Docs"
description: "Windows デスクトップ .NET (XAML) アプリケーションで、アクセス トークンを必要とする API を Azure Active Directory v2 エンドポイントから呼び出す方法"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 4065727aef04d7969d438c6ef79127bb44568be1
ms.contentlocale: ja-jp


---

## <a name="set-up-your-project"></a>プロジェクトの設定

このセクションでは、新しいプロジェクトの作成方法について順を追って説明し、Windows デスクトップ .NET アプリケーション (XAML) に *"Microsoft でサインイン"* を統合して、トークンを必要とする Web API でクエリを実行できるようにする方法を示します。

このガイドで作成するアプリケーションでは、結果を画面にグラフ化して示すボタンとサインアウト ボタンが表示されます。

> 代わりにこのサンプルの Visual Studio プロジェクトをダウンロードすることもできます。 [プロジェクトをダウンロード](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip)したら[構成](#create-an-application-express)手順に進み、実行前にコード サンプルを構成します。


### <a name="create-your-application"></a>アプリケーションの作成
1. Visual Studioで、[`File`] \(ファイル)  >  [`New`] \(新規)  >  [`Project`] \(プロジェクト) を選択します。<br/>
2. *"テンプレート"* から `Visual C#` を選択します。
3. [`WPF App`] \(WPF アプリ) を選択します (または、Visual Studio のバージョンによっては [*WPF アプリケーション*])。

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>プロジェクトへの Microsoft Authentication Library (MSAL) の追加
1. Visual Studio で、[`Tools`] \(ツール)  >  [`Nuget Package Manager`] \(NuGet パッケージ マネージャー)  >  [`Package Manager Console`] \(パッケージ マネージャー コンソール) を選択します。
2. [パッケージ マネージャー コンソール] ウィンドウで、以下をコピーして貼り付けます。

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

> 上記パッケージにより、Microsoft Authentication Library (MSAL) がインストールされます。 MSAL は、Azure Active Directory v2 によって保護される API へのアクセスで使用するユーザー トークンの取得、キャッシュ、更新を処理します。

## <a name="add-the-code-to-initialize-msal"></a>MSAL を初期化するコードの追加
この手順を使用すると、トークンの処理など MSAL ライブラリとのやり取りを処理するクラスを作成できます。

1. `App.xaml.cs` ファイルを開き、次の手順に従ってクラスに MSAL ライブラリの参照を追加します。

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
App クラスを以下のように更新します。
</li>
</ol>

```csharp
public partial class App : Application
{
    //Below is the clientId of your app registration. 
    //You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";

    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

}
```

## <a name="create-your-applications-ui"></a>アプリケーションの UI の作成
以下のセクションでは、アプリケーションで、Microsof Graph のような保護されたバックエンド サーバーにクエリを実行する方法を示します。 MainWindow.xaml ファイルは、プロジェクト テンプレートの一部として自動的に作成されます。 このファイルを開き、以下の手順を実行します。

アプリケーションの `<Grid>` を以下のように置き換えます。

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

