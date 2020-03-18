---
title: "Azure 政府機関認知サービス |Microsoft ドキュメント"
description: "Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。"
services: azure-government
cloud: gov
documentationcenter: 
author: yujhongmicrosoft
manager: zakramer
ms.assetid: cba97199-851d-43ae-a75a-c601f3f81601
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/09/2017
ms.author: yujhong
ms.openlocfilehash: 7a11ba977d7b5e557981b1a65284022905488485
ms.sourcegitcommit: 09a2485ce249c3ec8204615ab759e3b58c81d8cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2018
---
# <a name="cognitive-services-on-azure-government--computer-vision-face-translator-text-apis"></a>Azure Government – Computer Vision、Face、Translator Text API で認識サービス

Azure Government の Cognitive Services の概要を表示します、[ここをクリックして](documentation-government-services-aiandcognitiveservices.md)ください。

## <a name="prerequisites"></a>前提条件
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) をインストールし、設定してください。
* [Azure Government での PowerShell](documentation-government-get-started-connect-with-ps.md) に接続してください。

## <a name="part-1-provision-cognitive-services-accounts"></a>パート 1: Cognitive Services アカウントのプロビジョニング

Cognitive Services API にアクセスするためには、まずアクセスするそれぞれの API の Cognitive Services アカウントをプロビジョニングする必要があります。 **Azure Government Portal で Cognitive Services はまだサポートされていません**が、API やサービスにアクセスする Azure PowerShell を使用することができます。 

> [!NOTE]
> アカウントを作成し、アクセスしたい **各** APIのキーの取得のプロセス(下記参照)を通過する必要があります
> 
> 

1. **Cognitive Services リソース プロバイダーが、自分のアカウントに登録されている**ことを確認してください。 **次の Powershell コマンドを実行することで**確認ができます。

   ```PowerShell
   Get-AzureRmResourceProvider
   ```
   **`Microsoft.CognitiveServices` が表示されない**場合、**次のコマンドを実行することで**リソース プロバイダーに登録する必要があります。
   ```PowerShell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.CognitiveServices
   ```
2. 次の Powershell コマンドの "rg-name" 、 "name-of-your-api" および "location-of-resourcegroup" を関連するアカウント情報に置き換えます。 

   アクセスする次の 3 つの API のいずれかに "type of API" タグを置き換えます。
       * ComputerVision
       * Face
       * TextTranslation

   ```PowerShell
   New-AzureRmCognitiveServicesAccount -ResourceGroupName 'rg-name' -name 'name-of-your-api' -Type <type of API> -SkuName S0 -Location 'location-of-resourcegroup'
   ```
   例: 

   ```PowerShell
   New-AzureRmCognitiveServicesAccount -ResourceGroupName 'resourcegrouptest' -name 'myFaceAPI' -Type Face -SkuName S0 -Location 'usgovvirginia'
   ```

   コマンドを実行した後の次のように表示されます。 

   ![cog1](./media/documentation-government-cognitiveservices-img1.png)

3. コピーし、「エンドポイント」保存属性の場所に API の呼び出しを行うときに必要になります。 

### <a name="retrieve-account-key"></a>アカウント キーを取得します。

特定の API にアクセスするアカウント キーを取得する必要があります。 

次の Powershell コマンドで、"youraccountname"タグを作成したアカウントを指定した名前に置き換えます。 'rg-name' タグをリソース グループの名前に置き換えます。

```PowerShell
Get-AzureRmCognitiveServicesAccountKey -Name <youraccountname> -ResourceGroupName 'rg-name'
```

例:
```PowerShell
Get-AzureRmCognitiveServicesAccountKey -Name myFaceAPI -ResourceGroupName 'resourcegrouptest'
```
コピーし、最初の保存キーどこかにも必要になる API に対して呼び出しを行います。

![cog2](./media/documentation-government-cognitiveservices-img2.png)

API に対して呼び出しを行う準備が整いました。 

## <a name="part-2-api-quickstarts"></a>パート 2: API のクイック スタート 
次のクイック スタートは Azure Government の Cognitive Services を通じて使用可能な API を使用した作業を開始するのに役立ちます。

## <a name="computer-vision-api"></a>Computer Vision API
### <a name="prerequisites"></a>前提条件

* Microsoft Computer Vision API Windows SDK の取得は[こちら](https://github.com/Microsoft/Cognitive-vision-windows)です。

* Visual Studio がインストールされていることを確認します。
    -   [Visual Studio 2017 バージョン 15.3](https://www.visualstudio.com/vs/preview/) (**Azure 開発**ワークロードを含む)。
    
    >[!NOTE] 
    > インストールまたは Visual Studio 2017 15.3 のバージョンにアップグレードした後、Azure Functions の Visual Studio 2017 ツールを手動で更新する必要もあります。 ツールを更新するには、**[ツール]** メニューで **[拡張機能と更新プログラム]** >          **[更新プログラム]** > **[Visual Studio Marketplace]** > **[Azure Functions and Web Jobs Tools]\(Azure Functions と Web ジョブのツール\)** > **[更新]** の順に選択します。 
    >
    >
    
### <a name="variations"></a>バリエーション
* Azure Government の Face API にアクセスするための URI は以下です。
   - `https://(resource-group-location).api.cognitive.microsoft.us/face/v1.0`
   - この URI と商用 Azure で使用される URI の主な違いは、の終了 **.us** と uri の先頭の場所


### <a name="AnalyzeImage"></a>画像を Computer Vision API を使用して C# で分析します。

[画像の分析方法](https://westcentralus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fa)によって、画像の内容に基づいたビジュアルの機能を抽出することができます。 イメージをアップロードまたはイメージ URL を指定を選択する返すには、必要な機能を含みます。
* イメージのコンテンツに関連するタグの詳細な一覧。
* 完全な文章のイメージのコンテンツの説明です。
* 座標、性別、およびイメージに含まれているすべての面の経過時間。
* イメージの種類 (クリップアートまたは線の描画)。
* 主要な色、アクセント カラー イメージは黒かどうか、または白黒です。
* これで定義されたカテゴリ[分類](../cognitive-services/computer-vision/category-taxonomy.md)です。
* イメージは、性的に露骨挑発的なコンテンツを含みますか。

### <a name="analyze-an-image-c-example-request"></a>イメージ (C#) 要求の例を分析します。

1. Visual Studio で新しいコンソール ソリューションを作成します。
2. Program.cs を次のコードに置き換えます。
3. 変更、`uriBase`第 1 部から保存して保持する「エンドポイント」属性に、"/analyze"エンドポイントの後にします。
4. 置換、`subscriptionKey`有効なサブスクリプション キーを持つ値です。
5. プログラムを実行します。

```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;

namespace VisionApp1
{
        static class Program
        {
            // **********************************************
            // *** Update or verify the following values. ***
            // **********************************************

            // Replace the subscriptionKey string value with your valid subscription key.
            const string subscriptionKey = "<subscription key>";

            //Copy and paste the "Endpoint" attribute that you saved before into the uriBase string "/analyze" at the end. 
            //Example: https://virginia.api.cognitive.microsoft.us/vision/v1.0/analyze
  
            const string uriBase = "<endpoint>/analyze";
            
            static void Main()
            {
                // Get the path and filename to process from the user.
                Console.WriteLine("Analyze an image:");
                Console.Write("Enter the path to an image you wish to analyze: ");
                string imageFilePath = Console.ReadLine();

                // Execute the REST API call.
                MakeAnalysisRequest(imageFilePath);

                Console.WriteLine("\nPlease wait a moment for the results to appear. Then, press Enter to exit...\n");
                Console.ReadLine();
            }


            /// <summary>
            /// Gets the analysis of the specified image file by using the Computer Vision REST API.
            /// </summary>
            /// <param name="imageFilePath">The image file.</param>
            static async void MakeAnalysisRequest(string imageFilePath)
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters. A third optional parameter is "details".
                string requestParameters = "visualFeatures=Categories,Description,Color&language=en";

                // Assemble the URI for the REST API Call.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Request body. Posts a locally stored JPEG image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses content type "application/octet-stream".
                    // The other content types you can use are "application/json" and "multipart/form-data".
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                    // Execute the REST API call.
                    response = await client.PostAsync(uri, content);

                    // Get the JSON response.
                    string contentString = await response.Content.ReadAsStringAsync();

                    // Display the JSON response.
                    Console.WriteLine("\nResponse:\n");
                    Console.WriteLine(JsonPrettyPrint(contentString));
                }
            }


            /// <summary>
            /// Returns the contents of the specified file as a byte array.
            /// </summary>
            /// <param name="imageFilePath">The image file to read.</param>
            /// <returns>The byte array of the image data.</returns>
            static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }


            /// <summary>
            /// Formats the given JSON string by adding line breaks and indents.
            /// </summary>
            /// <param name="json">The raw JSON string to format.</param>
            /// <returns>The formatted JSON string.</returns>
            static string JsonPrettyPrint(string json)
            {
                if (string.IsNullOrEmpty(json))
                    return string.Empty;

                json = json.Replace(Environment.NewLine, "").Replace("\t", "");

                StringBuilder sb = new StringBuilder();
                bool quote = false;
                bool ignore = false;
                int offset = 0;
                int indentLength = 3;

                foreach (char ch in json)
                {
                    switch (ch)
                    {
                        case '"':
                            if (!ignore) quote = !quote;
                            break;
                        case '\'':
                            if (quote) ignore = !ignore;
                            break;
                    }

                    if (quote)
                        sb.Append(ch);
                    else
                    {
                        switch (ch)
                        {
                            case '{':
                            case '[':
                                sb.Append(ch);
                                sb.Append(Environment.NewLine);
                                sb.Append(new string(' ', ++offset * indentLength));
                                break;
                            case '}':
                            case ']':
                                sb.Append(Environment.NewLine);
                                sb.Append(new string(' ', --offset * indentLength));
                                sb.Append(ch);
                                break;
                            case ',':
                                sb.Append(ch);
                                sb.Append(Environment.NewLine);
                                sb.Append(new string(' ', offset * indentLength));
                                break;
                            case ':':
                                sb.Append(ch);
                                sb.Append(' ');
                                break;
                            default:
                                if (ch != ' ') sb.Append(ch);
                                break;
                        }
                    }
                }

                return sb.ToString().Trim();
            }
        }
    }
```
### <a name="analyze-an-image-response"></a>イメージ、応答を分析します。

JSON では、正常な応答が返されます。 正常な応答の例を次に示します。

```json

{
   "categories": [
      {
         "name": "people_baby",
         "score": 0.52734375
      },
      {
         "name": "people_young",
         "score": 0.4375
      }
   ],
   "description": {
      "tags": [
         "person",
         "indoor",
         "clothing",
         "woman",
         "white",
         "table",
         "food",
         "girl",
         "smiling",
         "posing",
         "holding",
         "black",
         "sitting",
         "young",
         "plate",
         "hair",
         "wearing",
         "cake",
         "large",
         "shirt",
         "dress",
         "eating",
         "standing",
         "blue"
      ],
      "captions": [
         {
            "text": "a woman posing for a picture",
            "confidence": 0.460196158842535
         }
      ]
   },
   "requestId": "7c20cc50-f5eb-453b-abb5-98378917431c",
   "metadata": {
      "width": 721,
      "height": 960,
      "format": "Jpeg"
   },
   "color": {
      "dominantColorForeground": "Black",
      "dominantColorBackground": "White",
      "dominantColors": [
         "White"
      ],
      "accentColor": "7C4F57",
      "isBWImg": false
   }
}
```
Computer Vision API の詳細については[パブリック ドキュメント](../cognitive-services/computer-vision/index.md)と[パブリック API ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fa)を参照してください。

## <a name="face-api"></a>Face API
### <a name="prerequisites"></a>前提条件
* Microsoft Face API Windows SDK の取得は[こちら](https://www.nuget.org/packages/Microsoft.ProjectOxford.Face/)です。

* Visual Studio がインストールされていることを確認します。
    -   [Visual Studio 2017 バージョン 15.3](https://www.visualstudio.com/vs/preview/) (**Azure 開発**ワークロードを含む)。
    
    >[!NOTE] 
    > インストールまたは Visual Studio 2017 15.3 のバージョンにアップグレードした後、Azure Functions の Visual Studio 2017 ツールを手動で更新する必要もあります。 ツールを更新するには、**[ツール]** メニューで **[拡張機能と更新プログラム]** >          **[更新プログラム]** > **[Visual Studio Marketplace]** > **[Azure Functions and Web Jobs Tools]\(Azure Functions と Web ジョブのツール\)** > **[更新]** の順に選択します。 
    >
    >
    
### <a name="variations"></a>バリエーション 
* Azure Government の Face API にアクセスするための URI は以下です。
   - `https://(resource-group-location).api.cognitive.microsoft.us/face/v1.0`
   - この URI と商用 Azure で使用される URI の主な違いは、の終了 **.us**と uri の先頭の場所

### <a name="Detect"></a>Face API を使用して C# で画像から顔を検出します。

[Face - Detect method](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)使用して、画像から顔を検出し、含まれる顔の属性を返します。

* フェイス ID: 一意の ID Face API のいくつかのシナリオで使用します。 
* : 面の四角形、左、上、幅、および高さ直面しているイメージ内の場所を示すです。
* 目印: 27 ポイント フェイス目印フェイス コンポーネントの重要な位置を指すの配列。
* 年齢、性別を含む顔の属性は、強度、ヘッド ポーズ、および顔髪を笑いました。 

### <a name="face-detect-c-example-request"></a>顔の検出の要求の例 (C#)

サンプルは、Face API のクライアント ライブラリを使用して C# で記述します。 

1. Visual Studio で新しいコンソール ソリューションを作成します。
2. Program.cs を次のコードに置き換えます。
3. 置換、`subscriptionKey`上で取得したキーの値を持つ値です。
4. 変更、`uriBase`上で取得した「エンドポイント」属性の値。
5. プログラムを実行します。
6. ハード ドライブにイメージへのパスを入力します。

```csharp

using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;

namespace FaceApp1
{

    static class Program
    {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************
         
        // Replace the subscriptionKey string value with your valid subscription key.
        const string subscriptionKey = "<subscription key>";

        //Copy and paste the "Endpoint" attribute that you saved before into the uriBase string "/detect" at the end. 
        //Example: https://virginia.api.cognitive.microsoft.us/face/v1.0/detect
        const string uriBase ="<endpoint>/detect";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Detect faces:");
            Console.Write("Enter the path to an image with faces that you wish to analzye: ");
            string imageFilePath = Console.ReadLine();

            // Execute the REST API call.
            MakeAnalysisRequest(imageFilePath);

            Console.WriteLine("\nPlease wait a moment for the results to appear. Then, press Enter to exit...\n");
            Console.ReadLine();
        }


        /// <summary>
        /// Gets the analysis of the specified image file by using the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file.</param>
        static async void MakeAnalysisRequest(string imageFilePath)
        {
            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnfaceId=true&returnfaceLandmarks=false&returnfaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion";

            // Assemble the URI for the REST API Call.
            string uri = uriBase + "?" + requestParameters;

            HttpResponseMessage response;

            // Request body. Posts a locally stored JPEG image.
            byte[] byteData = GetImageAsByteArray(imageFilePath);

            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                response = await client.PostAsync(uri, content);

                // Get the JSON response.
                string contentString = await response.Content.ReadAsStringAsync();

                // Display the JSON response.
                Console.WriteLine("\nResponse:\n");
                Console.WriteLine(JsonPrettyPrint(contentString));
            }
        }


        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
            BinaryReader binaryReader = new BinaryReader(fileStream);
            return binaryReader.ReadBytes((int)fileStream.Length);
        }


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            int offset = 0;
            int indentLength = 3;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\'':
                        if (quote) ignore = !ignore;
                        break;
                }

                if (quote)
                    sb.Append(ch);
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (ch != ' ') sb.Append(ch);
                            break;
                    }
                }
            }

            return sb.ToString().Trim();
        }
    }
}
```
### <a name="face-detect-response"></a>顔の検出の応答

JSON では、正常な応答が返されます。 正常な応答の例を次に示します。 

```json
Response:
[
   {
      "faceId": "0ed7f4db-1207-40d4-be2e-84694e42d682",
      "faceRectangle": {
         "top": 60,
         "left": 83,
         "width": 361,
         "height": 361
      },
      "faceAttributes": {
         "smile": 0.284,
         "headPose": {
            "pitch": 0.0,
            "roll": -12.2,
            "yaw": -16.7
         },
         "gender": "female",
         "age": 16.5,
         "facialHair": {
            "moustache": 0.0,
            "beard": 0.0,
            "sideburns": 0.0
         },
         "glasses": "NoGlasses",
         "emotion": {
            "anger": 0.003,
            "contempt": 0.001,
            "disgust": 0.001,
            "fear": 0.002,
            "happiness": 0.284,
            "neutral": 0.694,
            "sadness": 0.012,
            "surprise": 0.004
         }
      }
   }
]
```
Face API の詳細については[パブリック ドキュメント](../cognitive-services/Face/index.md)と[パブリック API ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)を参照してください。

## <a name="text-translation-api"></a>Text Translation API
### <a name="prerequisites"></a>前提条件

* Visual Studio がインストールされていることを確認します。
    -   [Visual Studio 2017 バージョン 15.3](https://www.visualstudio.com/vs/preview/) (**Azure 開発**ワークロードを含む)。
    
    >[!NOTE] 
    > インストールまたは Visual Studio 2017 15.3 のバージョンにアップグレードした後、Azure Functions の Visual Studio 2017 ツールを手動で更新する必要もあります。 ツールを更新するには、**[ツール]** メニューで **[拡張機能と更新プログラム]** >          **[更新プログラム]** > **[Visual Studio Marketplace]** > **[Azure Functions and Web Jobs Tools]\(Azure Functions と Web ジョブのツール\)** > **[更新]** の順に選択します。 
    >
    >
    
### <a name="variations"></a>バリエーション
* Azure Government の Text Translation API にアクセスするための URI は以下です。
   - `https://api.microsofttranslator.us/v2/http.svc`
   - 商用の URI から唯一の違いは、エンドポイントの **.us**
* Text Translation API にアクセス トークンを取得するための URI は、`Endpoint`によってこのクイック スタートの第 1 部で保存された属性`/IssueToken`終了時

### <a name="text-translation-method"></a>テキスト変換方法
このサンプルを使用して、[Text Translation - Translate method](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate)別の言語から、テキスト文字列に変換するには、言語を指定します。 複数ある[言語コード](https://msdn.microsoft.com/library/hh456380.aspx)でテキスト変換 API を使用できます。 

### <a name="text-translation-c-example-request"></a>テキスト変換 (C#) の要求の例

サンプルは、C# で書き込まれます。 

1. Visual Studio で新しいコンソール ソリューションを作成します。
2. Program.cs を以下の対応するコードに置き換えます。
3. 置換、`subscriptionKey`上で取得したキーの値を持つ値です。
4. Token.cs クラスおよびコピーを作成し、次の対応するコードを貼り付けます。 
5. プログラムを実行します。

「テキスト」、「から」と"to"Program.cs 内の変数を置き換えることでさまざまな言語とテキストをテストすることもできます。 

```csharp
using System;
using System.IO;
using System.Net;
using System.Runtime.Serialization;
using System.Threading.Tasks;

namespace TextTranslationApp1
{
        static class Program
        {
            // **********************************************
            // *** Update or verify the following value. ***
            // **********************************************

            // Replace the subscriptionKey string value with your valid subscription key.
            const string subscriptionKey = "<subscription key>";

            // This is the base uri for accessing the Text Translation API. We will be testing the /translate method.
            const string uriBase = "https://api.microsofttranslator.us/v2/http.svc/translate?";

            static void Main()
            {
                MainAsync().Wait();
            }

            private static async Task MainAsync()
            {
                // Replace the "text" variable with whatever text you want to translate
                // Replace the "from" variable with the language that the "text" string is written in
                // Replace the "to" variable with the language that you want the text to be translated into

                string text = "elephants are cute";
                string from = "en";
                string to = "es";

                //Formulating the uri to make a GET call to the API 
                string uri = uriBase + "text=" + text + "&from=" + from + "&to=" + to;

                //Declare an instance of the Token class
                Token token = new Token();
                //Retrieve the token
                string accessToken = await token.GetToken(subscriptionKey);
                // Make the GET call
                HttpWebRequest httpWebRequest = (HttpWebRequest)WebRequest.Create(uri);
                httpWebRequest.Headers.Add("Authorization", accessToken);
                //Getting the JSON response
                using (WebResponse response = httpWebRequest.GetResponse())
                using (Stream stream = response.GetResponseStream())
                {
                    DataContractSerializer dcs = new DataContractSerializer(Type.GetType("System.String"));
                    string translation = (string)dcs.ReadObject(stream);
                    System.Console.WriteLine("Translation for source text '{0}' from {1} to {2} is", text, from, to);
                    Console.WriteLine(translation);
                    Console.ReadLine();
                }

            }

        }
    }
```
今すぐしようとして、テキスト変換 API にアクセス トークンを取得するためには、このメソッドを格納する Token.cs クラスを作成します。
ソリューション エクスプ ローラーからプロジェクトを右クリックし、「新しいクラス」を追加します。 コピーし、Token.cs クラスに次のコードを貼り付けます。

```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

namespace TextTranslationApp1
{
    public class Token
    {
        /// URL of the token service
        /// Replace this url with your endpoint (the location should be the location of your resource group) and add the "IssueToken" at the end
        private static readonly Uri ServiceUrl = new Uri("https://virginia.api.cognitive.microsoft.us/sts/v1.0/IssueToken");

        /// Name of header used to pass the subscription key to the token service
        private const string OcpApimSubscriptionKeyHeader = "Ocp-Apim-Subscription-Key";

        /// After obtaining a valid token, this class will cache it for this duration.
        /// Use a duration of 5 minutes, which is less than the actual token lifetime of 10 minutes.
        private static readonly TimeSpan TokenCacheDuration = new TimeSpan(0, 5, 0);

        /// Cache the value of the last valid token obtained from the token service.
        public string _storedTokenValue = string.Empty;

        /// When the last valid token was obtained.
        private DateTime _storedTokenTime = DateTime.MinValue;


        /// Gets the HTTP status code for the most recent request to the token service.
        public HttpStatusCode RequestStatusCode
        {
            get; private set;
        }

        // This method retrieves and caches an access token and generates a new one after 10 minutes, when the token expires. 
        public async Task<string> GetToken(string SubscriptionKey)
        {
            if (string.IsNullOrWhiteSpace(SubscriptionKey))
            {
                return string.Empty;
            }

            // Re-use the cached token if there is one.
            if ((DateTime.Now - _storedTokenTime) < TokenCacheDuration)
            {
                return _storedTokenValue;
            }

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = ServiceUrl;
                request.Content = new StringContent(string.Empty);
                request.Headers.TryAddWithoutValidation(OcpApimSubscriptionKeyHeader, SubscriptionKey);
                client.Timeout = TimeSpan.FromMinutes(10);
                var response = await client.SendAsync(request);
                this.RequestStatusCode = response.StatusCode;
                response.EnsureSuccessStatusCode();
                var token = await response.Content.ReadAsStringAsync();
                _storedTokenTime = DateTime.Now;
                _storedTokenValue = "Bearer " + token;
                return _storedTokenValue;
            }
        }       
    }
}

```
### <a name="text-translation-response"></a>翻訳のテキスト応答

コンソールから、正常な応答の出力の例は、次に示します。 

![成功](./media/documentation-government-cognitiveservices-img6.png)

Text Translation API の詳細については[パブリック ドキュメント](../cognitive-services/translator/translator-info-overview.md)と[パブリック API ドキュメント](http://docs.microsofttranslator.com/text-translate.html)を参照してください。

### <a name="next-steps"></a>次の手順
* サブスクライブ、 [Azure 政府機関向けブログ](https://blogs.msdn.microsoft.com/azuregov/)
* 使用して、スタック オーバーフローに関するヘルプを表示、"[azure gov](https://stackoverflow.com/questions/tagged/azure-gov)"タグ
* フィードバックのお願いまたは経由での新機能を要求、 [Azure 政府フィードバック フォーラム](https://feedback.azure.com/forums/558487-azure-government)
