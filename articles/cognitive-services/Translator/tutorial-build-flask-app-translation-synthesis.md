---
title: チュートリアル:テキストの翻訳、音声合成、分析を行う Flask アプリを作成する - Translator Text API
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、テキストの翻訳、感情分析、翻訳済みテキストの音声合成を行う Flask ベースの Web アプリを作成します。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 12/09/2019
ms.author: swmachan
ms.openlocfilehash: 5e19e4247e58135c6a9bb7c6cc327cb566d294df
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978460"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>チュートリアル:Azure Cognitive Services を使用して Flask アプリを作成する

このチュートリアルでは、Azure Cognitive Services を使用してテキストの翻訳、感情分析、翻訳済みテキストの音声合成を行う Flask Web アプリを作成します。 重点的に取り上げるのは目的のアプリケーションを実現する Python コードと Flask ルートですが、アプリを実現する HTML と JavaScript についてもサポートを提供する予定です。 何か問題があれば、下のフィードバック ボタンを使用してお知らせください。

このチュートリアルの内容:

> [!div class="checklist"]
> * Azure サブスクリプション キーを取得する
> * 開発環境をセットアップして依存関係をインストールする
> * Flask アプリを作成する
> * Translator Text API を使用してテキストを翻訳する
> * Text Analytics を使用して、入力されたテキストと翻訳の肯定的/否定的感情を分析する
> * Speech Services を使用して、翻訳済みのテキストを合成音声に変換する
> * Flask アプリをローカルで実行する

> [!TIP]
> この記事の説明をスキップして、すべてのコードを一度にご覧になりたい場合は、[GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial) でサンプル全体とビルド手順を参照してください。

## <a name="what-is-flask"></a>Flask とは

Flask は、Web アプリケーションを作成するためのマイクロフレームワークです。 つまり Flask には、Web アプリケーションを作成するためのツールやライブラリ、テクノロジが用意されています。 Web ページやブログ、Wiki はもちろん、Web ベースの予定表アプリケーションや商用 Web サイトといった本格的な Web アプリケーションを作成することもできます。

このチュートリアルを読み終えた後で、さらに詳しく学びたい方は、次のリンク先のドキュメントを参照してください。

* [Flask のドキュメント](http://flask.pocoo.org/)
* [Flask for Dummies - Flask ビギナーズ ガイド](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>前提条件

このチュートリアルに必要なソフトウェアとサブスクリプション キーを確認しましょう。

* [Python 3.5.2 以降](https://www.python.org/downloads/)
* [Git ツール](https://git-scm.com/downloads)
* IDE またはテキスト エディター ([Visual Studio Code](https://code.visualstudio.com/)、[Atom](https://atom.io/) など)  
* [Chrome](https://www.google.com/chrome/browser/) または [Firefox](https://www.mozilla.org/firefox)
* **Translator Text** のサブスクリプション キー (リージョンの選択は不要)
* **Text Analytics** のサブスクリプション キー (**米国西部**リージョン)。
* **Speech Services** のサブスクリプション キー (**米国西部**リージョン)。

## <a name="create-an-account-and-subscribe-to-resources"></a>アカウントを作成してリソースをサブスクライブする

前述のように、このチュートリアルには 3 つのサブスクリプション キーが必要となります。 つまり、次の目的に使用するリソースをご自分の Azure アカウント内に作成する必要があります。
* Translator Text
* Text Analytics
* Speech Services

リソースを作成する具体的な手順については、[Azure portal での Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページを参照してください。

> [!IMPORTANT]
> このチュートリアルでは、必要なリソースを米国西部リージョンに作成してください。 別のリージョンを使用する場合は、各 Python ファイルでベース URL を調整する必要があります。

## <a name="set-up-your-dev-environment"></a>開発環境を設定する

Flask Web アプリを作成する前に、プロジェクトの作業ディレクトリを作成して、いくつかの Python パッケージをインストールする必要があります。

### <a name="create-a-working-directory"></a>作業ディレクトリを作成する

1. コマンド ライン (Windows) またはターミナル (macOS/Linux) を開きます。 次に、プロジェクトの作業ディレクトリとサブ ディレクトリを作成します。  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. プロジェクトの作業ディレクトリに移動します。  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>`virtualenv` で仮想環境を作成してアクティブ化する

`virtualenv` を使用して Flask アプリの仮想環境を作成しましょう。 仮想環境を使用することで、確実にクリーンな作業環境が手に入ります。

1. 作業ディレクトリで、次のコマンドを実行して仮想環境を作成します: **macOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   仮想環境で Python 3 を使用するよう明示的に宣言しました。 これで、ユーザーが複数バージョンの Python をインストールしていても正しいバージョンが使用されます。

   **Windows CMD/Windows Bash:**
   ```
   virtualenv venv
   ```
   ここではシンプルに、venv という名前を仮想環境に付けています。

2. 仮想環境をアクティブ化するコマンドは、プラットフォーム/シェルによって異なります。   

   | プラットフォーム | シェル | command |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | Bash | `source venv/Scripts/activate` |
   | | コマンド ライン | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   このコマンドを実行した後は、コマンド ライン セッションまたはターミナル セッションを `venv` で始める必要があります。

3. セッションは、コマンド ラインまたはターミナルに「`deactivate`」と入力することで、いつでも非アクティブ化することができます。

> [!NOTE]
> Python には、仮想環境の作成と管理に関するドキュメントが豊富にあります。[virtualenv](https://virtualenv.pypa.io/en/latest/) に関するページを参照してください。

### <a name="install-requests"></a>requests をインストールする

requests は、HTTP 1.1 要求を送信するためのモジュールとして広く使われています。 クエリ文字列を URL に手動で追加したり、POST データをフォームエンコードしたりする必要はありません。

1. requests をインストールするには、次を実行します。

   ```
   pip install requests
   ```

> [!NOTE]
> requests の詳細については、「[Requests: HTTP for Humans (Requests: 人のための HTTP)](http://docs.python-requests.org/en/master/)」を参照してください。

### <a name="install-and-configure-flask"></a>Flask をインストールして構成する

次に、Flask をインストールする必要があります。 Web アプリのルーティングが Flask によって処理されるので、サブスクリプション キーをエンド ユーザーには見せずにサーバー間の呼び出しを行うことができます。

1. Flask をインストールするには、次を実行します。
   ```
   pip install Flask
   ```
   Flask がインストールされたことを確認しましょう。 次のコマンドを実行します。
   ```
   flask --version
   ```
   バージョンがターミナルに出力されます。 それ以外の場合、なんらかの問題があります。

2. Flask アプリを実行するには、flask コマンドを使用するか、Python の -m スイッチを Flask で使用します。 その前に、`FLASK_APP` 環境変数をエクスポートして、使用するアプリをターミナルに伝える必要があります。

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Flask アプリを作成する

このセクションでは、ユーザーがアプリのルートに移動したときに HTML ファイルを返す最小限の Flask アプリを作成します。 コードの細かな点はあまり気にしないでください。このファイルは後からまた更新します。

### <a name="what-is-a-flask-route"></a>Flask ルートとは

"[ルート](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)" について少し触れておきます。 ルーティングは、URL を特定の関数にバインドする目的で使用されます。 Flask は、ルート デコレーターを使用して、特定の URL に関数を登録します。 たとえば、ユーザーが Web アプリのルート (`/`) に移動すると、`index.html` がレンダリングされます。  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

この点をよく理解するために、もう 1 つの例を見てみましょう。

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

このコードでは、ユーザーが `http://your-web-app.com/about` に移動すると、`about.html` ファイルがレンダリングされます。

これらのサンプルは、html ページをレンダリングしてユーザーに表示する方法を示していますが、ルートを使用すれば、ボタンが押されたときに API を呼び出したり、ホームページから離れることなく任意の数のアクションを実行したりすることもできます。 この点は、翻訳、感情分析、音声合成のルートを作成するときに実際に見てみましょう。

### <a name="get-started"></a>作業開始

1. IDE でプロジェクトを開いて、作業ディレクトリのルートに `app.py` という名前のファイルを作成します。 さらに、次のコードを `app.py` にコピーして保存します。

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   このコード ブロックは、ユーザーが Web アプリのルート (`/`) に移動するたびに `index.html` を表示するようアプリに命令しています。

2. 次に、Web アプリのフロントエンドを作成してみましょう。 `templates` ディレクトリに `index.html` という名前のファイルを作成します。 さらに、このコードを `templates/index.html` にコピーします。

   ```html
   <!doctype html>
   <html lang="en">
     <head>
       <!-- Required metadata tags -->
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
       <meta name="description" content="Translate and analyze text with Azure Cognitive Services.">
       <!-- Bootstrap CSS -->
       <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
       <title>Translate and analyze text with Azure Cognitive Services</title>
     </head>
     <body>
       <div class="container">
         <h1>Translate, synthesize, and analyze text with Azure</h1>
         <p>This simple web app uses Azure for text translation, text-to-speech conversion, and sentiment analysis of input text and translations. Learn more about <a href="https://docs.microsoft.com/azure/cognitive-services/">Azure Cognitive Services</a>.
        </p>
        <!-- HTML provided in the following sections goes here. -->

        <!-- End -->
       </div>

       <!-- Required Javascript for this tutorial -->
       <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
       <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
       <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
       <script type = "text/javascript" src ="static/scripts/main.js"></script>
     </body>
   </html>
   ```

3. Flask アプリをテストしてみましょう。 ターミナルから、次を実行します。

   ```
   flask run
   ```

4. ブラウザーを開き、指定した URL に移動します。 作成したシングル ページ アプリが表示されると思います。 **Ctrl + C** キーを押してアプリを強制終了してください。

## <a name="translate-text"></a>テキストを翻訳する

ここまでは、シンプルな Flask アプリの動作を確認しました。今度は、次のことを試してみましょう。

* Translator Text API を呼び出して応答を返す Python を記述する
* その Python コードを呼び出す Flask ルートを作成する
* テキスト入力と翻訳のための領域、言語セレクター、翻訳ボタンを HTML に追加する
* ユーザーが HTML から Flask アプリを対話的に操作できる JavaScript を記述する

### <a name="call-the-translator-text-api"></a>Translator Text API を呼び出す

最初に、Translator Text API を呼び出す関数を記述する必要があります。 この関数は、`text_input` と `language_output` の 2 つの引数を受け取ります。 ユーザーがアプリで翻訳ボタンを押すたびに、この関数が呼び出されます。 HTML 内のテキスト領域が `text_input` として送信され、HTML で選択された言語の値が `language_output` として送信されます。

1. まず、作業ディレクトリのルートに `translate.py` というファイルを作成します。
2. さらに、次のコードを `translate.py` に追加します。 この関数は、`text_input` と `language_output` の 2 つの引数を受け取ります。
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   
   # Don't forget to replace with your Cog Services location!
   # Our Flask route will supply two arguments: text_input and language_output.
   # When the translate text button is pressed in our Flask app, the Ajax request
   # will grab these values from our web app, and use them in the request.
   # See main.js for Ajax calls.
   def get_translation(text_input, language_output):
       base_url = 'https://api.cognitive.microsofttranslator.com'
       path = '/translate?api-version=3.0'
       params = '&to=' + language_output
       constructed_url = base_url + path + params

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Ocp-Apim-Subscription-Region': 'location',
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = [{
           'text' : text_input
       }]
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Translator Text のサブスクリプション キーを追加して保存します。

### <a name="add-a-route-to-apppy"></a>`app.py` にルートを追加する

次に、`translate.py` を呼び出すルートを Flask アプリに作成する必要があります。 ユーザーがアプリで翻訳ボタンを押すたびに、このルートが呼び出されます。

このアプリでは、作成したルートが `POST` 要求を受け取ります。 これは、翻訳対象のテキストとその翻訳の出力言語が関数に必要となるためです。

Flask には、各要求の解析と管理に役立つヘルパー関数が用意されています。 記載したコードの `get_json()` は、`POST` 要求からのデータを JSON として返します。 次に、`data['text']` と `data['to']` を使用して、`translate.py` の `get_translation()` 関数にテキストと出力言語の値を渡します。 最後にその応答を JSON として返します。このデータを Web アプリ内で表示することになります。

以降のセクションでは、このプロセスを繰り返して、感情分析と音声合成のためのルートを作成します。

1. `app.py` を開いて、`app.py` の先頭の import ステートメントを探し、次の行を追加します。

   ```python
   import translate
   ```
   これで、`translate.py` にあるメソッドを Flask アプリで使用できるようになります。

2. 次のコードを `app.py` の末尾にコピーして保存します。

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>`index.html` を更新する

これで、テキストを翻訳する関数とそれを呼び出す Flask アプリのルートが完成しました。次に、アプリの HTML の構築を始めましょう。 以下の HTML では、いくつかの処理を行います。

* ユーザーが翻訳対象テキストを入力するためのテキスト領域を指定する。
* 言語セレクターを追加する。
* 検出された言語と翻訳中に返された信頼度スコアをレンダリングするための HTML 要素を追加する。
* 出力された翻訳を表示する読み取り専用のテキスト領域を指定する。
* 後でこのチュートリアルの中でこのファイルに追加することになる、感情分析と音声合成のコードに使用するプレースホルダーを追加する。

それでは、`index.html` を更新していきましょう。

1. `index.html` を開いて、次のコード コメントを見つけます。
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. これらのコード コメントを次の HTML ブロックに置き換えます。
   ```html
   <div class="row">
     <div class="col">
       <form>
         <!-- Enter text to translate. -->
         <div class="form-group">
           <label for="text-to-translate"><strong>Enter the text you'd like to translate:</strong></label>
           <textarea class="form-control" id="text-to-translate" rows="5"></textarea>
         </div>
         <!-- Select output language. -->
         <div class="form-group">
           <label for="select-language"><strong>Translate to:</strong></label>
           <select class="form-control" id="select-language">
             <option value="ar">Arabic</option>
             <option value="ca">Catalan</option>
             <option value="zh-Hans">Chinese (Simplified)</option>
             <option value="zh-Hant">Chinese (Traditional)</option>
             <option value="hr">Croatian</option>
             <option value="en">English</option>
             <option value="fr">French</option>
             <option value="de">German</option>
             <option value="el">Greek</option>
             <option value="he">Hebrew</option>
             <option value="hi">Hindi</option>
             <option value="it">Italian</option>
             <option value="ja">Japanese</option>
             <option value="ko">Korean</option>
             <option value="pt">Portuguese</option>
             <option value="ru">Russian</option>
             <option value="es">Spanish</option>
             <option value="th">Thai</option>
             <option value="tr">Turkish</option>
             <option value="vi">Vietnamese</option>
           </select>
         </div>
         <button type="submit" class="btn btn-primary mb-2" id="translate">Translate text</button></br>
         <div id="detected-language" style="display: none">
           <strong>Detected language:</strong> <span id="detected-language-result"></span><br />
           <strong>Detection confidence:</strong> <span id="confidence"></span><br /><br />
         </div>

         <!-- Start sentiment code-->

         <!-- End sentiment code -->

       </form>
     </div>
     <div class="col">
       <!-- Translated text returned by the Translate API is rendered here. -->
       <form>
         <div class="form-group" id="translator-text-response">
           <label for="translation-result"><strong>Translated text:</strong></label>
           <textarea readonly class="form-control" id="translation-result" rows="5"></textarea>
         </div>

         <!-- Start voice font selection code -->

         <!-- End voice font selection code -->

       </form>

       <!-- Add Speech Synthesis button and audio element -->

       <!-- End Speech Synthesis button -->

     </div>
   </div>
   ```

次の手順で、必要な JavaScript を記述します。 これが HTML と Flask ルートの間の架け橋になります。

### <a name="create-mainjs"></a>`main.js` を作成します。  

`main.js` ファイルは、HTML と Flask ルートとの間の架け橋になります。 アプリは jQuery、Ajax、XMLHttpRequest の組み合わせを使用してコンテンツをレンダリングし、Flask ルートに `POST` 要求を送信します。

以下のコードでは、HTML から取得した内容を使用して、Flask ルートへの要求を作成しています。 具体的には、テキスト領域の内容と言語セレクターを変数に代入したうえで、要求に追加し、`translate-text` に渡しています。

その後、応答を反復処理しながら、翻訳、検出言語、信頼度スコアで HTML を更新します。

1. IDE で、`main.js` という名前のファイルを `static/scripts` ディレクトリに作成します。
2. このコードを `static/scripts/main.js` にコピーします。
   ```javascript
   //Initiate jQuery on load.
   $(function() {
     //Translate text with flask route
     $("#translate").on("click", function(e) {
       e.preventDefault();
       var translateVal = document.getElementById("text-to-translate").value;
       var languageVal = document.getElementById("select-language").value;
       var translateRequest = { 'text': translateVal, 'to': languageVal }

       if (translateVal !== "") {
         $.ajax({
           url: '/translate-text',
           method: 'POST',
           headers: {
               'Content-Type':'application/json'
           },
           dataType: 'json',
           data: JSON.stringify(translateRequest),
           success: function(data) {
             for (var i = 0; i < data.length; i++) {
               document.getElementById("translation-result").textContent = data[i].translations[0].text;
               document.getElementById("detected-language-result").textContent = data[i].detectedLanguage.language;
               if (document.getElementById("detected-language-result").textContent !== ""){
                 document.getElementById("detected-language").style.display = "block";
               }
               document.getElementById("confidence").textContent = data[i].detectedLanguage.score;
             }
           }
         });
       };
     });
     // In the following sections, you'll add code for sentiment analysis and
     // speech synthesis here.
   })
   ```

### <a name="test-translation"></a>翻訳をテストする

アプリで翻訳をテストしましょう。

```
flask run
```

指定されたサーバー アドレスに移動します。 入力領域にテキストを入力し、言語を選択して、翻訳ボタンをクリックします。 これで翻訳が表示されます。 正しく機能しない場合は、サブスクリプション キーを追加してあることを確認してください。

> [!TIP]
> 先ほど行った変更が反映されていない場合や、思いどおりにアプリが動作しない場合は、キャッシュをクリアするか、プライベート ウィンドウやシークレット ウィンドウを開いてみてください。

**Ctrl + C** キーを押してアプリを強制終了してから、次のセクションに進みましょう。

## <a name="analyze-sentiment"></a>感情を分析する

[Text Analytics API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) を使用すると、感情分析を実行したり、テキストからキー フレーズを抽出したり、ソース言語を検出したりすることができます。 このアプリでは、入力されたテキストがポジティブかニュートラルかネガティブかを感情分析を使用して調べます。 この API は 0 から 1 までの数値スコアを返します。 1 に近いスコアは正の感情、0 に近いスコアは負の感情を示します。

このセクションでは、次の作業を行います。

* Text Analytics API を呼び出して感情分析を行い、応答を返す Python を記述する
* その Python コードを呼び出す Flask ルートを作成する
* 感情スコアの領域と分析の実行ボタンを HTML に追加する
* ユーザーが HTML から Flask アプリを対話的に操作できる JavaScript を記述する

### <a name="call-the-text-analytics-api"></a>Text Analytics API を呼び出す

Text Analytics API を呼び出す関数を記述しましょう。 この関数は、`input_text`、`input_language`、`output_text`、`output_language` の 4 つの引数を受け取ります。 ユーザーがアプリで感情分析の実行ボタンを押すたびに、この関数が呼び出されます。 テキスト領域と言語セレクターを通じてユーザーが入力したデータ、そして検出された言語と出力された翻訳が、それぞれの要求で渡されます。 応答オブジェクトには、原文と翻訳の感情スコアが含まれます。 以降のセクションでは、その応答を解析してアプリ内で使用するための JavaScript を記述していきます。 その前に、Text Analytics API の呼び出しに注目しましょう。

1. 作業ディレクトリのルートに `sentiment.py` というファイルを作成します。
2. さらに、次のコードを `sentiment.py` に追加します。
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'

   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language, output_text, output_language):
       base_url = 'https://westus.api.cognitive.microsoft.com/text/analytics'
       path = '/v2.0/sentiment'
       constructed_url = base_url + path

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = {
           'documents': [
               {
                   'language': input_language,
                   'id': '1',
                   'text': input_text
               },
               {
                   'language': output_language,
                   'id': '2',
                   'text': output_text
               }
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Text Analytics のサブスクリプション キーを追加して保存します。

### <a name="add-a-route-to-apppy"></a>`app.py` にルートを追加する

`sentiment.py` を呼び出すルートを Flask アプリに作成しましょう。 ユーザーがアプリで感情分析の実行ボタンを押すたびに、このルートが呼び出されます。 翻訳のルートと同様、対応する関数が引数を受け取るため、このルートは `POST` 要求を受け取ります。

1. `app.py` を開いて、`app.py` の先頭の import ステートメントを探し、次のように更新します。

   ```python
   import translate, sentiment
   ```
   これで、`sentiment.py` にあるメソッドを Flask アプリで使用できるようになります。

2. 次のコードを `app.py` の末尾にコピーして保存します。
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       output_text = data['outputText']
       output_lang =  data['outputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang, output_text, output_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>`index.html` を更新する

これで、感情分析を実行する関数とそれを呼び出す Flask アプリのルートが完成しました。次に、アプリの HTML の記述を始めましょう。 以下の HTML では、いくつかの処理を行います。

* 感情分析の実行ボタンをアプリに追加する
* 感情スコアについて説明する要素を追加する
* 感情スコアを表示する要素を追加する

1. `index.html` を開いて、次のコード コメントを見つけます。
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. これらのコード コメントを次の HTML ブロックに置き換えます。
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>`main.js` を更新する

以下のコードでは、HTML から取得した内容を使用して、Flask ルートへの要求を作成しています。 具体的には、テキスト領域の内容と言語セレクターを変数に代入したうえで、要求に追加し、`sentiment-analysis` ルートに渡しています。

その後、応答を反復処理しながら、感情スコアで HTML を更新します。

1. IDE で、`main.js` という名前のファイルを `static` ディレクトリに作成します。

2. このコードを `static/scripts/main.js` にコピーします。
   ```javascript
   //Run sentinment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage, "outputText": outputText,  "outputLanguage": outputLanguage };

     if (inputText !== "") {
       $.ajax({
         url: "/sentiment-analysis",
         method: "POST",
         headers: {
             "Content-Type":"application/json"
         },
         dataType: "json",
         data: JSON.stringify(sentimentRequest),
         success: function(data) {
           for (var i = 0; i < data.documents.length; i++) {
             if (typeof data.documents[i] !== "undefined"){
               if (data.documents[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.documents[i].score;
               }
               if (data.documents[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.documents[i].score;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
               if (data.errors[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== '' && document.getElementById("translation-sentiment").textContent !== ""){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>感情分析をテストする

アプリで感情分析をテストしてみましょう。

```
flask run
```

指定されたサーバー アドレスに移動します。 入力領域にテキストを入力し、言語を選択して、翻訳ボタンをクリックします。 これで翻訳が表示されます。 次に、感情分析の実行ボタンをクリックします。 2 つのスコアが表示されます。 正しく機能しない場合は、サブスクリプション キーを追加してあることを確認してください。

> [!TIP]
> 先ほど行った変更が反映されていない場合や、思いどおりにアプリが動作しない場合は、キャッシュをクリアするか、プライベート ウィンドウやシークレット ウィンドウを開いてみてください。

**Ctrl + C** キーを押してアプリを強制終了してから、次のセクションに進みましょう。

## <a name="convert-text-to-speech"></a>テキストを音声に変換する

[Text-to-Speech API](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) により、アプリでテキストを人の声に近い自然な合成音声に変換できます。 このサービスは、標準音声、ニューラル音声、カスタム音声をサポートします。 サンプル アプリで使用しているのは、利用できる音声のほんの一部にすぎません。すべての音声の一覧については、[サポートされている言語](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)に関するページを参照してください。

このセクションでは、次の作業を行います。

* Text-to-Speech API を使用してテキストを音声に変換する Python を記述する
* その Python コードを呼び出す Flask ルートを作成する
* テキストを音声に変換するボタンと音声再生用の要素を HTML に追加する
* ユーザーが Flask アプリを対話的に操作できる JavaScript を記述する

### <a name="call-the-text-to-speech-api"></a>Text-to-Speech API を呼び出す

テキストを音声に変換する関数を記述しましょう。 この関数は、`input_text` と `voice_font` の 2 つの引数を受け取ります。 ユーザーがアプリでテキストから音声への変換ボタンを押すたびに、この関数が呼び出されます。 `input_text` は、テキスト翻訳の呼び出しから返された翻訳出力であり、`voice_font` は、HTML の音声フォント セレクターの値です。

1. 作業ディレクトリのルートに `synthesize.py` というファイルを作成します。

2. さらに、次のコードを `synthesize.py` に追加します。
   ```Python
   import os, requests, time
   from xml.etree import ElementTree

   class TextToSpeech(object):
       def __init__(self, input_text, voice_font):
           subscription_key = 'YOUR_SPEECH_SERVICES_SUBSCRIPTION_KEY'
           self.subscription_key = subscription_key
           self.input_text = input_text
           self.voice_font = voice_font
           self.timestr = time.strftime('%Y%m%d-%H%M')
           self.access_token = None

       # This function performs the token exchange.
       def get_token(self):
           fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
           headers = {
               'Ocp-Apim-Subscription-Key': self.subscription_key
           }
           response = requests.post(fetch_token_url, headers=headers)
           self.access_token = str(response.text)

       # This function calls the TTS endpoint with the access token.
       def save_audio(self):
           base_url = 'https://westus.tts.speech.microsoft.com/'
           path = 'cognitiveservices/v1'
           constructed_url = base_url + path
           headers = {
               'Authorization': 'Bearer ' + self.access_token,
               'Content-Type': 'application/ssml+xml',
               'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
               'User-Agent': 'YOUR_RESOURCE_NAME',
           }
           # Build the SSML request with ElementTree
           xml_body = ElementTree.Element('speak', version='1.0')
           xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
           voice = ElementTree.SubElement(xml_body, 'voice')
           voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
           voice.set('name', 'Microsoft Server Speech Text to Speech Voice {}'.format(self.voice_font))
           voice.text = self.input_text
           # The body must be encoded as UTF-8 to handle non-ascii characters.
           body = ElementTree.tostring(xml_body, encoding="utf-8")

           #Send the request
           response = requests.post(constructed_url, headers=headers, data=body)

           # Write the response as a wav file for playback. The file is located
           # in the same directory where this sample is run.
           return response.content
   ```
3. Speech Services のサブスクリプション キーを追加して保存します。

### <a name="add-a-route-to-apppy"></a>`app.py` にルートを追加する

`synthesize.py` を呼び出すルートを Flask アプリに作成しましょう。 ユーザーがアプリでテキストから音声への変換ボタンを押すたびに、このルートが呼び出されます。 翻訳と感情分析のルートと同様、対応する関数が 2 つの引数 (音声合成の対象となるテキストと再生に使用する音声フォント) を受け取るため、このルートは `POST` 要求を受け取ります。

1. `app.py` を開いて、`app.py` の先頭の import ステートメントを探し、次のように更新します。

   ```python
   import translate, sentiment, synthesize
   ```
   これで、`synthesize.py` にあるメソッドを Flask アプリで使用できるようになります。

2. 次のコードを `app.py` の末尾にコピーして保存します。

   ```Python
   @app.route('/text-to-speech', methods=['POST'])
   def text_to_speech():
       data = request.get_json()
       text_input = data['text']
       voice_font = data['voice']
       tts = synthesize.TextToSpeech(text_input, voice_font)
       tts.get_token()
       audio_response = tts.save_audio()
       return audio_response
   ```

### <a name="update-indexhtml"></a>`index.html` を更新する

これで、テキストから音声への変換を行う関数とそれを呼び出す Flask アプリのルートが完成しました。次に、アプリの HTML の記述を始めましょう。 以下の HTML では、いくつかの処理を行います。

* 音声選択ドロップダウンを提供する
* テキストを音声に変換するボタンを追加する
* 合成音声の再生に使用する audio 要素を追加する

1. `index.html` を開いて、次のコード コメントを見つけます。
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. これらのコード コメントを次の HTML ブロックに置き換えます。
   ```html
   <div class="form-group">
     <label for="select-voice"><strong>Select voice font:</strong></label>
     <select class="form-control" id="select-voice">
       <option value="(ar-SA, Naayf)">Arabic | Male | Naayf</option>
       <option value="(ca-ES, HerenaRUS)">Catalan | Female | HerenaRUS</option>
       <option value="(zh-CN, HuihuiRUS)">Chinese (Mainland) | Female | HuihuiRUS</option>
       <option value="(zh-CN, Kangkang, Apollo)">Chinese (Mainland) | Male | Kangkang, Apollo</option>
       <option value="(zh-HK, Tracy, Apollo)">Chinese (Hong Kong)| Female | Tracy, Apollo</option>
       <option value="(zh-HK, Danny, Apollo)">Chinese (Hong Kong) | Male | Danny, Apollo</option>
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yaiting, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, Jessa24kRUS)">English (US) | Female | Jessa24kRUS</option>
       <option value="(en-US, Guy24kRUS)">English (US) | Male | Guy24kRUS</option>
       <option value="(en-IE, Sean)">English (IE) | Male | Sean</option>
       <option value="(fr-FR, Julie, Apollo)">French | Female | Julie, Apollo</option>
       <option value="(fr-FR, HortenseRUS)">French | Female | Julie, HortenseRUS</option>
       <option value="(fr-FR, Paul, Apollo)">French | Male | Paul, Apollo</option>
       <option value="(de-DE, Hedda)">German | Female | Hedda</option>
       <option value="(de-DE, HeddaRUS)">German | Female | HeddaRUS</option>
       <option value="(de-DE, Stefan, Apollo)">German | Male | Apollo</option>
       <option value="(el-GR, Stefanos)">Greek | Male | Stefanos</option>
       <option value="(he-IL, Asaf)">Hebrew (Isreal) | Male | Asaf</option>
       <option value="(hi-IN, Kalpana, Apollo)">Hindi | Female | Kalpana, Apollo</option>
       <option value="(hi-IN, Hemant)">Hindi | Male | Hemant</option>
       <option value="(it-IT, LuciaRUS)">Italian | Female | LuciaRUS</option>
       <option value="(it-IT, Cosimo, Apollo)">Italian | Male | Cosimo, Apollo</option>
       <option value="(ja-JP, Ichiro, Apollo)">Japanese | Male | Ichiro</option>
       <option value="(ja-JP, HarukaRUS)">Japanese | Female | HarukaRUS</option>
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Haemi</option>
       <option value="(pt-BR, HeloisaRUS)">Portuguese (Brazil) | Female | HeloisaRUS</option>
       <option value="(pt-BR, Daniel, Apollo)">Portuguese (Brazil) | Male | Daniel, Apollo</option>
       <option value="(pt-PT, HeliaRUS)">Portuguese (Portugal) | Female | HeliaRUS</option>
       <option value="(ru-RU, Irina, Apollo)">Russian | Female | Irina, Apollo</option>
       <option value="(ru-RU, Pavel, Apollo)">Russian | Male | Pavel, Apollo</option>
       <option value="(ru-RU, EkaterinaRUS)">Russian | Female | EkaterinaRUS</option>
       <option value="(es-ES, Laura, Apollo)">Spanish | Female | Laura, Apollo</option>
       <option value="(es-ES, HelenaRUS)">Spanish | Female | HelenaRUS</option>
       <option value="(es-ES, Pablo, Apollo)">Spanish | Male | Pablo, Apollo</option>
       <option value="(th-TH, Pattara)">Thai | Male | Pattara</option>
       <option value="(tr-TR, SedaRUS)">Turkish | Female | SedaRUS</option>
       <option value="(vi-VN, An)">Vietnamese | Male | An</option>
     </select>
   </div>
   ```

3. 続けて、次のコード コメントを見つけます。
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. これらのコード コメントを次の HTML ブロックに置き換えます。

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. 作業内容を保存します。

### <a name="update-mainjs"></a>`main.js` を更新する

以下のコードでは、HTML から取得した内容を使用して、Flask ルートへの要求を作成しています。 具体的には、翻訳と音声フォントを変数に代入したうえで、要求に追加し、`text-to-speech` ルートに渡しています。

その後、応答を反復処理しながら、感情スコアで HTML を更新します。

1. IDE で、`main.js` という名前のファイルを `static` ディレクトリに作成します。
2. このコードを `static/scripts/main.js` にコピーします。
   ```javascript
   // Convert text-to-speech
   $("#text-to-speech").on("click", function(e) {
     e.preventDefault();
     var ttsInput = document.getElementById("translation-result").value;
     var ttsVoice = document.getElementById("select-voice").value;
     var ttsRequest = { 'text': ttsInput, 'voice': ttsVoice }

     var xhr = new XMLHttpRequest();
     xhr.open("post", "/text-to-speech", true);
     xhr.setRequestHeader("Content-Type", "application/json");
     xhr.responseType = "blob";
     xhr.onload = function(evt){
       if (xhr.status === 200) {
         audioBlob = new Blob([xhr.response], {type: "audio/mpeg"});
         audioURL = URL.createObjectURL(audioBlob);
         if (audioURL.length > 5){
           var audio = document.getElementById("audio");
           var source = document.getElementById("audio-source");
           source.src = audioURL;
           audio.load();
           audio.play();
         }else{
           console.log("An error occurred getting and playing the audio.")
         }
       }
     }
     xhr.send(JSON.stringify(ttsRequest));
   });
   // Code for automatic language selection goes here.
   ```
3. あともう少しで終了です。 最後に、翻訳用に選択された言語に基づいて音声フォントを自動的に選択するためのコードを `main.js` に追加します。 次のコード ブロックを `main.js` に追加してください。
   ```javascript
   // Automatic voice font selection based on translation output.
   $('select[id="select-language"]').change(function(e) {
     if ($(this).val() == "ar"){
       document.getElementById("select-voice").value = "(ar-SA, Naayf)";
     }
     if ($(this).val() == "ca"){
       document.getElementById("select-voice").value = "(ca-ES, HerenaRUS)";
     }
     if ($(this).val() == "zh-Hans"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "zh-Hant"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "hr"){
       document.getElementById("select-voice").value = "(hr-HR, Matej)";
     }
     if ($(this).val() == "en"){
       document.getElementById("select-voice").value = "(en-US, Jessa24kRUS)";
     }
     if ($(this).val() == "fr"){
       document.getElementById("select-voice").value = "(fr-FR, HortenseRUS)";
     }
     if ($(this).val() == "de"){
       document.getElementById("select-voice").value = "(de-DE, HeddaRUS)";
     }
     if ($(this).val() == "el"){
       document.getElementById("select-voice").value = "(el-GR, Stefanos)";
     }
     if ($(this).val() == "he"){
       document.getElementById("select-voice").value = "(he-IL, Asaf)";
     }
     if ($(this).val() == "hi"){
       document.getElementById("select-voice").value = "(hi-IN, Kalpana, Apollo)";
     }
     if ($(this).val() == "it"){
       document.getElementById("select-voice").value = "(it-IT, LuciaRUS)";
     }
     if ($(this).val() == "ja"){
       document.getElementById("select-voice").value = "(ja-JP, HarukaRUS)";
     }
     if ($(this).val() == "ko"){
       document.getElementById("select-voice").value = "(ko-KR, HeamiRUS)";
     }
     if ($(this).val() == "pt"){
       document.getElementById("select-voice").value = "(pt-BR, HeloisaRUS)";
     }
     if ($(this).val() == "ru"){
       document.getElementById("select-voice").value = "(ru-RU, EkaterinaRUS)";
     }
     if ($(this).val() == "es"){
       document.getElementById("select-voice").value = "(es-ES, HelenaRUS)";
     }
     if ($(this).val() == "th"){
       document.getElementById("select-voice").value = "(th-TH, Pattara)";
     }
     if ($(this).val() == "tr"){
       document.getElementById("select-voice").value = "(tr-TR, SedaRUS)";
     }
     if ($(this).val() == "vi"){
       document.getElementById("select-voice").value = "(vi-VN, An)";
     }
   });
   ```

### <a name="test-your-app"></a>アプリをテストする

アプリで音声合成をテストしてみましょう。

```
flask run
```

指定されたサーバー アドレスに移動します。 入力領域にテキストを入力し、言語を選択して、翻訳ボタンをクリックします。 これで翻訳が表示されます。 次に音声を選択して、テキストから音声への変換ボタンを押します。 翻訳が合成音声として再生されます。 正しく機能しない場合は、サブスクリプション キーを追加してあることを確認してください。

> [!TIP]
> 先ほど行った変更が反映されていない場合や、思いどおりにアプリが動作しない場合は、キャッシュをクリアするか、プライベート ウィンドウやシークレット ウィンドウを開いてみてください。

以上で終了です。翻訳、感情分析、合成音声を実行する実用的なアプリが完成しました。 **Ctrl + C** キーを押してアプリを強制終了してください。 他の [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) もチェックしてみましょう。

## <a name="get-the-source-code"></a>ソース コードを入手する

このプロジェクトのソース コードは、[GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial) で入手できます。

## <a name="next-steps"></a>次の手順

* [Translator Text API のリファレンス](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [Text Analytics API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Text-to-speech API リファレンス](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
