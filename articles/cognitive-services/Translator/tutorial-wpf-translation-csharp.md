---
title: チュートリアル:WPF (C#) を使って翻訳アプリを作成する - Translator Text API
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、単一サブスクリプション キーを使ってテキスト翻訳、言語検出、スペル チェックに Cognitive Service API シリーズを利用する Windows Presentation Foundation (WPF) アプリを作成します。 この演習では、Translator Text API と Bing Spell Check API の機能を使用する方法について説明します。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 36d85e11133e7197212ae1b37609628689b68a13
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657933"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>チュートリアル:WPF を使って翻訳アプリを作成する

このチュートリアルでは、単一サブスクリプション キーを使ってテキスト翻訳、言語検出、スペル チェックに Azure Cognitive Service API を利用する [Windows Presentation Foundation (WPF)](https://docs.microsoft.com/visualstudio/designers/getting-started-with-wpf?view=vs-2017) アプリを作成します。 具体的には、アプリから Translator Text と [Bing Spell Check](https://azure.microsoft.com/services/cognitive-services/spell-check/) の API を呼び出します。

WPF とは デスクトップ クライアント アプリを作成する UI フレームワークです。 WPF 開発プラットフォームでは、アプリ モデル、リソース、コントロール、グラフィックス、レイアウト、データ バインディング、ドキュメント、セキュリティなど、広範なアプリ開発機能がサポートされています。 .NET Framework のサブセットなので、以前に .NET Framework と ASP.NET または Windows フォームを使ってアプリを作成したことがある場合、そのプログラミングの経験を活かすことができます。 WPF では、Extensible Application Markup Language (XAML) を使って、アプリ プログラミングの宣言型モデルを提供します。これについては、後のセクションで確認します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Visual Studio で WPF プロジェクトを作成する
> * アセンブリと NuGet パッケージをプロジェクトに追加する
> * XAML を使用してアプリの UI を作成する
> * Translator Text API を使用して、言語の取得、テキストの翻訳、ソース言語の検出を行う
> * Bing Spell Check API を使用して、入力の検証、翻訳精度の向上を行う
> * WPF アプリを実行する

### <a name="cognitive-services-used-in-this-tutorial"></a>このチュートリアルで使用する Cognitive Services

この一覧には、このチュートリアルで使用する Cognitive Services が含まれています。 リンクをたどると、各機能の API リファレンスを閲覧できます。

| Service | 機能 | 説明 |
|---------|---------|-------------|
| Translator Text | [言語の取得](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) | テキスト翻訳がサポートされている言語の完全な一覧を取得します。 |
| Translator Text | [Translate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) | 60 を超す言語にテキストを翻訳できます。 |
| Translator Text | [Detect](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect) | 入力テキストの言語を検出します。 検出の信頼度スコアが含まれます。 |
| Bing Spell Check | [スペル チェック](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | スペル ミスを修正して、翻訳精度を向上させます。 |

## <a name="prerequisites"></a>前提条件

続行する前に、次が必要です。

* Azure Cognitive Services サブスクリプション。 [Cognitive Services キーの取得](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#multi-service-subscription)。
* Windows マシン
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) - Community または Enterprise

> [!NOTE]
> このチュートリアルでは、米国西部リージョンにサブスクリプションを作成することをお勧めします。 それ以外の場合、この演習では、コード内のエンドポイントとリージョンを変更する必要があります。  

## <a name="create-a-wpf-app-in-visual-studio"></a>Visual Studio で WPF アプリを作成する

最初に実行する必要があるのは、Visual Studio で自分のプロジェクトを設定することです。

1. Visual Studio を開きます。 次に、 **[ファイル] > [新規] > [プロジェクト]** の順に選択します。
2. 左側のパネルで **[Visual C#]** を見つけて選択します。 次に、中央のパネルで **[WPF アプリ (.NET Framework)]** を選択します。
   ![Visual Studio で WPF アプリを作成する](media/create-wpf-project-visual-studio.png)
3. ご自分のプロジェクトに `MSTranslatorTextDemo` という名前を付け、フレームワークのバージョンを **.NET Framework 4.5.2 以降**に設定し、 **[OK]** をクリックします。
4. プロジェクトが作成されます。 2 つのタブに `MainWindow.xaml` と `MainWindow.xaml.cs` が開いていることに注目してください。 このチュートリアルでは、これら 2 つのファイルにコードを追加していきます。 前者はアプリのユーザー インターフェイス用、後者は Translator Text と Bing Spell Check の呼び出し用です。
   ![環境を確認する](media/blank-wpf-project.png)

次のセクションでは、JSON 解析などの追加機能のために、アセンブリと NuGet パッケージをプロジェクトに追加します。

## <a name="add-references-and-nuget-packages-to-your-project"></a>参照と NuGet パッケージをプロジェクトに追加する

このプロジェクトには、いくつかの .NET Framework アセンブリと NewtonSoft.Json が必要です。これらは、NuGet パッケージ マネージャーを使用してインストールします。

### <a name="add-net-framework-assemblies"></a>.NET Framework アセンブリを追加する

オブジェクトのシリアル化と逆シリアル化を行うためのアセンブリ、および HTTP 要求と応答を管理するためのアセンブリをプロジェクトに追加します。

1. Visual Studio のソリューション エクスプローラー (右側のパネル) で自分のプロジェクトを見つけます。 自分のプロジェクトを右クリックし、 **[追加] > [参照]** の順に選択して、 **[参照マネージャー]** を開きます。
   ![アセンブリ参照を追加する](media/add-assemblies-sample.png)
2. [アセンブリ] タブには、参照に使用できるすべての .NET Framework アセンブリが表示されます。 画面の右上にある検索バーを使用して、以下の参照を検索し、プロジェクトに追加します。
   * [System.Runtime.Serialization](https://docs.microsoft.com/dotnet/api/system.runtime.serialization)
   * [System.Web](https://docs.microsoft.com/dotnet/api/system.web)
   * [System.Web.Extensions](https://docs.microsoft.com/dotnet/api/system.web)
   * [System.Windows](https://docs.microsoft.com/dotnet/api/system.windows)
3. これらの参照をプロジェクトに追加したら、 **[OK]** をクリックして、 **[参照マネージャー]** を閉じることができます。

> [!NOTE]
> アセンブリ参照の詳細については、「[方法:参照マネージャーを使用して参照を追加または削除する](https://docs.microsoft.com/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2017)」を参照してください。

### <a name="install-newtonsoftjson"></a>NewtonSoft.Json をインストールする

このアプリでは、NewtonSoft.Json を使用して、JSON オブジェクトを逆シリアル化します。 次の手順に従ってパッケージをインストールします。

1. Visual Studio のソリューション エクスプローラーで自分のプロジェクトを見つけて右クリックします。 **[NuGet パッケージの管理]** を選択します。
2. **[参照]** タブを見つけて選択します。
3. 検索バーに「[NewtonSoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)」と入力します。
   ![NewtonSoft.Json を見つけてインストールする](media/add-nuget-packages.png)
4. パッケージを選択し、 **[インストール]** をクリックします。
5. インストールが完了したら、タブを閉じます。

## <a name="create-a-wpf-form-using-xaml"></a>XAML を使用して WPF フォームを作成する

このアプリを使用するためには、ユーザー インターフェイスが必要です。 ユーザーが入力言語と翻訳言語の選択および翻訳するテキストの入力を行うことができ、翻訳の出力を表示するフォームを XAML を使用して作成します。

作成するものを見てみましょう。

![WPF XAML ユーザー インターフェイス](media/translator-text-csharp-xaml.png)

このユーザー インターフェイスには、次のコンポーネントが含まれています。

| Name | Type | 説明 |
|------|------|-------------|
| `FromLanguageComboBox` | ComboBox | Microsoft Translator でサポートされているテキスト翻訳の言語の一覧を表示します。 ユーザーは、翻訳前の言語を選択します。 |
| `ToLanguageComboBox` | ComboBox | `FromComboBox` と同じ言語の一覧を表示しますが、ユーザーの翻訳先の言語を選択するために使用されます。 |
| `TextToTranslate` | TextBox | ユーザーが翻訳対象のテキストを入力できます。 |
| `TranslateButton` | ボタン | このボタンを使用して、テキストを翻訳します。 |
| `TranslatedTextLabel` | Label | 翻訳を表示します。 |
| `DetectedLanguageLabel` | Label | 翻訳するテキスト (`TextToTranslate`) の検出された言語を表示します。 |

> [!NOTE]
> このフォームは XAML ソース コードを使用して作成しますが、Visual Studio のエディターを使用して作成できます。

プロジェクトにコードを追加します。

1. Visual Studio で、`MainWindow.xaml` のタブを選択します。
2. このコードをプロジェクトに追加し、保存します。
   ```xaml
   <Window x:Class="MSTranslatorTextDemo.MainWindow"
           xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
           xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
           xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
           xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
           xmlns:local="clr-namespace:MSTranslatorTextDemo"
           mc:Ignorable="d"
           Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
       <Grid>
           <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
           <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
           <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
           <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

           <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
           <ComboBox x:Name="ToLanguageComboBox"
                   HorizontalAlignment="Left"
                   Margin="306,88,0,0"
                   VerticalAlignment="Top"
                   Width="175" FontSize="14" TabIndex="2">

           </ComboBox>
           <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
           <ComboBox x:Name="FromLanguageComboBox"
               HorizontalAlignment="Left"
               Margin="42,88,0,0"
               VerticalAlignment="Top"
               Width="175" FontSize="14" TabIndex="1"/>
           <Label x:Name="TranslatedTextLabel" Content="Translation is displayed here." HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
           <Label x:Name="DetectedLanguageLabel" Content="Autodetected language is displayed here." HorizontalAlignment="Left" Margin="39,288,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="84" BorderThickness="0"/>
       </Grid>
   </Window>
   ```
3. Visual Studio に、アプリのユーザー インターフェイスのプレビューが表示されます。 上の画像のようになります。

これで、フォームが準備できました。 次は、Text Translation と Bing Spell Check を使用するためのコードを書きます。

> [!NOTE]
> このフォームを自由に調整したり、独自に作成したりできます。

## <a name="create-your-app"></a>アプリを作成する

`MainWindow.xaml.cs` には、アプリを制御するコードが含まれています。 この後のセクションでは、ドロップダウン メニューを設定するコード、および Translator Text と Bing Spell Check によって公開されるいくつかの API を呼び出すコードを追加します。

* このプログラムが開始され、`MainWindow` がインスタンス化されると、Translator Text API の `Languages` メソッドが呼び出されて、言語が取得され、言語選択ドロップダウンが設定されます。 これは、各セッションの開始時に 1 回行われます。
* **[Translate]** ボタンがクリックされると、ユーザーの言語選択とテキストが取得され、入力に対してスペル チェックが実行され、翻訳と検出された言語がユーザーに表示されます。
  * Translator Text API の `Translate` メソッドが呼び出されて、`TextToTranslate` のテキストが翻訳されます。 この呼び出しには、ドロップダウン メニューを使用して選択された `to` と `from` の言語も含まれます。
  * Translator Text API の `Detect` メソッドが呼び出されて、`TextToTranslate` のテキスト言語が判断されます。
  * Bing Spell Check を使用して、`TextToTranslate` が検証され、スペル ミスが修正されます。

このプロジェクトのすべては、`MainWindow : Window` クラスにカプセル化されます。 まず、サブスクリプション キーを設定し、Translator Text と Bing Spell Check のエンドポイントを宣言して、アプリを初期化するコードを追加します。

1. Visual Studio で、`MainWindow.xaml.cs` のタブを選択します。
2. 事前に設定された `using` ステートメントを次に置き換えます。  
   ```csharp
   using System;
   using System.Windows;
   using System.Net;
   using System.Net.Http;
   using System.IO;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using Newtonsoft.Json;
   ```
3. `MainWindow : Window` クラスを見つけ、次のコードに置き換えます。
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator Text and Bing Spell Check
       public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
       const string BING_SPELL_CHECK_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/bing/v7.0/spellcheck/";
       // An array of language codes
       private string[] languageCodes;

       // Dictionary to map language codes from friendly name (sorted case-insensitively on language name)
       private SortedDictionary<string, string> languageCodesAndTitles =
           new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

       // Global exception handler to display error message and exit
       private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
       {
           Exception e = (Exception)args.ExceptionObject;
           MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
           System.Windows.Application.Current.Shutdown();
       }
       // MainWindow constructor
       public MainWindow()
       {
           // Display a message if unexpected error is encountered
           AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

           if (COGNITIVE_SERVICES_KEY.Length != 32)
           {
               MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                   "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                   "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
               System.Windows.Application.Current.Shutdown();
           }
           else
           {
               // Start GUI
               InitializeComponent();
               // Get languages for drop-downs
               GetLanguagesForTranslate();
               // Populate drop-downs with values from GetLanguagesForTranslate
               PopulateLanguageMenus();
           }
       }
   // NOTE:
   // In the following sections, we'll add code below this.
   }
   ```
   1. 自分の Cognitive Services サブスクリプション キーを追加し、保存します。

このコード ブロックでは、翻訳可能な言語に関する情報を含む 2 つのメンバー変数を宣言しました。

| 変数 | Type | 説明 |
|----------|------|-------------|
|`languageCodes` | 文字列の配列 |言語コードをキャッシュします。 この Translator サービスは、英語には `en` というように短いコードを使用して言語を識別します。 |
|`languageCodesAndTitles` | 並べ替え済みディクショナリ | ユーザー インターフェイス内の「わかりやすい」名前を API で使用される短いコードにマッピングします。 大文字と小文字を区別せずアルファベット順の並べ替えを保持します。 |

次に、`MainWindow` コンストラクター内に、`HandleExceptions` を使用したエラー処理を追加しました。 これにより、例外が処理されない場合にアラートが提供されます。 その後、指定されたサブスクリプション キーの長さが 32 文字であることを確認するチェックが実行されます。 キーが 32 文字よりも短いまたは長い場合、エラーがスローされます。

少なくともキーの長さが正しい場合、`InitializeComponent()` が呼び出され、メイン アプリ ウィンドウの XAML 記述の検索、読み込み、およびインスタンス化が行われて、ユーザー インターフェイスが起動します。

最後に、翻訳の言語を取得するメソッドと、アプリのユーザー インターフェイスのドロップダウン メニューを設定するメソッドを呼び出すコードを追加しました。 ご安心ください。すぐにこれらの呼び出しの背後にあるコードについて説明します。

## <a name="get-supported-languages"></a>サポートされている言語を取得する

Translator Text API では、現在、60 を超える言語がサポートされています。 今後も新しい言語のサポートが追加される予定なので、作成するアプリでは、言語の一覧をハードコーディングせずに、Translator Text によって公開される Languages リソースを呼び出すことをお勧めします。

このセクションでは、翻訳可能な言語の一覧が必要であることを指定した、Languages リソースへの `GET` 要求を作成します。

> [!NOTE]
> Languages リソースでは、クエリ パラメーター (transliteration、dictionary、translation) を使用して言語のサポートをフィルター処理できます。 詳細については、[API リファレンス](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages)に関するページを参照してください。

先に進む前に、Languages リソースへの呼び出しのサンプル出力を確認してみましょう。

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    }
    // Additional languages are provided in the full JSON output.
}
```

この出力から、特定の言語の言語コードと `name` を抽出できます。 このアプリでは、NewtonSoft.Json を使用して、JSON オブジェクトを逆シリアル化します ([`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm))。

前のセクションの続きから再開し、サポートされている言語をアプリに渡すメソッドを追加します。

1. Visual Studio で、`MainWindow.xaml.cs` のタブを開きます。
2. 次のコードをプロジェクトに追加します。
   ```csharp
   // ***** GET TRANSLATABLE LANGUAGE CODES
   private void GetLanguagesForTranslate()
   {
       // Send request to get supported language codes
       string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
       WebRequest WebRequest = WebRequest.Create(uri);
       WebRequest.Headers.Add("Accept-Language", "en");
       WebResponse response = null;
       // Read and parse the JSON response
       response = WebRequest.GetResponse();
       using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
       {
           var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
           var languages = result["translation"];

           languageCodes = languages.Keys.ToArray();
           foreach (var kv in languages)
           {
               languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
           }
       }
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

`GetLanguagesForTranslate()` メソッドにより、HTTP GET 要求が作成されます。要求の範囲を翻訳がサポートされている言語に制限するために、`scope=translation` クエリ文字列パラメーターが使用されています。 サポートされている言語は英語で返されるため、`en` の値を持つ `Accept-Language` ヘッダーが追加されます。

JSON 応答が解析され、ディクショナリに変換されます。 次に、言語コードが `languageCodes` メンバー変数に追加されます。 言語コードとわかりやすい言語名を含んだキーと値のペアがループ処理され、`languageCodesAndTitles` メンバー変数に追加されます フォームのドロップダウン メニューには、わかりやすい名前が表示されますが、翻訳を要求するにはコードが必要です。

## <a name="populate-language-drop-down-menus"></a>言語ドロップダウン メニューを設定する

このユーザー インターフェイスは XAML を使用して定義されているので、`InitializeComponent()` を呼び出す以外は設定するために行う必要のあることはあまりありません。 行う必要のある 1 つの作業は、 **[Translate from]** および **[Translate to]** のドロップダウン メニューにわかりやすい言語名を追加するというもので、これは `PopulateLanguageMenus()` によって行われます。

1. Visual Studio で、`MainWindow.xaml.cs` のタブを開きます。
2. 次のコードをプロジェクト内の `GetLanguagesForTranslate()` メソッドの下に追加します。
   ```csharp
   private void PopulateLanguageMenus()
   {
       // Add option to automatically detect the source language
       FromLanguageComboBox.Items.Add("Detect");

       int count = languageCodesAndTitles.Count;
       foreach (string menuItem in languageCodesAndTitles.Keys)
       {
           FromLanguageComboBox.Items.Add(menuItem);
           ToLanguageComboBox.Items.Add(menuItem);
       }

       // Set default languages
       FromLanguageComboBox.SelectedItem = "Detect";
       ToLanguageComboBox.SelectedItem = "English";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

このメソッドにより、`languageCodesAndTitles` ディクショナリが反復処理され、各キーが両方のメニューに追加されます。 メニューが設定されると、既定の翻訳元言語と翻訳先言語がそれぞれ **[Detect]** と **[English]** に設定されます。

> [!TIP]
> メニューの既定の選択肢を設定しない場合、ユーザーは、"to" や "from" の言語を最初に選択せずに **[翻訳]** をクリックできます。 既定値を使用すれば、この問題を扱う必要がなくなります。

`MainWindow` が初期化され、ユーザー インターフェイスが作成されたので、ユーザーが **[Translate]** ボタンをクリックするまでこのコードは実行されません。

## <a name="detect-language-of-source-text"></a>ソース テキストの言語を検出する

Translator Text API を使用して、ソース テキスト (テキスト領域に入力されたテキスト) の言語を検出するメソッドを作成します。 この要求によって返される値は、後で翻訳要求で使用します。

1. Visual Studio で、`MainWindow.xaml.cs` のタブを開きます。
2. 次のコードをプロジェクト内の `PopulateLanguageMenus()` メソッドの下に追加します。
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator Text
       HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(detectUri);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
       detectLanguageWebRequest.ContentType = "application/json; charset=utf-8";
       detectLanguageWebRequest.Method = "POST";

       // Send request
       var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
       string jsonText = serializer.Serialize(text);

       string body = "[{ \"Text\": " + jsonText + " }]";
       byte[] data = Encoding.UTF8.GetBytes(body);

       detectLanguageWebRequest.ContentLength = data.Length;

       using (var requestStream = detectLanguageWebRequest.GetRequestStream())
           requestStream.Write(data, 0, data.Length);

       HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

       // Read and parse JSON response
       var responseStream = response.GetResponseStream();
       var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
       dynamic jsonResponse = serializer.DeserializeObject(jsonString);

       // Fish out the detected language code
       var languageInfo = jsonResponse[0];
       if (languageInfo["score"] > (decimal)0.5)
       {
           DetectedLanguageLabel.Content = languageInfo["language"];
           return languageInfo["language"];
       }
       else
           return "Unable to confidently detect input language.";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

このメソッドにより、Detect リソースへの HTTP `POST` 要求が作成されます。 1 つの引数 `text` を受け取ります。この引数は要求の本文として渡されます。 後で、翻訳要求を作成するときに、UI に入力されたテキストが、言語検出のためにこのメソッドに渡されます。

さらに、このメソッドでは、応答の信頼度スコアが評価されます。 スコアが `0.5` より大きい場合、検出された言語がユーザー インターフェイスに表示されます。

## <a name="spell-check-the-source-text"></a>ソース テキストのスペル チェックを実行する

Bing Spell Check API を使用して、ソース テキストのスペル チェックを実行するメソッドを作成します。 これにより、Translator Text API から正確な翻訳が返されます。 **[Translate]** ボタンをクリックすると、翻訳要求でソース テキストへの修正が渡されます。

1. Visual Studio で、`MainWindow.xaml.cs` のタブを開きます。
2. 次のコードをプロジェクト内の `DetectLanguage()` メソッドの下に追加します。

```csharp
// ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
private string CorrectSpelling(string text)
{
    string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

    // Create a request to Bing Spell Check API
    HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
    spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
    spellCheckWebRequest.Method = "POST";
    spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

    // Create and send the request
    string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
    byte[] data = Encoding.UTF8.GetBytes(body);
    spellCheckWebRequest.ContentLength = data.Length;
    using (var requestStream = spellCheckWebRequest.GetRequestStream())
        requestStream.Write(data, 0, data.Length);
    HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

    // Read and parse the JSON response; get spelling corrections
    var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
    var responseStream = response.GetResponseStream();
    var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
    dynamic jsonResponse = serializer.DeserializeObject(jsonString);
    var flaggedTokens = jsonResponse["flaggedTokens"];

    // Construct sorted dictionary of corrections in reverse order (right to left)
    // This ensures that changes don't impact later indexes
    var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
    for (int i = 0; i < flaggedTokens.Length; i++)
    {
        var correction = flaggedTokens[i];
        var suggestion = correction["suggestions"][0];  // Consider only first suggestion
        if (suggestion["score"] > (decimal)0.7)         // Take it only if highly confident
            corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
    }

    // Apply spelling corrections, in order, from right to left
    foreach (int i in corrections.Keys)
    {
        var oldtext = corrections[i][0];
        var newtext = corrections[i][1];

        // Apply capitalization from original text to correction - all caps or initial caps
        if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
        else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

        text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
    }
    return text;
}
// NOTE:
// In the following sections, we'll add code below this.
```

## <a name="translate-text-on-click"></a>クリック時にテキストを翻訳する

最後に行う必要がある作業は、ユーザー インターフェイスにある **[Translate]** ボタンがクリックされたときに呼び出されるメソッドの作成です。

1. Visual Studio で、`MainWindow.xaml.cs` のタブを開きます。
2. 次のコードをプロジェクト内の `CorrectSpelling()` メソッドの下に追加し、保存します。  
   ```csharp
   // ***** PERFORM TRANSLATION ON BUTTON CLICK
   private async void TranslateButton_Click(object sender, EventArgs e)
   {
       string textToTranslate = TextToTranslate.Text.Trim();

       string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
       string fromLanguageCode;

       // auto-detect source language if requested
       if (fromLanguage == "Detect")
       {
           fromLanguageCode = DetectLanguage(textToTranslate);
           if (!languageCodes.Contains(fromLanguageCode))
           {
               MessageBox.Show("The source language could not be detected automatically " +
                   "or is not supported for translation.", "Language detection failed",
                   MessageBoxButton.OK, MessageBoxImage.Error);
               return;
           }
       }
       else
           fromLanguageCode = languageCodesAndTitles[fromLanguage];

       string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

       // spell-check the source text if the source language is English
       if (fromLanguageCode == "en")
       {
           if (textToTranslate.StartsWith("-"))    // don't spell check in this case
               textToTranslate = textToTranslate.Substring(1);
           else
           {
               textToTranslate = CorrectSpelling(textToTranslate);
               TextToTranslate.Text = textToTranslate;     // put corrected text into input field
           }
       }
       // handle null operations: no text or same source/target languages
       if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
       {
           TranslatedTextLabel.Content = textToTranslate;
           return;
       }

       // send HTTP request to perform the translation
       string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
       string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

       System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
       var requestBody = JsonConvert.SerializeObject(body);

       using (var client = new HttpClient())
       using (var request = new HttpRequestMessage())
       {
           request.Method = HttpMethod.Post;
           request.RequestUri = new Uri(uri);
           request.Content = new StringContent(requestBody, Encoding.UTF8, "app/json");
           request.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
           request.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
           request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

           var response = await client.SendAsync(request);
           var responseBody = await response.Content.ReadAsStringAsync();

           var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
           var translation = result[0]["translations"][0]["text"];

           // Update the translation field
           TranslatedTextLabel.Content = translation;
       }
   }
   ```

最初の手順では、"翻訳元" と "翻訳先" の言語およびユーザーがフォームに入力したテキストを取得します。 ソース言語が **[Detect]** に設定されている場合、`DetectLanguage()` が呼び出され、ソース テキストの言語が判断されます。 テキストの言語が Translator API でサポートされていない場合があります。 その場合は、ユーザーに通知するメッセージが表示され、テキストを翻訳せずに戻ります。

ソース言語が英語の場合 (指定または検出)、`CorrectSpelling()` でテキストのスペルを確認し、修正を適用します。 修正されたテキストがテキスト領域に追加され、ユーザーは修正が行われたことがわかります。

テキストを翻訳するコードには見覚えがあると思います。URI を構築し、要求を作成し、それを送信し、応答を解析します。 JSON 配列には翻訳のオブジェクトが複数含まれている場合がありますが、このアプリで必要なのは 1 つのみです。

要求が成功すると、`TranslatedTextLabel.Content` が `translation` に置き換えられ、ユーザー インターフェイスが更新されて、翻訳されたテキストが表示されます。

## <a name="run-your-wpf-app"></a>WPF アプリを実行する

WPF を使用して、動作する翻訳アプリを作成しました。 このアプリを実行するには、Visual Studio の **[開始]** ボタンをクリックします。

## <a name="source-code"></a>ソース コード

このプロジェクトのソース コードは、GitHub で入手できます。

* [ソース コードの確認](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Microsoft Translator Text API のリファレンス](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
