---
title: チュートリアル:エンティティ リンク アプリを構築する - C#
titlesuffix: Azure Cognitive Services
description: Entity Linking API を使用して、テキストを分析し、名前付きエンティティをナレッジ ベースの関連エントリにリンクします。
services: cognitive-services
author: DavidLiCIG
manager: nitinme
ms.service: cognitive-services
ms.subservice: entity-linking-intelligence
ms.topic: tutorial
ms.date: 07/06/2016
ms.author: davl
ms.openlocfilehash: fc1bdd5c6ad4829e22af9922c6749e60f842abaf
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594034"
---
# <a name="tutorial-build-an-entity-linking-app-with-c"></a>チュートリアル:C# を使用してエンティティ リンク アプリを構築する

Microsoft の Entity Linking は、テキストを分析し、名前付きエンティティをナレッジ ベース内の関連エントリにリンクする自然言語処理ツールです。 

このチュートリアルでは、Entity Linking クライアント ライブラリを NuGet パッケージとして使用して、エンティティのリンクを探索します。 

### <a name="Prerequisites">前提条件</a>

- Visual Studio 2015
- Microsoft Cognitive Services API キー
- クライアント ライブラリと例を入手する
- Microsoft Entity Linking NuGet パッケージ

Entity Linking Intelligence Service API クライアント ライブラリは [SDK](https://www.github.com/microsoft/cognitive-entitylinking-windows) を介してダウンロードできます。 ダウンロードした ZIP ファイルは、任意のフォルダーに抽出する必要があります、多くのユーザーは Visual Studio 2015 フォルダーを選択します。

### <a name="step-1-subscribe-entity-linking-intelligence-service-and-get-your-own-key">手順 1:Entity Linking Intelligence Service にサブスクライブしてキーを取得する</a>
Entity Linking Intelligence Service を使用する前に、API キーにサインアップする必要があります。 [サブスクリプション](https://www.microsoft.com/cognitive-services/en-us/sign-up)に関するページを参照してください。 プライマリ キーとセカンダリ キーの両方をこのチュートリアルで利用できます。

### <a name="step-2-create-a-new-project-in-visual-studio">手順 2: Visual Studio で新しいプロジェクトを作成する</a>

Visual Studio で新しいプロジェクトを作成することから始めましょう。 まず、スタート メニューから Visual Studio 2015 を起動します。 次に、**[インストール済み]、[テンプレート]、[Visual C#]、[Windows ユニバーサル] の順に選択し、プロジェクト テンプレートとして [空のアプリ]** を選択して、新しいプロジェクトを作成します。

 ![ユニバーサル アプリを作成する](./Images/CreateUWP.png)

### <a name="step-3-add-the-entity-linking-nuget-package-to-your-project">手順 3:プロジェクトに Entity Linking NuGet パッケージを追加する</a>

Cognitive Services の Entity Linking は NuGet.org パッケージとしてリリースされており、使用する前にインストールする必要があります。
プロジェクトに追加するには、**[ソリューション エクスプローラー]** タブに移動し、プロジェクトを右クリックして **[NuGet パッケージの管理]** を選択します。

まず、**[NuGet パッケージ マネージャー]** ウィンドウで、右上の **[パッケージ ソース]** として NuGet.org を選択します。 左上の **[参照]** を選択し、検索ボックスに「ProjectOxford.EntityLinking」と入力します。 **Microsoft.ProjectOxford.EntityLinking** NuGet パッケージを選択し、**[インストール]** をクリックします。

次に、Newtonsoft.Json を探してインストールします。 変更を確認するプロンプトが表示されたら、**[OK]** をクリックします。 EntityLinking のライセンス条項が表示された場合は、**[同意する]** をクリックします。

以上で Entity Linking がアプリケーションの一部としてインストールされました。 ソリューション エクスプローラーでプロジェクトの一部として **Microsoft.ProjectOxford.EntityLinking** の参照が存在することで、インストールを確認できます。

 ![プロジェクトに含まれる NuGet ライブラリ](./Images/NugetLibraryInProject.png)
 
### <a name="step-4-add-an-input-and-output-text-block-to-your-apps-xaml">手順 4:入力と出力のテキスト ブロックをアプリの XAML に追加する</a>
**ソリューション エクスプローラー**で **MainPage.xaml** に移動し、ファイルをダブルクリックします。新しいウィンドウにファイルが表示されます。 **[デザイナー]** タブの **[XAML]** ボタンをダブルクリックすると、**ビジュアルデザイナー**が非表示になり、コード ビューのすべての領域が確保されるので便利です。

 ![プロジェクトに含まれる NuGet ライブラリ](./Images/UWPMainPage.png)
 
テキスト サービスとして、機能を視覚化する最適な方法は、入力と出力のテキスト ブロックを作成することです。 これを行うには、**[グリッド]** に次の XAML を追加します。 このコードで、入力テキスト ボックス、出力テキスト ブロック、および開始ボタンという 3 つのコンポーネントが追加されます。
 
 ```XAML
 <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="*"/>
        <RowDefinition Height="*" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <TextBox x:Name="inputBox" Grid.Row="0" TextWrapping="Wrap" Text="Enter a paragraph" Margin="10" AcceptsReturn="True" />
    <TextBlock x:Name="outputBlock" Grid.Row="1" TextWrapping="Wrap" Text="Result will be here" Margin="10" />
    <Button x:Name="button" Grid.Row="2" Content="Get Result" />
</Grid>
 ```
 
### <a name="step-5-proceed-to-add-entity-linking-intelligence-service">手順 5:Entity Linking Intelligence Service の追加に進む</a>
 
これでユーザー インターフェイスが作成されました。 Entity Linking サービスを使用する前に、button_Click ハンドラーを追加する必要があります。 **ソリューション エクスプローラー**から **MainPage.xaml** を開きます。 ボタンの最後に button_Click ハンドラーを追加します。
 
 ```XAML
 <Button x:Name="button" Grid.Row="2" Content="Get Result" Click="button_Click" />
 ```
 
button_Click ハンドラーはコード内に実装する必要があります。 button_Click を実装するには、**ソリューション エクスプローラー**から **MainPage.xaml.cs** を開きます。 EntityLinkingServiceClient は、Entity Linking の応答を取得するためのラッパーです。 EntityLinkingServiceClient のコンストラクター引数は、Cognitive Services のサブスクリプション キーです。 Entity Linking サービスを呼び出すには、**手順 1** で取得したサブスクリプション キーを貼り付けます。 

Entity Linking サービスを使用して応答に "wikipediaId" を追加するコード例を次に示します。 
 
 ```csharp
 private async void button_Click(object sender, RoutedEventArgs e)
{
    var text = this.inputBox.Text;
    var client = new EntityLinkingServiceClient("Your subscription key","https://api.labs.cognitive.microsoft.com");
    var linkResponse = await client.LinkAsync(text);
    var result = string.Join(", ", linkResponse.Select(i => i.WikipediaID).ToList());
    this.outputBlock.Text = result;
}
 ```
 
これで、初めての自然言語処理 Entity Linking アプリを実行する準備が整いました。 **F5 キー**を押して、アプリケーションをコンパイルして起動します。 テキスト スニペットまたは段落を入力ボックスに貼り付けます。 [Get Result]\(結果の取得\) ボタンを押して、出力ブロック内の特定されたエンティティを観察します。
 
 ![UWP サンプル結果](./Images/DemoCodeResult.png)
 
### <a name="summary">まとめ</a>
 
このチュートリアルでは、数行の C# および XAML コードで Entity Linking Intelligence Service クライアント ライブラリを利用するアプリケーションの作成方法を学びました。 

