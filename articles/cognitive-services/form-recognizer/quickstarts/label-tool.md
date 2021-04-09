---
title: 'クイック スタート: サンプル ラベル付けツールを使用したフォームのラベル付け、モデルのトレーニング、フォームの分析 - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Form Recognizer のサンプル ラベル付けツールを使用して、フォーム ドキュメントに手動でラベルを付けます。 次に、ラベル付けされたドキュメントを使用してカスタム ドキュメント処理モデルをトレーニングし、そのモデルを使用してキーと値のペアを抽出します。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: ドキュメントの処理
ms.openlocfilehash: 89de0752b3015fb8132bfa50c7dbdce174061bcc
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467295"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>サンプル ラベル付けツールを使用したラベルによる Form Recognizer モデルのトレーニング

このクイックスタートでは、Form Recognizer REST API とサンプル ラベル付けツールを使用して、手動でラベル付けされたデータを使ってカスタム ドキュメント処理モデルをトレーニングします。 Form Recognizer を使用した教師あり学習の詳細については、概要に関するページの「[ラベルを使用したトレーニング](../overview.md#train-with-labels)」を参照してください。

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* Azure サブスクリプションを用意できたら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer リソースを作成"  target="_blank">Form Recognizer リソースを作成</a>し、自分のキーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** を選択します。
  * 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
  * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* 同じ種類の少なくとも 6 つのフォームのセット。 このデータを使用して、モデルのトレーニングとフォームのテストを行います。 このクイックスタートでは、[サンプル データセット](https://go.microsoft.com/fwlink/?linkid=2090451)を使用できます (*sample_data.zip* をダウンロードして展開します)。 Standard パフォーマンス レベルの Azure Storage アカウントの BLOB ストレージ コンテナーのルートに、トレーニング ファイルをアップロードします。

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer リソースを作成する

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="try-it-out"></a>試してみる

Form Recognizer サンプル ラベル付けツールをオンラインで試すには、[FOTT の Web サイト](https://fott-preview.azurewebsites.net/)にアクセスします。

### <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1)

> [!div class="nextstepaction"]
> [事前構築済みモデルを試す](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [事前構築済みモデルを試す](https://fott.azurewebsites.net/)

---

Form Recognizer サービスを試すには、Azure サブスクリプション ([無料で作成](https://azure.microsoft.com/free/cognitive-services)) と [Form Recognizer リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) エンドポイントおよびキーが必要です。

## <a name="set-up-the-sample-labeling-tool"></a>サンプル ラベル付けツールを設定する

サンプル ラベル付けツールを実行するには、Docker エンジンを使用します。 次の手順に従って、Docker コンテナーを設定します。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。

> [!TIP]
> GitHub のオープンソース プロジェクトの OCR Form Labeling Tool を使用することもできます。 このツールは、React + Redux を使用して作成された TypeScript Web アプリケーションです。 詳細または共同作成については、[OCR Form Labeling Tool](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application) リポジトリを参照してください。 ツールをオンラインで試すには、[FOTT Web サイト](https://fott.azurewebsites.net/)にアクセスします。

1. まず、ホスト コンピューターに Docker をインストールします。 このガイドでは、ローカル コンピューターをホストとして使用する方法について説明します。 Azure で Docker ホスティング サービスを使用する場合は、「[サンプルのラベル付けツールのデプロイ](../deploy-label-tool.md)」攻略ガイドを参照してください。

   ホスト コンピューターは、次のハードウェア要件を満たしている必要があります。

    | コンテナー | 最小値 | 推奨|
    |:--|:--|:--|
    |サンプル ラベル付けツール|2 コア、4 GB メモリ|4 コア、8 GB メモリ|

   お使いのオペレーティング システムに該当する手順に従って、マシンに Docker をインストールします。

   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)

1. `docker pull` コマンドを使用して、サンプル ラベル付けツールのコンテナーを取得します。

### <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1)

```console
 docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```console
docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
```

---
</br>
  3. これで、`docker run` を使用してコンテナーを実行する準備が整いました。

### <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1)

```console
 docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview eula=accept
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```console
docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
```

---

   このコマンドを実行すると、Web ブラウザーからサンプル ラベル付けツールを使用できるようになります。 `http://localhost:3000` にアクセスします。

> [!NOTE]
> Form Recognizer REST API を使用して、ドキュメントにラベルを付けたり、モデルをトレーニングしたりすることもできます。 REST API を使用してトレーニングおよび分析を行うには、[REST API と Python によるラベルを使用したトレーニング](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)に関するページを参照してください。

## <a name="set-up-input-data"></a>入力データを設定する

まず、すべてのトレーニング ドキュメントが同じ形式であることを確認します。 複数の形式のフォームがある場合は、共通する形式に基づいてサブフォルダーに分類します。 トレーニング時には、API に対してサブフォルダーを指定する必要があります。

### <a name="configure-cross-domain-resource-sharing-cors"></a>クロスドメイン リソース共有 (CORS) を構成する

ストレージ アカウントで CORS を有効にします。 Azure portal で自分のストレージ アカウントを選択し、左側のペインで **[CORS]** タブを選択します。 一番下の行に、次の値を入力します。 上部にある **[保存]** を選択します。

* [許可されたドメイン] = *
* [許可されたメソッド] = \[すべて選択\]
* [許可されたヘッダー] = *
* [公開されるヘッダー] = *
* [最長有効期間] = 200

> [!div class="mx-imgBorder"]
> ![Azure portal での CORS の設定](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>サンプル ラベル付けツールに接続する

 サンプル ラベル付けツールは、ソース (元のアップロードされたフォーム) とターゲット (作成されたラベルと出力データ) に接続されます。

接続は、複数のプロジェクトにまたがって設定および共有できます。 拡張可能なプロバイダー モデルが使用されるため、新しいソースまたはターゲット プロバイダーを簡単に追加できます。

新しい接続を作成するには、左側のナビゲーション バーにある **新しい接続** (プラグ) アイコンを選択します。

フィールドに次の値を入力します。

* **[表示名]** - 接続の表示名。
* **[説明]** - プロジェクトの説明。
* **[SAS URL]** - Azure Blob Storage コンテナーの Shared Access Signature (SAS) URL。 [!INCLUDE [get SAS URL](../includes/sas-instructions.md)]

   :::image type="content" source="../media/quickstarts/get-sas-url.png" alt-text="SAS URL の取得":::

:::image type="content" source="../media/label-tool/connections.png" alt-text="サンプル ラベル付けツールの接続設定。":::

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

サンプル ラベル付けツールでは、プロジェクトに構成と設定が保存されます。 新しいプロジェクトを作成し、フィールドに次の値を入力します。

* **[表示名]** - プロジェクトの表示名
* **[セキュリティ トークン]** - 一部のプロジェクト設定には、API キーや他の共有シークレットなどの機密性の高い値を含めることができます。 各プロジェクトでは、機密性の高いプロジェクト設定の暗号化または暗号化解除に使用できるセキュリティ トークンが生成されます。 セキュリティ トークンは、左側のナビゲーション バーの下部にある歯車アイコンを選択すると、[アプリケーション設定] に表示されます。
* **[基になる接続]** - このプロジェクトに使用する、前の手順で作成した Azure Blob Storage 接続。
* **[フォルダー パス]** (省略可能) - ソース フォームが BLOB コンテナー上のフォルダーに配置されている場合は、ここにフォルダー名を指定します。
* **[Form Recognizer Service Uri]\(Form Recognizer サービスの URI\)** - Form Recognizer のエンドポイント URL。
* **[API キー]** - Form Recognizer のサブスクリプション キー。
* **[説明]** (省略可能) - プロジェクトの説明

:::image type="content" source="../media/label-tool/new-project.png" alt-text="サンプル ラベル付けツールの新規プロジェクト ページ。":::

## <a name="label-your-forms"></a>フォームにラベルを付ける

プロジェクトを作成するか開くと、メインのタグ エディター ウィンドウが開きます。 このタグ エディターは、次の 3 つの部分で構成されます。

* サイズ変更可能なプレビュー ペイン。基になる接続にあるフォームのスクロール可能な一覧が表示されます。
* メインのエディター ペイン。ここで、タグを適用できます。
* タグ エディター ペイン。ここで、タグの変更、ロック、並べ替え、削除を行うことができます。

### <a name="identify-text-and-tables"></a>テキストとテーブルを特定する 

左側のペインにある **[Run OCR on all files]\(すべてのファイルに対して OCR を実行\)** を選択して、各ドキュメントのテキストとテーブルのレイアウト情報を取得します。 ラベル付けツールによって、各テキスト要素の周囲に境界ボックスが描画されます。

ラベル付けツールでは、どのテーブルが自動的に抽出されたかも示されます。 ドキュメントの左側にあるテーブル (グリッド) アイコンを選択すると、抽出されたテーブルが表示されます。 テーブルの内容は自動的に抽出されるため、このクイックスタートではテーブルの内容に対するラベル付けは行わず、自動化された抽出を信頼することにします。

:::image type="content" source="../media/label-tool/table-extraction.png" alt-text="サンプル ラベル付けツールでのテーブルの視覚化。":::

v2.1 では、トレーニング ドキュメントに値が入力されていない場合は、値が入る枠を描画できます。 ウィンドウの左上隅にある **描画領域** を使用して、領域をタグ付け可能にします。

### <a name="apply-labels-to-text"></a>ラベルをテキストに適用する

次に、タグ (ラベル) を作成し、モデルに分析させるテキスト要素に適用します。

### <a name="v20"></a>[v2.0](#tab/v2-1)  

1. まず、タグ エディター ペインを使用して、識別するタグを作成します。
   1. **+** を選択して、新しいタグを作成します。
   1. タグ名を入力します。
   1. Enter キーを押して、タグを保存します。
1. メインのエディターで、強調表示されたテキスト要素または描画した領域から単語を選択します。
1. 適用するタグを選択するか、対応するキーボード キーを押します。 数字キーは、最初の 10 個のタグのホットキーとして割り当てられます。 タグの順序は、タグ エディター ペインの上矢印と下矢印のアイコンを使用して変更できます。
    > [!Tip]
    > フォームにラベルを付けるときは、次のヒントに留意してください。
    >
    > * 適用できるタグは、選択したテキスト要素ごとに 1 つのみです。
    > * 各タグは、1 ページにつき 1 回のみ適用できます。 同じフォームに同じ値が複数回出現する場合は、インスタンスごとに異なるタグを作成します。 たとえば、"invoice # 1"、"invoice # 2" などとします。
    > * タグは複数のページにまたがることはできません。
    > * フォームに表示されるラベル値は、2 つの異なるタグを使用して 2 つの部分に分割しないでください。 たとえば、アドレス フィールドが複数の行にまたがる場合でも、1 つのタグを使用してラベルを付ける必要があります。
    > * タグが付けられたフィールドには値のみを含めます。キーは含めないでください。
    > * テーブル データは自動的に検出され、最終的な出力 JSON ファイルに表示されます。 ただし、モデルが一部のテーブル データを検出できない場合は、これらのフィールドに手動でタグを付けることもできます。 テーブル内のセルごとに異なるラベルを使用してタグ付けします。 さまざまな行数を含むテーブルがフォームにある場合は、できるだけ大きなテーブルを含む 1 つ以上のフォームにタグを付けてください。
    > * **+** の右にあるボタンを使用して、タグの検索、名前の変更、順序変更、削除を行います。
    > * タグそのものは削除せずに、適用されているタグを解除するには、タグ付けされた四角形をドキュメント ビューで選択し、Delete キーを押します。
    >

### <a name="v20"></a>[v2.0](#tab/v2-0)

1. まず、タグ エディター ペインを使用して、識別するタグを作成します。
   1. **+** を選択して、新しいタグを作成します。
   1. タグ名を入力します。
   1. Enter キーを押して、タグを保存します。
1. メインのエディターで、強調表示されたテキスト要素から単語を選択します。
1. 適用するタグを選択するか、対応するキーボード キーを押します。 数字キーは、最初の 10 個のタグのホットキーとして割り当てられます。 タグの順序は、タグ エディター ペインの上矢印と下矢印のアイコンを使用して変更できます。
    > [!Tip]
    > フォームにラベルを付けるときは、次のヒントに留意してください。
    >
    > * 適用できるタグは、選択したテキスト要素ごとに 1 つのみです。
    > * 各タグは、1 ページにつき 1 回のみ適用できます。 同じフォームに同じ値が複数回出現する場合は、インスタンスごとに異なるタグを作成します。 たとえば、"invoice # 1"、"invoice # 2" などとします。
    > * タグは複数のページにまたがることはできません。
    > * フォームに表示されるラベル値は、2 つの異なるタグを使用して 2 つの部分に分割しないでください。 たとえば、アドレス フィールドが複数の行にまたがる場合でも、1 つのタグを使用してラベルを付ける必要があります。
    > * タグが付けられたフィールドには値のみを含めます。キーは含めないでください。
    > * テーブル データは自動的に検出され、最終的な出力 JSON ファイルに表示されます。 ただし、モデルが一部のテーブル データを検出できない場合は、これらのフィールドに手動でタグを付けることもできます。 テーブル内のセルごとに異なるラベルを使用してタグ付けします。 さまざまな行数を含むテーブルがフォームにある場合は、できるだけ大きなテーブルを含む 1 つ以上のフォームにタグを付けてください。
    > * **+** の右にあるボタンを使用して、タグの検索、名前の変更、順序変更、削除を行います。
    > * タグそのものは削除せずに、適用されているタグを解除するには、タグ付けされた四角形をドキュメント ビューで選択し、Delete キーを押します。
>

---
---

:::image type="content" source="../media/label-tool/main-editor-2-1.png" alt-text="サンプル ラベル付けツールのメインのエディター ウィンドウ。":::

上記の手順に従って、少なくとも 5 つのフォームにラベルを付けてください。

### <a name="specify-tag-value-types"></a>タグの値の型を指定する

それぞれのタグに予期されるデータ型を設定できます。 タグの右側にあるコンテキスト メニューを開いて型を選択します。 この機能により、検出アルゴリズムは、テキスト検出精度の向上につながる仮説を立てることができます。 また、最終的な JSON 出力では、検出された値が確実に標準化された形式で返されます。 値の型に関する情報は、ラベル ファイルと同じパスにある **fields.json** ファイルに保存されます。

> [!div class="mx-imgBorder"]
> ![サンプル ラベル付けツールでの値の型の選択](../media/whats-new/value-type.png)

現在、次の値の型とバリエーションがサポートされています。

* `string`
  * 既定値、`no-whitespaces`、`alphanumeric`

* `number`
  * 既定値、`currency`

* `date`
  * 既定値、`dmy`、`mdy`、`ymd`

* `time`
* `integer`
* `selectionMark` – "_v2.1-preview.1 の新機能_"

> [!NOTE]
> 日付の書式設定については、次の規則を参照してください。
>
> 日付書式設定を機能させるには、形式 (`dmy`、`mdy`、`ymd`) を指定する必要があります。
>
> `, - / . \` は日付区切り記号として使用できます。 空白を区切り記号として使用することはできません。 次に例を示します。
>
> * 01,01,2020
> * 01-01-2020
> * 01/01/2020
>
> 日付と月はそれぞれ 1 桁または 2 桁の数字として、年は 2 桁または 4 桁の数字として記述することができます。
>
> * 1-1-2020
> * 1-01-20
>
> 日付文字列が 8 桁の場合、区切り文字は任意です。
>
> * 01012020
> * 01 01 2020
>
> 月は、完全な名前または短い名前で記述することもできます。 名前が使用されている場合、区切り文字は省略可能です。 ただし、この形式は他の形式より正確ではないと認識されることがあります。
>
> * 01/Jan/2020
> * 01Jan2020
> * 01 Jan 2020

### <a name="label-tables-v21-only"></a>テーブルにラベルを付ける (v2.1 のみ)

場合によって、データは、キーと値のペアではなくテーブルとしてラベル付けした方が役立つことがあります。 この場合は、[Add a new table tag]\(新しいテーブル タグの追加\) をクリックしてテーブル タグを作成し、ドキュメントに応じてテーブルで固定行数と可変行数のどちらを使用するかを指定し、スキーマを定義できます。

:::image type="content" source="../media/label-tool/table-tag.png" alt-text="テーブル タグの構成。":::

テーブル タグを定義したら、セルの値にタグを付けます。

:::image type="content" source="../media/table-labeling.png" alt-text="テーブルのラベル付け。":::

## <a name="train-a-custom-model"></a>カスタム モデルをトレーニングする

左側のペインでトレーニング アイコンを選択して、[トレーニング] ページを開きます。 次に、 **[Train]\(トレーニング\)** ボタンを選択して、モデルのトレーニングを開始します。 トレーニング プロセスが完了すると、次の情報が表示されます。

* **[Model ID]** - 作成およびトレーニングされたモデルの ID。 トレーニングの呼び出しごとに、独自の ID を持つ新しいモデルが作成されます。 この文字列を安全な場所にコピーしてください。[REST API](./client-library.md?pivots=programming-language-rest-api) または[クライアント ライブラリ](./client-library.md)を使用して予測呼び出しを行う場合に必要になります。
* **[Average Accuracy]\(平均精度\)** - モデルの平均精度。 追加のフォームにラベルを付け、再度トレーニングを行って新しいモデルを作成することにより、モデルの精度を向上させることができます。 最初は 5 つのフォームにラベルを付け、必要に応じてフォームの数を増やすことをお勧めします。
* タグの一覧と、タグごとの予測精度。


:::image type="content" source="../media/label-tool/train-screen.png" alt-text="トレーニング ビュー。":::

トレーニングが完了したら、 **[Average Accuracy]\(平均精度\)** の値を確認します。 低い場合は、入力ドキュメントをさらに追加して、上記の手順を繰り返す必要があります。 既にラベルを付けたドキュメントは、プロジェクトのインデックスに残ります。

> [!TIP]
> REST API 呼び出しを使用してトレーニング プロセスを実行することもできます。 これを行う方法については、[Python によるラベルを使用したトレーニング](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)に関するページを参照してください。

## <a name="compose-trained-models"></a>トレーニング済みのモデルを作成する

### <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1)

[Model Compose]\(モデルの作成\) では、1 つのモデル ID に最大 100 個のモデルを作成できます。 作成された `modelID` で Analyze を呼び出すと、まず送信されたフォームが分類され、最も適合するモデルが選択され、そのモデルに対する結果が返されます。 この操作は、受信フォームが複数のテンプレートのいずれかに属している場合に役立ちます。

サンプル ラベル付けツールでモデルを作成するには、左側にある [Model Compose]\(モデルの作成\) (結合する矢印) アイコンを選択します。 左側で、一緒に作成したいモデルを選択します。 矢印アイコンの付いたモデルは、既に作成済みのモデルです。
**[作成] ボタン** を選択します。 ポップアップで、新しく作成するモデルに名前を付け、 **[作成]** をクリックします。 操作が完了すると、新しく作成されたモデルが一覧に表示されます。

:::image type="content" source="../media/label-tool/model-compose.png" alt-text="モデルの作成の UX ビュー。":::

### <a name="v20"></a>[v2.0](#tab/v2-0)

現在、この機能は v2.1.preview でご利用いただけます。 プレビュー。

---

## <a name="analyze-a-form"></a>フォームを分析する

左側の予測 (電球) アイコンを選択して、モデルをテストします。 トレーニング プロセスで使用しなかったフォーム ドキュメントをアップロードします。 次に、右側にある **[予測]** ボタンを選択して、フォームのキーと値の予測を取得します。 このツールでは、境界ボックスにタグが適用され、各タグの信頼度がレポートされます。

> [!TIP]
> REST 呼び出しを使用して Analyze API を実行することもできます。 これを行う方法については、[Python によるラベルを使用したトレーニング](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)に関するページを参照してください。

## <a name="improve-results"></a>結果を改善する

レポートされる精度によっては、モデルを改善するために、さらにトレーニングを行う必要が生じる場合があります。 予測が完了したら、適用されている各タグの信頼度の値を確認します。 トレーニングの平均精度値が高くても信頼度スコアが低い (または結果が不正確である) 場合は、予測ファイルをトレーニング セットに追加してラベルを付け、再度トレーニングを行う必要があります。

分析されたドキュメントがトレーニングで使用されたドキュメントと異なる場合は、レポートされる平均精度、信頼度スコア、および実際の精度に一貫性がなくなる場合があります。 ドキュメントには、人間が見た場合に似ていても AI モデルにとっては異なって見えるものがあることに留意してください。 たとえば、2 つのバリエーションがあるフォームの種類を使ってトレーニングするとします。ここでは、トレーニング セットはバリエーション A 20%、バリエーション B 80% で構成されています。この場合、予測の際にバリエーション A のドキュメントの信頼度スコアが低くなる可能性があります。

## <a name="save-a-project-and-resume-later"></a>プロジェクトを保存して後で再開する

プロジェクトを時を改めて再開したり、別のブラウザーで再開したりするには、プロジェクトのセキュリティ トークンを保存し、後で再入力する必要があります。

### <a name="get-project-credentials"></a>プロジェクトの資格情報を取得する

プロジェクトの設定ページ (スライダー アイコン) に移動し、セキュリティ トークンの名前を書き留めます。 次に、アプリケーション設定 (歯車アイコン) に移動します。ここには、現在のブラウザー インスタンスのセキュリティ トークンがすべて表示されます。 プロジェクトのセキュリティ トークンを検索し、その名前とキー値を安全な場所にコピーします。

### <a name="restore-project-credentials"></a>プロジェクトの資格情報を復元する

プロジェクトを再開する場合は、まず、同じ Blob Storage コンテナーへの接続を作成する必要があります。 これを行うには、前述の手順を繰り返します。 次に、アプリケーション設定ページ (歯車アイコン) に移動し、プロジェクトのセキュリティ トークンがそこにあるかどうかを確認します。 ない場合は、新しいセキュリティ トークンを追加し、前の手順で保存したトークン名とキーをコピーします。 **[保存]** を選択して設定を保持します。

### <a name="resume-a-project"></a>プロジェクトを再開する

最後に、メイン ページ (家のアイコン) に移動し、 **[Open Cloud Project]\(クラウド プロジェクトを開く\)** を選択します。 その後、Blob Storage 接続を選択し、プロジェクトの **.fott** ファイルを選択します。 これにはセキュリティ トークンが含まれているため、アプリケーションによってプロジェクトの設定がすべて読み込まれます。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Form Recognizer のサンプル ラベル付けツールを使用し、手動でラベル付けされたデータを使ってモデルをトレーニングする方法について説明しました。 トレーニング データにラベルを付ける独自のユーティリティを作成したい場合は、ラベル付けされたデータのトレーニングを扱う REST API を使用してください。

> [!div class="nextstepaction"]
> [Python でラベルを使用してトレーニングする](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)

* [Form Recognizer とは](../overview.md)
* [Form Recognizer のクイックスタート](client-library.md)
