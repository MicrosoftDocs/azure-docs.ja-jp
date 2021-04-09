---
title: Computer Vision から Read OCR Docker コンテナーをインストールする
titleSuffix: Azure Cognitive Services
description: Computer Vision の Read OCR Docker コンテナーを使用して、イメージとドキュメントからオンプレミスでテキストを抽出します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: オンプレミス、OCR、Docker、コンテナー
ms.openlocfilehash: 1c9e681e3c02cb65b2a54070cc778051a0e7ac53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102432555"
---
# <a name="install-read-ocr-docker-containers-preview"></a>Read OCR Docker コンテナー (プレビュー) をインストールする 

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

コンテナーを使用すると、独自の環境で Computer Vision API を実行できます。 コンテナーは、特定のセキュリティ要件とデータ ガバナンス要件に適しています。 この記事では、Computer Vision コンテナーをダウンロード、インストール、実行する方法について説明します。

*Read* OCR コンテナーを使用すると、JPEG、PNG、BMP、PDF、TIFF の各ファイル形式をサポートするイメージとドキュメントから、印刷されたテキストおよび手書きのテキストを抽出できます。 詳細については、[Read API のドキュメント](concept-recognizing-text.md#read-api)に関する記事を参照してください。

## <a name="read-32-preview-container"></a>Read 3.2-preview コンテナー

> [!NOTE]
> Read 3.0-preview コンテナーは非推奨となりました。 

Read 3.2-preview OCR コンテナーは、次のものを備えています。
* 精度の向上のための新しいモデル。
* 同じドキュメント内での複数言語のサポート。
* 合計 73 言語のサポート。 [OCR でサポートされている言語](./language-support.md#optical-character-recognition-ocr)の完全な一覧を参照してください。
* ドキュメントとイメージの両方に対する 1 つの操作。
* 大きなドキュメントとイメージのサポート。
* 信頼度スコア。
* 印刷および手書きの両方のテキストを含むドキュメントのサポート。
* ドキュメント内の選択したページからのみテキストを抽出する機能。
* テキスト行の出力順序の既定からより自然な読み取り順序への選択 (ラテン語系の言語のみ)。
* 手書きスタイルとしての、またはラテン言語に対してのみでないテキスト行の分類。

現時点で Read 2.0 コンテナーを使用している場合は、 [移行ガイド](read-container-migration-guide.md)に関する記事を参照して、新しいバージョンの変更点を確認してください。

## <a name="prerequisites"></a>前提条件

コンテナーを使用する前に、次の前提条件を満たす必要があります。

|必須|目的|
|--|--|
|Docker エンジン| [ホスト コンピューター](#the-host-computer)に Docker エンジンをインストールしておく必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上で Docker 環境の構成を行うパッケージが用意されています。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。<br><br> コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 <br><br> **Windows では**、Linux コンテナーをサポートするように Docker を構成することも必要です。<br><br>|
|Docker に関する知識 | レジストリ、リポジトリ、コンテナー、コンテナー イメージなど、Docker の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。| 
|Computer Vision リソース |コンテナーを使用するためには、以下が必要です。<br><br>Azure **Computer Vision** リソースとその関連する API キーおよびエンドポイント URI。 どちらの値も、対象リソースの概要ページとキー ページで使用でき、コンテナーを開始するために必要です。<br><br>**{API_KEY}** : **[キー]** ページにある 2 つの利用可能なリソース キーのどちらか<br><br>**{ENDPOINT_URI}** : **[概要]** ページに提示されているエンドポイント|

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

## <a name="request-approval-to-run-the-container"></a>コンテナーを実行するための承認を要求する

コンテナーを実行するための承認を要求するには、[要求フォーム](https://aka.ms/csgate)に記入して送信します。 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>ホスト コンピューター

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Advanced Vector Extension のサポート

**ホスト** コンピューターとは、Docker コンテナーを実行するコンピューターのことです。 ホストは、[高度なベクター拡張機能](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2) を *サポートしている必要があります*。 次のコマンドを使用して、Linux ホストでの AVX2 サポートを確認できます。

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> AVX2 をサポートするにはホスト コンピューターが *必須* です。 AVX2 サポートがないと、コンテナーは正しく機能 *しません*。

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

読み取りのコンテナー イメージを入手できます。

| コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|-----------|------------|
| Read 2.0-preview | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| Read 3.2-プレビュー | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して、コンテナー イメージをダウンロードします。

### <a name="docker-pull-for-the-read-container"></a>読み取りコンテナー用の Docker pull

# <a name="version-32-preview"></a>[Version 3.2-preview](#tab/version-3-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2
```

# <a name="version-20-preview"></a>[Version 2.0-preview](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>コンテナーを使用する方法

コンテナーを[ホスト コンピューター](#the-host-computer)上に用意できたら、次の手順を使用してコンテナーを操作します。

1. 必要な課金設定を使用して[コンテナーを実行](#run-the-container-with-docker-run)します。 `docker run` コマンドの他の[例](computer-vision-resource-container-config.md)もご覧いただけます。 
1. [コンテナーの予測エンドポイントに対するクエリを実行します](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

コンテナーを実行するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 `{ENDPOINT_URI}` と `{API_KEY}` の値を取得する方法の詳細については、「[必須パラメーターの収集](#gathering-required-parameters)」を参照してください。

`docker run` コマンドの[例](computer-vision-resource-container-config.md#example-docker-run-commands)を利用できます。

# <a name="version-32-preview"></a>[Version 3.2-preview](#tab/version-3-2)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから読み取りコンテナーを実行します。
* 8 つの CPU コアと 18 ギガバイト (GB) のメモリを割り当てます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます。
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。

# <a name="version-20-preview"></a>[Version 2.0-preview](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから読み取りコンテナーを実行します。
* 8 つの CPU コアと 16 ギガバイト (GB) のメモリを割り当てます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます。
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。

---


`docker run` コマンドの他の[例](./computer-vision-resource-container-config.md#example-docker-run-commands)もご覧いただけます。 

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。

より高いスループットが必要な場合 (複数ページのファイルを処理する場合など)、[Azure Storage](../../storage/common/storage-account-create.md) と [Azure Queue](../../storage/queues/storage-queues-introduction.md) を使用して、[Kubernetes クラスターに](deploy-computer-vision-on-premises.md)複数のコンテナーをデプロイすることを検討してください。

処理用のイメージを格納するために Azure Storage を使用している場合は、コンテナーを呼び出すときに使用する[接続文字列](../../storage/common/storage-configure-connection-string.md)を作成できます。

接続文字列を見つけるには

1. Azure portal で **ストレージ アカウント** に移動し、自分のアカウントを見つけます。
2. 左側のナビゲーション リストで **[アクセス キー]** をクリックします。
3. 接続文字列は、 **[接続文字列]** の下に配置されます。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>コンテナーの予測エンドポイントに対するクエリの実行

コンテナーには、REST ベースのクエリ予測エンドポイント API が用意されています。 

# <a name="version-32-preview"></a>[Version 3.2-preview](#tab/version-3-2)

コンテナー API には、ホストの `http://localhost:5000` を使用します。 Swagger パスは `http://localhost:5000/swagger/vision-v3.2-preview-read/swagger.json` で確認できます。

# <a name="version-20-preview"></a>[Version 2.0-preview](#tab/version-2)

コンテナー API には、ホストの `http://localhost:5000` を使用します。 Swagger パスは `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` で確認できます。

---

### <a name="asynchronous-read"></a>非同期読み取り


# <a name="version-32-preview"></a>[Version 3.2-preview](#tab/version-3-2)

Computer Vision サービスで該当する REST 操作を使用する方法と同じように、`POST /vision/v3.2/read/analyze` 操作と `GET /vision/v3.2/read/operations/{operationId}` 操作を同時に使用して、画像を非同期に読み取ることができます。 非同期 POST メソッドでは、HTTP GET 要求に対する識別子として使用される `operationId` が返されます。


Swagger UI で `Analyze` を選択し、ブラウザーで展開します。 次に、 **[Try it out]\(試してみる\)**  >  **[Choose file]\(ファイルの選択\)** を選択します。 この例では、次の画像を使用します。

![タブとスペース](media/tabs-vs-spaces.png)

非同期 POST が正常に実行されると、**HTTP 202** 状態コードが返されます。 応答の一部として、要求の結果エンドポイントを保持する `operation-location` ヘッダーがあります。

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.2/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location` は完全修飾 URL であり、HTTP GET を介してアクセスされます。 次に示すのは、前の画像から `operation-location` URL を実行すると返される JSON 応答です。

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          },
          {
            "boundingBox": [
              286,
              171,
              415,
              165,
              417,
              197,
              287,
              201
            ],
            "text": "paces",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.746
              }
            },
            "words": [
              {
                "boundingBox": [
                  286,
                  179,
                  404,
                  166,
                  405,
                  198,
                  290,
                  201
                ],
                "text": "paces",
                "confidence": 0.938
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[Version 2.0-preview](#tab/version-2)

Computer Vision サービスで該当する REST 操作を使用する方法と同じように、`POST /vision/v2.0/read/core/asyncBatchAnalyze` 操作と `GET /vision/v2.0/read/operations/{operationId}` 操作を同時に使用して、画像を非同期に読み取ることができます。 非同期 POST メソッドでは、HTTP GET 要求に対する識別子として使用される `operationId` が返されます。

Swagger UI で `asyncBatchAnalyze` を選択し、ブラウザーで展開します。 次に、 **[Try it out]\(試してみる\)**  >  **[Choose file]\(ファイルの選択\)** を選択します。 この例では、次の画像を使用します。

![タブとスペース](media/tabs-vs-spaces.png)

非同期 POST が正常に実行されると、**HTTP 202** 状態コードが返されます。 応答の一部として、要求の結果エンドポイントを保持する `operation-location` ヘッダーがあります。

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location` は完全修飾 URL であり、HTTP GET を介してアクセスされます。 次に示すのは、前の画像から `operation-location` URL を実行すると返される JSON 応答です。

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

---

> [!IMPORTANT]
> ロード バランサーの背後に複数の読み取りコンテナーをデプロイする場合は (たとえば、Docker Compose または Kubernetes の下)、外部キャッシュが必要です。 処理コンテナーと GET 要求コンテナーは同じではない可能性があるため、外部キャッシュによって結果が格納され、コンテナーとの間で共有されます。 キャッシュ設定の詳細については、「[Computer Vision Docker コンテナーを構成する](./computer-vision-resource-container-config.md)」を参照してください。

### <a name="synchronous-read"></a>同期読み取り

次の操作を使用して、画像を同期的に読み取ることができます。 

# <a name="version-32-preview"></a>[Version 3.2-preview](#tab/version-3-2)

`POST /vision/v3.2/read/syncAnalyze` 

# <a name="version-20-preview"></a>[Version 2.0-preview](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

画像全体が読み込まれたら、そのときにだけ、API から JSON 応答が返されます。 これに対する唯一の例外は、エラーが発生した場合です。 エラーが発生すると、次の JSON が返されます。

```json
{
    "status": "Failed"
}
```

JSON 応答オブジェクトには、非同期バージョンと同じオブジェクト グラフが含まれます。 JavaScript を使用していて、タイプ セーフが必要な場合は、TypeScript を使用して、JSON 応答をキャストすることを検討してください。

ユースケースの例については、<a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">こちらの TypeScript サンドボックス</a>を参照し、 **[Run]\(実行\)** を選択してその使いやすさを確認してください。

## <a name="stop-the-container"></a>コンテナーの停止

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>トラブルシューティング

出力[マウント](./computer-vision-resource-container-config.md#mount-settings)とログを有効にした状態でコンテナーを実行すると、コンテナーによってログ ファイルが生成されます。これらはコンテナーの起動時または実行時に発生した問題のトラブルシューティングに役立ちます。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>課金

Cognitive Services コンテナーでは、Azure アカウントの対応するリソースを使用して、Azure に課金情報が送信されます。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

これらのオプションの詳細については、「[コンテナーの構成](./computer-vision-resource-container-config.md)」を参照してください。 

## <a name="summary"></a>まとめ

この記事では、Computer Vision コンテナーの概念とそのダウンロード、インストール、実行のワークフローについて説明しました。 要約すると:

* Computer Vision では、読み取りがカプセル化された、Docker 用の Linux コンテナーが提供されます。
* コンテナー イメージは、Azure の "コンテナー プレビュー" コンテナー レジストリからダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または SDK を使用して、コンテナーのホスト URI を指定することによって、Read コンテナーの操作を呼び出すことができます。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーが、顧客データ (解析対象の画像やテキストなど) を Microsoft に送信することはありません。

## <a name="next-steps"></a>次のステップ

* 構成設定について、[コンテナーの構成](computer-vision-resource-container-config.md)を確認する
* [Computer Vision の概要](overview.md)ページを読み、印刷されたテキストと手書きのテキストの認識の詳細について確認する
* コンテナーによりサポートされるメソッドの詳細を [Computer Vision API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) ページで確認する。
* [よく寄せられる質問 (FAQ)](FAQ.md) を参照して、Computer Vision 機能に関連する問題を解決する。
* さらに [Cognitive Services コンテナー](../cognitive-services-container-support.md)を使用する
