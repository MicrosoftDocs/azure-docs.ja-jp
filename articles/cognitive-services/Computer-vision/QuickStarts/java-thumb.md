---
title: クイック スタート:サムネイルの生成 - REST、Java
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Computer Vision API と Java を使って、画像からサムネイルを生成します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-java
ms.openlocfilehash: 93f4e8b338f63f19a8c92bffd709a5f408072818
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835121"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-java"></a>クイック スタート:Computer Vision の REST API と Java を使用したサムネイルを生成する

このクイックスタートでは、Computer Vision の REST API を使用して、画像からサムネイルを生成します。 その際に指定する高さと幅は、入力画像の縦横比と異なっていてもかまいません。 Computer Vision では、スマート トリミングを使ってインテリジェントに関心領域を識別し、その領域に基づいてトリミングの座標を生成します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* [Java&trade; プラットフォーム、Standard Edition Development Kit 7 または 8](https://aka.ms/azure-jdks) (JDK 7 または 8)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision リソースを作成"  target="_blank">Computer Vision リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを Computer Vision サービスに接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* キーとエンドポイント URL 用に、それぞれ `COMPUTER_VISION_SUBSCRIPTION_KEY` と `COMPUTER_VISION_ENDPOINT` という名前の[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。

## <a name="create-and-run-the-sample-application"></a>サンプル アプリケーションを作成して実行する

このサンプルを作成して実行するには、次の手順を実行します。

1. 普段使用している IDE またはエディターで新しい Java プロジェクトを作成します。 オプションを使用できる場合は、コマンド ライン アプリケーション テンプレートから Java プロジェクトを作成します。
1. 次のライブラリを Java プロジェクトにインポートします。 Maven を使用している場合、Maven 座標はライブラリごとに提供されます。
   - [Apache HTTP クライアント](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.X)
   - [Apache HTTP コア](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.X)
   - [JSON library](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. 次の `import` ステートメントをメイン クラスに追加します。 

   ```java
    import java.awt.*;
    import javax.swing.*;
    import java.net.URI;
    import java.io.InputStream;
    import javax.imageio.ImageIO;
    import java.awt.image.BufferedImage;
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONObject;
   ```

1. インポートの下に、次のサンプル コードの残りの部分を追加します (必要に応じて、ご利用のクラス名に変更します)。
1. Computer Vision サブスクリプション キーとエンドポイントを環境変数に追加します。
1. 必要に応じて、`imageToAnalyze` の値を独自の画像の URL に置き換えます。
1. 保存し、Java プロジェクトをビルドします。
1. IDE を使用している場合は、`GenerateThumbnail` を実行します。 それ以外の場合は、コマンド ラインから実行します (以下のコマンド)。

```java
/**
 * This sample uses the following libraries (create a "lib" folder to place them in): 
 * Apache HTTP client:
 * org.apache.httpcomponents:httpclient:4.5.X 
 * Apache HTTP core:
 * org.apache.httpcomponents:httpccore:4.4.X 
 * JSON library:
 * org.json:json:20180130
 *
 * To build/run from the command line: 
 *     javac GenerateThumbnail.java -cp .;lib\*
 *     java -cp .;lib\* GenerateThumbnail
 */

public class GenerateThumbnail {

    // Add your Computer Vision subscription key and endpoint to your environment
    // variables. Then, close and then re-open your command shell or project for the
    // changes to take effect.
    private static String subscriptionKey = System.getenv("COMPUTER_VISION_SUBSCRIPTION_KEY");
    private static String endpoint = System.getenv("COMPUTER_VISION_ENDPOINT");
    // The endpoint path
    private static final String uriBase = endpoint + "vision/v3.0/generateThumbnail";
    // It's optional if you'd like to use your own image instead of this one.
    private static final String imageToAnalyze = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder uriBuilder = new URIBuilder(uriBase);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API method.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity = new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            System.out.println("status" + response.getStatusLine().getStatusCode());

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200) {
                // Display the thumbnail.
                System.out.println("\nDisplaying thumbnail.\n");
                displayImage(entity.getContent());
            } else {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Error:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // Displays the given input stream as an image.
    public static void displayImage(InputStream inputStream) {
        try {
            BufferedImage bufferedImage = ImageIO.read(inputStream);

            ImageIcon imageIcon = new ImageIcon(bufferedImage);

            JLabel jLabel = new JLabel();
            jLabel.setIcon(imageIcon);

            JFrame jFrame = new JFrame();
            jFrame.setLayout(new FlowLayout());
            jFrame.setSize(100, 150);

            jFrame.add(jLabel);
            jFrame.setVisible(true);
            jFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="examine-the-response"></a>結果の確認

成功応答は、サムネイル画像データを表すバイナリ データとして返されます。 要求が成功すると、応答内のバイナリ データからサムネイルが生成され、サンプル アプリケーションによって作成された別のウィンドウに表示されます。 要求が失敗した場合、応答がコンソール ウィンドウに表示されます。 失敗した要求の応答には、原因の特定につながるエラー コードとメッセージが格納されています。

## <a name="next-steps"></a>次のステップ

Computer Vision を使用して光学文字認識 (OCR) を実行する Java Swing アプリケーションについて説明します。これは、スマートにクロップされたサムネイルを作成し、イメージ内の視覚的な特徴を検出、カテゴライズ、タグ付け、および記述します。

> [!div class="nextstepaction"]
> [Computer Vision API Java チュートリアル](../Tutorials/java-tutorial.md)

* Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)をお試しください。
