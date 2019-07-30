---
title: チュートリアル:Face API (C#)
titleSuffix: Azure Cognitive Services
description: Cognitive Services Face API を利用して画像内の顔の特徴を検出する Windows アプリを作成します。
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: ghogen
ms.openlocfilehash: 7907a79289149d9e165dd6df0c09bee596e624e2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606811"
---
# <a name="connecting-to-cognitive-services-face-api-by-using-connected-services-in-visual-studio"></a>Visual Studio の接続済みサービスを使用して Cognitive Services Face API に接続する

Cognitive Services Face API を使用して、写真の中の顔を検出、分析、整理、タグ付けすることができます。

この記事と関連記事では、Cognitive Services Face API に Visual Studio 接続済みサービス機能を使用する方法について詳しく説明します。 この機能は、Cognitive Services 拡張機能がインストールされた Visual Studio 2017 15.7 またはそれ以降の両方で使用できます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 このサブスクリプションがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
- Visual Studio 2017 バージョン 15.7 以降と、**Web 開発**のワークロードがインストールされていること。 [こちら](https://www.visualstudio.com/downloads/)でダウンロードできます。

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="create-a-project-and-add-support-for-cognitive-services-face-api"></a>プロジェクトを作成して Cognitive Services Face API のサポートを追加する

1. 新しい ASP.NET Core Web プロジェクトを作成します。 [空のプロジェクト] テンプレートを使用します。 

1. **ソリューション エクスプローラー**で、 **[追加]**  >  **[接続済みサービス]** を選択します。
   [接続済みサービス] ページが開いて、プロジェクトに追加できるサービスが表示されます。

   ![接続済みサービスのメニュー項目を追加する](./media/vs-face-connected-service/Connected-Service-Menu.PNG)

1. 利用可能なサービスのメニューで、 **[Cognitive Services Face API]** を選択します。

   ![接続するサービスを選択する](./media/vs-face-connected-service/Cog-Face-Connected-Service-0.PNG)

   Visual Studio にサインインしたときに、アカウントに関連付けられている Azure サブスクリプションがある場合は、サブスクリプションのドロップダウン リストがページに表示されます。

   ![サブスクリプションを選択します。](media/vs-face-connected-service/Cog-Face-Connected-Service-1.PNG)

1. 使用するサブスクリプションを選択し、Face API の名前を選択するか、または [編集] リンクを選択して自動生成の名前を修正し、リソース グループ、および価格レベルを選択します。

   ![接続済みサービスの詳細を編集する](media/vs-face-connected-service/Cog-Face-Connected-Service-2.PNG)

   価格レベルの詳細については、リンク先を参照してください。

1. [追加] を選択して、接続済みサービスのサポートを追加します。
   Face API の接続をサポートするために、Visual Studio はプロジェクトを修正し、NuGet パッケージ、構成ファイルのエントリ、およびその他の変更を追加します。

## <a name="use-the-face-api-to-detect-attributes-of-faces-in-an-image"></a>Face API を使用して画像の中の顔の属性を検出する

1. Startup.cs で次の using ステートメントを追加します。
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. 構成フィールドを追加し、Startup クラスの構成フィールドを初期化するコンストラクターを追加して、プログラムで構成を有効にします。

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. プロジェクトの wwwroot フォルダー内に images フォルダーを追加して、画像ファイルを wwwroot フォルダーに追加します。 たとえば、この [Face API ページ](https://azure.microsoft.com/services/cognitive-services/face/)にある画像の 1 つを使用できます。 画像の 1 つを右クリックしてローカルのハード ドライブに保存し、ソリューション エクスプローラーで images フォルダーを右クリックし、 **[追加]**  >  **[既存の項目]** を選択してその画像をプロジェクトに追加します。 ソリューション エクスプローラーで、プロジェクトは次のようになります。
 
   ![images フォルダーと画像ファイル](media/vs-face-connected-service/Cog-Face-Connected-Service-6.PNG)

1. 画像ファイルを右クリックして [プロパティ] を選択し、 **[新しい場合はコピーする]** を選択します。

   ![新しい場合はコピーする](media/vs-face-connected-service/Cog-Face-Connected-Service-5.PNG)
 
1. Face API にアクセスして画像をテストするには、Configure メソッドを次のコードに置き換えます。 imagePath 文字列を、顔の画像の正しいパスとファイル名に変更します。

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site.
            string imagePath = @"images/face1.png";

            // Enable static files such as image files.
            app.UseStaticFiles();

            string faceApiKey = this.configuration["FaceAPI_ServiceKey"];
            string faceApiEndPoint = this.configuration["FaceAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", faceApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

            // Assemble the URI for the REST API Call.
            string uri = faceApiEndPoint + "/detect?" + requestParameters;

            // Request body. Posts an image you've added to your site's images folder.
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            var byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentStringFace = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                var response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentStringFace = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync($"<p><b>Face Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Face detection API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentStringFace));
                await context.Response.WriteAsync("<p>");
            });
        }
   ```
    この手順のコードでは、接続済みサービスの追加時に追加したキーを使用して、Face REST API の呼び出しと共に HTTP 要求を構築します。

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

1. Web アプリケーションを実行し、どの Face API が画像内に見つかったか確認します。
 
   ![Face API の画像とフォーマットされた結果](media/vs-face-connected-service/Cog-Face-Connected-Service-4.PNG)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、リソース グループを削除します。 これにより、Cognitive Service と関連リソースが削除されます。 ポータルを使用してリソース グループを削除するには:

1. ポータル上部にある検索ボックスにリソース グループの名前を入力します。 このクイック スタートで使用されているリソース グループが検索結果に表示されたら、それを選択します。
1. **[リソース グループの削除]** を選択します。
1. **[リソース グループ名を入力してください:]** ボックスにリソース グループの名前を入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

[Face API のドキュメント](Overview.md)を読み、Face API について理解を深めます。
