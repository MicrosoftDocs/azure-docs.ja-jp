---
title: Visual Studio 接続済みサービス - Computer Vision
titleSuffix: Azure Cognitive Services
description: Visual Studio 接続済みサービス機能を使用して、ASP.NET Core Web アプリケーションから Computer Vision API に接続します。
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: ghogen
ms.custom: seodec18
ms.openlocfilehash: 6a576f2817069d7095ea863198168be083d0c6b5
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450948"
---
# <a name="use-connected-services-in-visual-studio-to-connect-to-the-computer-vision-api"></a>Visual Studio で接続済みサービスを使用して Computer Vision API に接続する

Cognitive Services Computer Vision API を使用することで、豊富な情報を抽出して視覚データを分類および処理したり、機械による画像のモデレートを実施してサービスのキュレーションを支援したりできます。

この記事と関連記事では、Cognitive Services Computer Vision API に Visual Studio 接続済みサービス機能を使用する方法について詳しく説明します。 この機能は、Cognitive Services 拡張機能がインストールされた Visual Studio 2017 15.7 またはそれ以降の両方で使用できます。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**。 このサブスクリプションがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
- **Visual Studio 2017 バージョン 15.7**(**Web 開発**ワークロードもインストールされます)。 [こちら](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)でダウンロードできます。

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>Cognitive Services Computer Vision API のサポートをプロジェクトに追加する

1. 新しい ASP.NET Core Web プロジェクトを作成します。 [空のプロジェクト] テンプレートを使用します。 

1. **ソリューション エクスプローラー**で、**[追加]** > **[接続済みサービス]** を選択します。
   [接続済みサービス] ページが開いて、プロジェクトに追加できるサービスが表示されます。

   ![Visual Studio プロジェクトの右クリック メニューのスクリーンショット: [追加] > [接続済みサービス]](../media/vs-common/Connected-Service-Menu.PNG)

1. 利用可能なサービスのメニューで、**[Cognitive Services Computer Vision API]** を選択します。

   ![[接続済みサービス] メニューの [Analyze Images with Computer Vision]\(Computer Vision で画像を分析\) を強調表示したところ](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Visual Studio にサインインしたときに、アカウントに関連付けられている Azure サブスクリプションがある場合は、サブスクリプションのドロップダウン リストがページに表示されます。

   ![Visual Studio の [Computer Vision API] ウィンドウの [サブスクリプション] ドロップダウンを強調表示したところ](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. 使用するサブスクリプションを選択し、Computer Vision API の名前を選択するか、または [編集] リンクを選択して自動生成の名前を修正し、リソース グループ、および価格レベルを選択します。

   ![接続済みサービスの詳細を編集する](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   価格レベルの詳細については、リンク先を参照してください。

1. [追加] を選択して、接続済みサービスのサポートを追加します。
   Computer Vision API の接続をサポートするために、Visual Studio はプロジェクトを修正し、NuGet パッケージ、構成ファイルのエントリ、およびその他の変更を追加します。 出力ウィンドウには、プロジェクトに対して実行されている処理のログが表示されます。 次のように表示されます。

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 2.1.0.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.0
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>Computer Vision API を使用して画像の属性を検出する

1. Startup.cs で次の using ステートメントを追加します。
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. 構成フィールドを追加し、`Startup` クラスの構成フィールドを初期化するコンストラクターを追加して、プログラムで構成を有効にします。

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. プロジェクトの wwwroot フォルダー内に images フォルダーを追加して、画像ファイルを wwwroot フォルダーに追加します。 たとえば、この [Computer Vision API ページ](https://azure.microsoft.com/services/cognitive-services/computer-vision/)にある画像の 1 つを使用できます。 画像の 1 つを右クリックしてローカルのハード ドライブに保存し、ソリューション エクスプローラーで images フォルダーを右クリックし、**[追加]** > **[既存の項目]** を選択してその画像をプロジェクトに追加します。 ソリューション エクスプローラーで、プロジェクトは次のようになります。 
  
   ![画像ファイルが選択された状態のソリューション エクスプローラー ビューのスクリーンショット](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. 画像ファイルを右クリックして [プロパティ] を選択し、**[新しい場合はコピーする]** を選択します。 

   ![画像の [プロパティ] ウィンドウ ([出力ディレクトリにコピー] が [新しい場合はコピーする] に設定されている)](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. Computer Vision API にアクセスして画像をテストするには、Configure メソッドを次のコードに置き換えます。

   ```csharp
        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site. 
            string imagePath = @"images/subway.png";

            // Enable static files such as image files. 
            app.UseStaticFiles();

            string visionApiKey = this.configuration["ComputerVisionAPI_ServiceKey"];
            string visionApiEndPoint = this.configuration["ComputerVisionAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", visionApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "visualFeatures=Categories,Description,Color&language=en";

            // Assemble the URI for the REST API Call.
            string uri = visionApiEndPoint + "/analyze" + "?" + requestParameters;

            HttpResponseMessage response;

            // Request body. Posts an image you've added to your site's images folder. 
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            byte[] byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentString = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentString = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync("<h1>Cognitive Services Demo</h1>");
                await context.Response.WriteAsync($"<p><b>Test Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Computer Vision API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentString));
                await context.Response.WriteAsync("<p>");
            });
        }

   ```
    このコードは、Computer Vision REST API の呼び出しのバイナリ コンテンツとして URI と画像を使用して HTTP リクエストを作成します。

1. ヘルパー関数 GetImageAsByteArray および JsonPrettyPrint を追加します。

   ```csharp
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

            string INDENT_STRING = "    ";
            var indent = 0;
            var quoted = false;
            var sb = new StringBuilder();
            for (var i = 0; i < json.Length; i++)
            {
                var ch = json[i];
                switch (ch)
                {
                    case '{':
                    case '[':
                        sb.Append(ch);
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        break;
                    case '}':
                    case ']':
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        sb.Append(ch);
                        break;
                    case '"':
                        sb.Append(ch);
                        bool escaped = false;
                        var index = i;
                        while (index > 0 && json[--index] == '\\')
                            escaped = !escaped;
                        if (!escaped)
                            quoted = !quoted;
                        break;
                    case ',':
                        sb.Append(ch);
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        break;
                    case ':':
                        sb.Append(ch);
                        if (!quoted)
                            sb.Append(" ");
                        break;
                    default:
                        sb.Append(ch);
                        break;
                }
            }
            return sb.ToString();
        }
   ```

1. Web アプリケーションを実行し、どの Computer Vision API が画像内に見つかったか確認します。

   ![Computer Vision API の画像とフォーマットされた結果](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、リソース グループを削除します。 これにより、Cognitive Service と関連リソースが削除されます。 ポータルを使用してリソース グループを削除するには:

1. ポータル上部にある検索ボックスにリソース グループの名前を入力します。 このクイック スタートで使用されているリソース グループが検索結果に表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[リソース グループ名を入力してください:]** ボックスにリソース グループの名前を入力し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

[Computer Vision API のドキュメント](Home.md)を読み、Computer Vision API について理解を深めます。
