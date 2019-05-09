---
title: チュートリアル:イメージの操作を実行する - Java
titlesuffix: Azure Cognitive Services
description: Microsoft Cognitive Services の Computer Vision API を使用する基本的な Java Swing アプリを探索します。 OCR を実行し、サムネイルを作成して、イメージ内の視覚的な特徴を操作します。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.author: kefre
ms.custom: seodec18
ms.date: 09/21/2017
ms.openlocfilehash: 4f6af31ba6b04ddbecb7cb42cebe345b6af720ac
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868091"
---
# <a name="tutorial-computer-vision-api-java"></a>チュートリアル:Computer Vision API Java

このチュートリアルでは、Azure Cognitive Services の Computer Vision REST API の機能について説明します。

Computer Vision REST API を使用して、光学文字認識 (OCR) を実行し、スマート トリミングされたサムネイルを作成するほか、イメージ内の視覚的な特徴 (顔など) を検出、カテゴライズ、タグ付け、および記述する Java Swing アプリケーションについて確認します。 この例では、分析および処理用にイメージ の URL を送信できます。 Computer Vision REST API を使用する独自のアプリを Java でビルドするためのテンプレートとして、このオープン ソースの例を使用できます。

このチュートリアルでは、Computer Vision を使用して次の操作を行う方法について説明します。

> [!div class="checklist"]
> * イメージを分析する
> * 画像内の自然または人工のランドマークを識別する
> * 画像内の有名人を識別する
> * 画像から高品質のサムネイルを作成する
> * 画像内の印字されたテキストを読み取る
> * 画像内の手書きのテキストを読み取る

Java Swing フォーム アプリケーションは既に記述されていますが、何の機能も持っていません。 このチュートリアルでは、Computer Vision REST API 固有のコードを追加して、アプリの機能を完成させます。

## <a name="prerequisites"></a>前提条件

### <a name="platform-requirements"></a>プラットフォームの要件

このチュートリアルは、NetBeans IDE を使用して開発されています。 具体的には、NetBeans の **Java SE** バージョンです。それは、[こちらからダウンロード](https://netbeans.org/downloads/index.html)できます。

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Computer Vision API にサブスクライブしてサブスクリプション キーを取得する 

例を作成する前に、Azure Cognitive Services の一部である Computer Vision API をサブスクライブする必要があります。 サブスクリプションとキーの管理の詳細については、[サブスクリプション](https://azure.microsoft.com/try/cognitive-services/)に関するページを参照してください。 このチュートリアルでは、主キーと 2 次キーの両方を使用できます。 

## <a name="acquire-the-incomplete-tutorial-project"></a>不完全なチュートリアル プロジェクトを取得する

### <a name="download-the-tutorial-project"></a>チュートリアル プロジェクトをダウンロードする

1. [Cognitive Services Java Computer Vision チュートリアル](https://github.com/Azure-Samples/cognitive-services-java-computer-vision-tutorial) リポジトリに移動します。
1. **[複製またはダウンロード]** ボタンをクリックします。
1. **[ZIP のダウンロード]** をクリックして、チュートリアル プロジェクトの ZIP ファイルをダウンロードします。

NetBeans は .zip ファイルからプロジェクトをインポートするため、.zip ファイルの内容を抽出する必要はありません。

### <a name="import-the-tutorial-project"></a>チュートリアル プロジェクトをインポートする

**cognitive-services-java-computer-vision-tutorial-master.zip** ファイルを NetBeans にインポートします。

1. NetBeans で、**[ファイル]** > **[プロジェクトをインポート]** > **[ZIP から...]** をクリックします。**[ZIP からプロジェクトをインポート]** ダイアログ ボックスが表示されます。
1. **[ZIP ファイル]** フィールドで、**[参照]** ボタンをクリックして **cognitive-services-java-computer-vision-tutorial-master.zip** ファイルを見つけ、**[開く]** をクリックします。
1. **[ZIP からプロジェクトをインポート]** ダイアログ ボックスで **[インポート]** をクリックします。
1. **[プロジェクト]** パネルで、**[ComputerVision]** > **[ソース パッケージ]** > **&lt;[既定のパッケージ]&gt;** を展開します。 
   NetBeans の一部のバージョンでは、**[ソース パッケージ]** > **&lt;[既定のパッケージ]&gt;** の代わりに **[src]** を使用します。 その場合は、**[src]** を展開します。
1. **MainFrame.java** をダブルクリックして、NetBeans エディターにファイルを読み込みます。 **MainFrame.java** ファイルの **[デザイン]** タブが表示されます。
1. **[ソース]** タブをクリックして、Java ソース コードを表示します。

### <a name="build-and-run-the-tutorial-project"></a>チュートリアル プロジェクトをビルドして実行する

1. **F6** キーを押して、チュートリアル アプリケーションをビルドして実行します。

    チュートリアル アプリケーションで、その機能用のウィンドウを表示するタブをクリックします。 ボタンのメソッドは空なので、何も起こりません。

    ウィンドウの下部には **[サブスクリプション キー]** フィールドと **[サブスクリプション リージョン]** フィールドがあります。 これらのフィールドには、有効なサブスクリプション キーとそのサブスクリプション キー用の適切なリージョンを入力する必要があります。 サブスクリプション キーを取得するには、[サブスクリプション](https://azure.microsoft.com/try/cognitive-services/)に関するページを参照してください。 そのリンクで無料試用版からサブスクリプション キーを取得した場合は、既定のリージョンの **westcentralus** がサブスクリプション キーの適切なリージョンです。

1. チュートリアル アプリケーションを終了します。

## <a name="add-the-tutorial-code-to-the-project"></a>チュートリアル コードをプロジェクトに追加する

Java Swing アプリケーションは 6 つのタブ付きで設定されます。 各タブは Computer Vision の別個の機能を示します (分析、OCR など)。 6 つのチュートリアル セクションに依存関係はないため、1 つのセクション、6 つのすべてのセクション、または任意のサブセットを追加できます。 セクションは任意の順序で追加できます。

### <a name="analyze-an-image"></a>イメージを分析する

Computer Vision の分析機能では、2,000 を超える認識可能なオブジェクト、生物、シーン、およびアクションについて画像をスキャンします。 分析が完了すると、分析機能は、わかりやすいタグ、色の分析、キャプションなどでイメージを説明する、JSON オブジェクトを返します。

チュートリアル アプリケーションの分析機能を完成させるには、次の手順を実行します。

#### <a name="add-the-event-handler-code-for-the-form-button"></a>フォーム ボタンのイベント ハンドラー コードを追加する

**analyzeImageButtonActionPerformed** イベント ハンドラー メソッドは、フォームをクリアし、URL で指定された画像を表示した後、**AnalyzeImage** メソッドを呼び出して画像を分析します。 **AnalyzeImage** が戻るとき、このメソッドは、書式設定された JSON 応答を **[応答]** テキスト領域に表示し、**JSONObject** から最初のキャプションを抽出し、キャプションとキャプションが正しいことを示す信頼度レベルを表示します。

次のコードをコピーして **analyzeImageButtonActionPerformed** メソッドに貼り付けます。

> [!NOTE]
> NetBeans では、メソッド定義行 (```private void```) またはそのメソッドの中かっこに貼り付けることはできません。 コードをコピーするには、メソッド定義と中かっこの間の行をコピーし、それらをメソッドの内容の上に貼り付けます。

```java
    private void analyzeImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL analyzeImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        analyzeImage.setIcon(new ImageIcon());
        analyzeCaptionLabel.setText("");
        analyzeResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            analyzeImageUrl = new URL(analyzeImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(analyzeImageUrl);
            scaleAndShowImage(bImage, analyzeImage);
        } catch(IOException e) {
            analyzeResponseTextArea.setText("Error loading Analyze image: " + e.getMessage());
            return;
        }
        
        // Analyze the image.
        JSONObject jsonObj = AnalyzeImage(analyzeImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        analyzeResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("description") && jsonObj.getJSONObject("description").has("captions")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("description").getJSONArray("captions").getJSONObject(0);
            
            if (jsonCaption.has("text") && jsonCaption.has("confidence")) {
                
                analyzeCaptionLabel.setText("Caption: " + jsonCaption.getString("text") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 呼び出しのラッパーを追加する

**AnalyzeImage** メソッドは、イメージを分析するために REST API 呼び出しをラップします。 このメソッドは、イメージを記述する **JSONObject** を返します。エラーが発生した場合は **null** を返します。

**AnalyzeImage** メソッドをコピーして、**analyzeImageButtonActionPerformed** メソッドのすぐ下に貼り付けます。

```java
    /**
     * Encapsulates the Microsoft Cognitive Services REST API call to analyze an image.
     * @param imageUrl: The string URL of the image to analyze.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject AnalyzeImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call for Analyze Image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseAnalyze;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color,Adult");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
 ```

#### <a name="run-the-application"></a>アプリケーションの実行

**F6** キーを押してアプリケーションを実行します。 **[Subscription Key]\(サブスクリプション キー\)** フィールドにサブスクリプション キーを入力し、**[Subscription Region]\(サブスクリプション リージョン\)** で正しいリージョンが使用されていることを確認します。 分析するイメージへの URL を入力し、**[Analyze Image]\(イメージの分析\)** ボタンをクリックしてイメージを分析し、結果を確認します。

### <a name="recognize-a-landmark"></a>ランドマークを認識する

Computer Vision のランドマーク機能は、山や有名な建物などの自然および人口のランドマークのイメージを分析します。 分析が完了すると、ランドマーク機能はイメージで見つかったランドマークを識別する JSON オブジェクトを返します。

チュートリアル アプリケーションのランドマーク機能を完成させるには、次の手順を実行します。

#### <a name="add-the-event-handler-code-for-the-form-button"></a>フォーム ボタンのイベント ハンドラー コードを追加する

**landmarkImageButtonActionPerformed** イベント ハンドラー メソッドは、フォームをクリアし、URL で指定された画像を表示した後、**LandmarkImage** メソッドを呼び出して画像を分析します。 **LandmarkImage** が戻るとき、このメソッドは、書式設定された JSON 応答を **[応答]** テキスト領域に表示し、**JSONObject** から最初のランドマークの名前を抽出し、ランドマークとランドマークが正しく認識されたことを示す信頼度レベルを表示します。

次のコードをコピーして **landmarkImageButtonActionPerformed** メソッドに貼り付けます。

> [!NOTE]
> NetBeans では、メソッド定義行 (```private void```) またはそのメソッドの中かっこに貼り付けることはできません。 コードをコピーするには、メソッド定義と中かっこの間の行をコピーし、それらをメソッドの内容の上に貼り付けます。

```java
    private void landmarkImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL landmarkImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        landmarkImage.setIcon(new ImageIcon());
        landmarkCaptionLabel.setText("");
        landmarkResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            landmarkImageUrl = new URL(landmarkImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(landmarkImageUrl);
            scaleAndShowImage(bImage, landmarkImage);
        } catch(IOException e) {
            landmarkResponseTextArea.setText("Error loading Landmark image: " + e.getMessage());
            return;
        }
        
        // Identify the landmark in the image.
        JSONObject jsonObj = LandmarkImage(landmarkImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        landmarkResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("landmarks")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("landmarks").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                landmarkCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 呼び出しのラッパーを追加する

**LandmarkImage** メソッドは、イメージを分析するために REST API 呼び出しをラップします。 このメソッドは、画像内で検出されたランドマークを記述する **JSONObject** を返します。エラーが発生した場合は **null** を返します。

**LandmarkImage** メソッドをコピーして、**landmarkImageButtonActionPerformed** メソッドのすぐ下に貼り付けます。

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify a landmark in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject LandmarkImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify a Landmark in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseLandmark;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-application"></a>アプリケーションの実行

**F6** キーを押してアプリケーションを実行します。 **[Subscription Key]\(サブスクリプション キー\)** フィールドにサブスクリプション キーを入力し、**[Subscription Region]\(サブスクリプション リージョン\)** で正しいリージョンが使用されていることを確認します。 **[ランドマーク]** タブをクリックし、ランドマークの画像の URL を入力した後、**[画像の分析]** lボタンをクリックして画像を分析し、その結果を確認します。

### <a name="recognize-celebrities"></a>著名人を認識する

Computer Vision の著名人機能は、有名な人物のイメージを分析します。 分析が完了すると、著名人機能はイメージで見つかった著名人を識別する JSON オブジェクトを返します。

チュートリアル アプリケーションの著名人機能を完成させるには、次の手順を実行します。

#### <a name="add-the-event-handler-code-for-the-form-button"></a>フォーム ボタンのイベント ハンドラー コードを追加する

**celebritiesImageButtonActionPerformed** イベント ハンドラー メソッドは、フォームをクリアし、URL で指定された画像を表示した後 **CelebritiesImage** メソッドを呼び出して画像を分析します。 **CelebritiesImage** が戻るとき、このメソッドは、書式設定された JSON 応答を **[応答]** テキスト領域に表示し、**JSONObject** から最初の有名人の名前を抽出し、その名前と有名人が正しく認識されたことを示す信頼度レベルを表示します。

次のコードをコピーして **celebritiesImageButtonActionPerformed** メソッドに貼り付けます。

> [!NOTE]
> NetBeans では、メソッド定義行 (```private void```) またはそのメソッドの中かっこに貼り付けることはできません。 コードをコピーするには、メソッド定義と中かっこの間の行をコピーし、それらをメソッドの内容の上に貼り付けます。

```java
    private void celebritiesImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL celebritiesImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        celebritiesImage.setIcon(new ImageIcon());
        celebritiesCaptionLabel.setText("");
        celebritiesResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            celebritiesImageUrl = new URL(celebritiesImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(celebritiesImageUrl);
            scaleAndShowImage(bImage, celebritiesImage);
        } catch(IOException e) {
            celebritiesResponseTextArea.setText("Error loading Celebrity image: " + e.getMessage());
            return;
        }
        
        // Identify the celebrities in the image.
        JSONObject jsonObj = CelebritiesImage(celebritiesImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        celebritiesResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("celebrities")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("celebrities").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                celebritiesCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 呼び出しのラッパーを追加する

**CelebritiesImage** メソッドは、イメージを分析するためにREST API 呼び出しをラップします。 このメソッドは、画像内で検出された有名人を記述する **JSONObject** を返します。エラーが発生した場合は **null** を返します。

**CelebritiesImage** メソッドをコピーして、**celebritiesImageButtonActionPerformed** メソッドのすぐ下に貼り付けます。

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify celebrities in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject CelebritiesImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseCelebrities;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-application"></a>アプリケーションの実行

**F6** キーを押してアプリケーションを実行します。 **[Subscription Key]\(サブスクリプション キー\)** フィールドにサブスクリプション キーを入力し、**[Subscription Region]\(サブスクリプション リージョン\)** で正しいリージョンが使用されていることを確認します。 **[有名人]** タブをクリックし、有名人の画像の URL を入力した後、**[画像の分析]** lボタンをクリックして画像を分析し、その結果を確認します。

### <a name="intelligently-generate-a-thumbnail"></a>サムネイルをインテリジェントに生成する

Computer Vision のサムネイル機能はイメージのサムネイルを生成します。 サムネイル機能は**スマート トリミング**機能を使用して、イメージ内の対象の領域を特定してサムネイルをその領域の中央に配置し、より美しいサムネイル イメージを生成します。

チュートリアル アプリケーションのサムネイル機能を完成させるには、次の手順を実行します。

#### <a name="add-the-event-handler-code-for-the-form-button"></a>フォーム ボタンのイベント ハンドラー コードを追加する

**thumbnailImageButtonActionPerformed** イベント ハンドラー メソッドは、フォームをクリアし、URL で指定された画像を表示した後、**getThumbnailImage** メソッドを呼び出してサムネイルを作成します。 **getThumbnailImage** が戻るとき、このメソッドは、生成したサムネイルを表示します。

次のコードをコピーして **thumbnailImageButtonActionPerformed** メソッドに貼り付けます。

> [!NOTE]
> NetBeans では、メソッド定義行 (```private void```) またはそのメソッドの中かっこに貼り付けることはできません。 コードをコピーするには、メソッド定義と中かっこの間の行をコピーし、それらをメソッドの内容の上に貼り付けます。

```java
    private void thumbnailImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL thumbnailImageUrl;
        JSONObject jsonError[] = new JSONObject[1];
        
        // Clear out the previous image, response, and thumbnail, if any.
        thumbnailSourceImage.setIcon(new ImageIcon());
        thumbnailResponseTextArea.setText("");
        thumbnailImage.setIcon(new ImageIcon());

        // Display the image specified in the text box.
        try {
            thumbnailImageUrl = new URL(thumbnailImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(thumbnailImageUrl);
            scaleAndShowImage(bImage, thumbnailSourceImage);
        } catch(IOException e) {
            thumbnailResponseTextArea.setText("Error loading image to thumbnail: " + e.getMessage());
            return;
        }
        
        // Get the thumbnail for the image.
        BufferedImage thumbnail = getThumbnailImage(thumbnailImageUrl.toString(), jsonError);
        
        // A non-null value indicates error.
        if (jsonError[0] != null) {
            // Format and display the JSON error.
            thumbnailResponseTextArea.setText(jsonError[0].toString(2));
            return;
        }
        
        // Display the thumbnail.
        if (thumbnail != null) {
            scaleAndShowImage(thumbnail, thumbnailImage);
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 呼び出しのラッパーを追加する

**getThumbnailImage** メソッドは、イメージを分析するために REST API 呼び出しをラップします。 このメソッドは、サムネイルが格納されている **BufferedImage** を返します。エラーが発生した場合は **null** を返します。 **jsonError** 文字列配列の最初の要素内にエラー メッセージが返されます。

次の **getThumbnailImage** メソッドをコピーし、**thumbnailImageButtonActionPerformed** メソッドのすぐ下に貼り付けます。

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to create a thumbnail for an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A BufferedImage containing the thumbnail, or null if a runtime error occurs. In the case
     * of an error, the error message will be returned in the first element of the jsonError string array.
     */
    private BufferedImage getThumbnailImage(String imageUrl, JSONObject[] jsonError) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseThumbnail;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity requestEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(requestEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200)
            {
                // Return the thumbnail.
                return ImageIO.read(entity.getContent());
            }
            else
            {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                jsonError[0] = new JSONObject(jsonString);
                return null;
            }
        }
        catch (Exception e)
        {
            String errorMessage = e.getMessage();
            System.out.println(errorMessage);
            jsonError[0] = new JSONObject(errorMessage);
            return null;
        }
    }
```

#### <a name="run-the-application"></a>アプリケーションの実行

**F6** キーを押してアプリケーションを実行します。 **[Subscription Key]\(サブスクリプション キー\)** フィールドにサブスクリプション キーを入力し、**[Subscription Region]\(サブスクリプション リージョン\)** で正しいリージョンが使用されていることを確認します。 **[サムネイル]** タブをクリックし、画像の URL を入力した後、**[サムネイルの生成]** をクリックして画像を分析し、その結果を確認します。

### <a name="read-printed-text-ocr"></a>印字されたテキストを読み取る (OCR)

Computer Vision の光学式文字認識 (OCR) 機能は、印字されたテキストのイメージを分析します。 分析が完了すると、OCR 機能はテキストとイメージ内のそのテキストの位置が含まれる JSON オブジェクトを返します。

チュートリアル アプリケーションの OCR 機能を完成させるには、次の手順を実行します。

#### <a name="add-the-event-handler-code-for-the-form-button"></a>フォーム ボタンのイベント ハンドラー コードを追加する

**ocrImageButtonActionPerformed** イベント ハンドラー メソッドは、フォームをクリアし、URL で指定された画像を表示した後、**OcrImage** メソッドを呼び出して画像を分析します。 **OcrImage** が戻るとき、このメソッドは、検出されたテキストを書式設定された JSON として **[応答]** テキスト領域に表示します。

次のコードをコピーして **ocrImageButtonActionPerformed** メソッドに貼り付けます。

> [!NOTE]
> NetBeans では、メソッド定義行 (```private void```) またはそのメソッドの中かっこに貼り付けることはできません。 コードをコピーするには、メソッド定義と中かっこの間の行をコピーし、それらをメソッドの内容の上に貼り付けます。

```java
    private void ocrImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL ocrImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        ocrImage.setIcon(new ImageIcon());
        ocrResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            ocrImageUrl = new URL(ocrImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(ocrImageUrl);
            scaleAndShowImage(bImage, ocrImage);
        } catch(IOException e) {
            ocrResponseTextArea.setText("Error loading OCR image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = OcrImage(ocrImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        ocrResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 呼び出しのラッパーを追加する

**OcrImage** メソッドは、イメージを分析するために REST API 呼び出しをラップします。 このメソッドは、呼び出しから返された JSON データの **JSONObject** を返します。エラーが発生した場合は **null** を返します。

**OcrImage** メソッドをコピーして、**ocrImageButtonActionPerformed** メソッドのすぐ下に貼り付けます。

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject OcrImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseOcr;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation ", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-application"></a>アプリケーションの実行

**F6** キーを押してアプリケーションを実行します。 **[Subscription Key]\(サブスクリプション キー\)** フィールドにサブスクリプション キーを入力し、**[Subscription Region]\(サブスクリプション リージョン\)** で正しいリージョンが使用されていることを確認します。 **[OCR]** タブをクリックし、テキストが印字されている画像の URL を入力し、**[Read Image]\(画像の読み取り\)** ボタンをクリックして画像を分析し、その結果を確認します。

### <a name="read-handwritten-text-handwriting-recognition"></a>手書きのテキストを読み取る (手書き認識)

Computer Vision の手書き認識機能は、手書きのテキストのイメージを分析します。 分析が完了すると、手書き認識機能はテキストとイメージ内のそのテキストの位置が含まれる JSON オブジェクトを返します。

チュートリアル アプリケーションの手書き認識機能を完成させるには、次の手順を実行します。

#### <a name="add-the-event-handler-code-for-the-form-button"></a>フォーム ボタンのイベント ハンドラー コードを追加する

**handwritingImageButtonActionPerformed** イベント ハンドラー メソッドは、フォームをクリアし、URL で指定された画像を表示した後、**HandwritingImage** メソッドを呼び出して画像を分析します。 **HandwritingImage** が戻るとき、このメソッドは、検出されたテキストを書式設定された JSON として **[応答]** テキスト領域に表示します。

次のコードをコピーして **handwritingImageButtonActionPerformed** メソッドに貼り付けます。

> [!NOTE]
> NetBeans では、メソッド定義行 (```private void```) またはそのメソッドの中かっこに貼り付けることはできません。 コードをコピーするには、メソッド定義と中かっこの間の行をコピーし、それらをメソッドの内容の上に貼り付けます。

```java
    private void handwritingImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL handwritingImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        handwritingImage.setIcon(new ImageIcon());
        handwritingResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            handwritingImageUrl = new URL(handwritingImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(handwritingImageUrl);
            scaleAndShowImage(bImage, handwritingImage);
        } catch(IOException e) {
            handwritingResponseTextArea.setText("Error loading Handwriting image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = HandwritingImage(handwritingImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        handwritingResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 呼び出しのラッパーを追加する

**HandwritingImage** メソッドは、画像を分析するために必要な 2 つの REST API 呼び出しをラップします。 手書き認識は時間のかかるプロセスであるため、2 段階のプロセスが使用されます。 最初の呼び出しはプロセスを行う画像を送信し、2 番目の呼び出しは、プロセスが完了したときに検出されたテキストを取得します。

テキストが取得された後、**HandwritingImage** メソッドは、テキストとテキストの場所を記述する **JSONObject** を返します。エラーが発生した場合は **null** を返します。

**HandwritingImage** メソッドをコピーして、**handwritingImageButtonActionPerformed** メソッドのすぐ下に貼り付けます。

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read handwritten text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject HandwritingImage(String imageUrl) {
        try (CloseableHttpClient textClient = HttpClientBuilder.create().build();
             CloseableHttpClient resultClient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseHandwriting;
            URIBuilder uriBuilder = new URIBuilder(uriString);
            
            // Request parameters.
            uriBuilder.setParameter("handwriting", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response.
            HttpResponse textResponse = textClient.execute(request);
            
            // Check for success.
            if (textResponse.getStatusLine().getStatusCode() != 202) {
                // An error occurred. Return the JSON error message.
                HttpEntity entity = textResponse.getEntity();
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            }
            
            String operationLocation = null;

            // The 'Operation-Location' in the response contains the URI to retrieve the recognized text.
            Header[] responseHeaders = textResponse.getAllHeaders();
            for(Header header : responseHeaders) {
                if(header.getName().equals("Operation-Location"))
                {
                    // This string is the URI where you can get the text recognition operation result.
                    operationLocation = header.getValue();
                    break;
                }
            }
            
            // NOTE: The response may not be immediately available. Handwriting recognition is an
            // async operation that can take a variable amount of time depending on the length
            // of the text you want to recognize. You may need to wait or retry this operation.
            //
            // This example checks once per second for ten seconds.
            
            JSONObject responseObj = null;
            int i = 0;
            do {
                // Wait one second.
                Thread.sleep(1000);
                
                // Check to see if the operation completed.
                HttpGet resultRequest = new HttpGet(operationLocation);
                resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());
                HttpResponse resultResponse = resultClient.execute(resultRequest);
                HttpEntity responseEntity = resultResponse.getEntity();
                if (responseEntity != null)
                {
                    // Get the JSON response.
                    String jsonString = EntityUtils.toString(responseEntity);
                    responseObj = new JSONObject(jsonString);
                }
            }
            while (i < 10 && responseObj != null &&
                    !responseObj.getString("status").equalsIgnoreCase("Succeeded"));
            
            // If the operation completed, return the JSON object.
            if (responseObj != null) {
                return responseObj;
            } else {
                // Return null for timeout error.
                System.out.println("Timeout error.");
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-application"></a>アプリケーションの実行

アプリケーションを実行するには、**F6** キーを押します。 **[Subscription Key]\(サブスクリプション キー\)** フィールドにサブスクリプション キーを入力し、**[Subscription Region]\(サブスクリプション リージョン\)** で正しいリージョンが使用されていることを確認します。 **[Read Handwritten Text]\(手書きのテキストの読み取り\)** タブをクリックし、テキストが手書きされている画像の URL を入力し、**[Read Image]\(画像の読み取り\)** ボタンをクリックして画像を分析し、その結果を確認します。

## <a name="next-steps"></a>次の手順

- [Computer Vision API C&#35; チュートリアル](CSharpTutorial.md)
- [Computer Vision API Python チュートリアル](PythonTutorial.md)
