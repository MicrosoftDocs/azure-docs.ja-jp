---
title: Face Python クライアント ライブラリのクイックスタート
description: Python 用 Face クライアント ライブラリを使用して、顔の検出、類似検索 (画像による顔検索)、顔の識別 (顔認識検索) を行います。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/10/2020
ms.author: pafarley
ms.openlocfilehash: 7fc1822c219c6c881e3d788f6b0e56675cecc466
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102445123"
---
Python 用 Face クライアント ライブラリを使用して顔認識を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 Face サービスは、画像内の人間の顔を検出および認識するための高度なアルゴリズムへのアクセスを提供します。

Python 用 Face クライアント ライブラリを使用すると、次のことができます。

* [画像内の顔を検出する](#detect-faces-in-an-image)
* [似た顔を探す](#find-similar-faces)
* [人物グループを作成してトレーニングする](#create-and-train-a-person-group)
* [顔を識別する](#identify-a-face)
* [顔を確認する](#verify-faces)

[リファレンス ドキュメント](/python/api/azure-cognitiveservices-vision-face/) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [パッケージ (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [サンプル](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * Python のインストールには、[pip](https://pip.pypa.io/en/stable/) が含まれている必要があります。 pip がインストールされているかどうかを確認するには、コマンド ラインで `pip --version` を実行します。 最新バージョンの Python をインストールして pip を入手してください。
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Face リソースを作成"  target="_blank">Face リソースを作成</a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを Face API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定
 
### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

Python をインストールしたら、次を使用してクライアント ライブラリをインストールすることができます。

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

新しい Python スクリプト (たとえば *quickstart-file.py*) を作成します。 次に、それを任意のエディターまたは IDE で開き、以下のライブラリをインポートします。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) にあり、このクイックスタートのコード例が含まれています。

次に、リソースの Azure エンドポイントおよびキー用の変数を作成します。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!IMPORTANT]
> Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Face リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 キーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 たとえば、[Azure Key Vault](../../../../key-vault/general/overview.md) が考えられます。

## <a name="object-model"></a>オブジェクト モデル

以下のクラスとインターフェイスにより、Face Python クライアント ライブラリの主要な機能の一部が処理されます。

|名前|説明|
|---|---|
|[FaceClient](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient) | このクラスは、Face サービスを使用するための承認を表し、すべての Face 機能に必要です。 サブスクリプション情報を使用してこれをインスタンス化し、他のクラスのインスタンスを生成するために使用します。 |
|[FaceOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations)|このクラスは、人間の顔に対して実行できる基本的な検出と認識のタスクを処理します。 |
|[DetectedFace](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface)|このクラスは、画像内の 1 つの顔から検出されたすべてのデータを表します。 これを使用して、顔に関する詳細情報を取得できます。|
|[FaceListOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations)|このクラスは、クラウドに格納された **FaceList** コンストラクトを管理します。これには、さまざまな顔のセットが格納されます。 |
|[PersonGroupPersonOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations)| このクラスは、クラウドに格納された **Person** コンストラクトを管理します。これには、1 人の人物に属している顔のセットが格納されます。|
|[PersonGroupOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations)| このクラスは、クラウドに格納された **PersonGroup** コンストラクトを管理します。これには、さまざまな **Person** オブジェクトのセットが格納されます。 |
|[ShapshotOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations)|このクラスは、スナップショット機能を管理します。これを使用すると、クラウドベースのすべての顔データを一時的に保存し、そのデータを新しい Azure サブスクリプションに移行することができます。 |

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Python 用 Face クライアント ライブラリを使用して以下のタスクを実行する方法が示されています。

* [クライアントを認証する](#authenticate-the-client)
* [画像内の顔を検出する](#detect-faces-in-an-image)
* [似た顔を探す](#find-similar-faces)
* [人物グループを作成してトレーニングする](#create-and-train-a-person-group)
* [顔を識別する](#identify-a-face)
* [顔を確認する](#verify-faces)

## <a name="authenticate-the-client"></a>クライアントを認証する

ご利用のエンドポイントとキーを使用してクライアントをインスタンス化します。 キーを使用して [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) オブジェクトを作成し、それをエンドポイントと共に使用して、[FaceClient](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient) オブジェクトを作成します。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>画像内の顔を検出する

次のコードは、リモート画像内の顔を検出します。 検出された顔の ID をコンソールに出力し、プログラムのメモリにも格納します。 次に、複数の人物が含まれている画像内の顔を検出し、それらの ID もコンソールに出力します。 [detect_with_url](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) メソッドのパラメーターを変更することで、[DetectedFace](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface) オブジェクトごとに異なる情報を返すことができます。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

> [!TIP]
> また、ローカルの画像から顔を検出することもできます。 [FaceOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations) のメソッドを参照してください (**detect_with_stream** など)。

### <a name="display-and-frame-faces"></a>顔を表示してフレームに収める

次のコードは、指定された画像をディスプレイに出力し、DetectedFace.faceRectangle プロパティを使用して顔の周りに四角形を描画します。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![顔の周囲に赤い四角形が描画されている若い女性](../../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>似た顔の検索

以下のコードでは、検出された顔 (ソース) を 1 つ受け取って、他の顔のセット (ターゲット) から一致するものを見つけます (画像による顔検索)。 一致するものが見つかると、一致した顔の ID がコンソールに出力されます。

### <a name="find-matches"></a>一致するものを探す

最初に、上のセクション (「[画像内の顔を検出する](#detect-faces-in-an-image)」) のコードを実行して、1 つの顔への参照を保存します。 次に、以下のコードを実行して、グループ画像内の複数の顔への参照を取得します。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

次に、以下のコード ブロックを追加して、グループ内で最初の顔のインスタンスを探します。 この動作を変更する方法を学習するには、[find_similar](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) メソッドを参照してください。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>一致するものを出力する

次のコードを使用して、一致の詳細をコンソールに出力します。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>人物グループを作成してトレーニングする

次のコードでは、3 つの異なる **Person** オブジェクトを持つ **PersonGroup** を作成します。 各 **Person** が例の画像のセットに関連付けられ、各人物を認識できるようにトレーニングが行われます。 

### <a name="create-persongroup"></a>PersonGroup を作成する

このシナリオの手順を実行するには、プロジェクトのルート ディレクトリに画像 (https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images ) を保存する必要があります。

この画像のグループには、3 人の異なる人物に対応する顔画像の 3 つのセットが含まれています。 このコードでは、3 つの **Person** オブジェクトを定義し、それらを `woman`、`man`、および `child` で始まる画像ファイルに関連付けます。

画像の設定後、作成する **PersonGroup** オブジェクトのために、スクリプトの先頭でラベルを定義します。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

次に、以下のコードをスクリプトの末尾に追加します。 このコードでは、**PersonGroup** オブジェクトと 3 つの **Person** オブジェクトが作成されます。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>顔を人物に割り当てる

次のコードでは、画像をプレフィックスで並べ替え、顔を検出し、顔を各 **Person** オブジェクトに割り当てます。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

> [!TIP]
> URL によって参照されたリモート画像から **PersonGroup** を作成することもできます。 [PersonGroupPersonOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations) のメソッドを参照してください (**add_face_from_url** など)。

### <a name="train-persongroup"></a>PersonGroup をトレーニングする

顔を割り当てたら、**PersonGroup** をトレーニングして、その各 **Person** オブジェクトに関連付けられている視覚的特徴を識別できるようにする必要があります。 次のコードは、非同期の **train** メソッドを呼び出し、結果をポーリングして、状態をコンソールに出力します。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

> [!TIP]
> Face API は、本質的に静的な一連の事前構築済みモデルで実行されます (サービスの実行中にモデルのパフォーマンスが低下したり改善されたりすることはありません)。 Microsoft により、まったく新しいモデル バージョンに移行することなくモデルのバックエンドが更新されると、モデルによって生成される結果が変わる可能性があります。 より新しいバージョンのモデルを利用するには、同じ登録画像でより新しいモデルをパラメーターとして指定し、**PersonGroup** を再トレーニングすることができます。

## <a name="identify-a-face"></a>顔を識別する

識別操作では、人物 (1人または複数人) の画像を受け取り、その画像に含まれるそれぞれの顔の同一性を見つけます (顔認識検索)。 検出された顔はそれぞれ、顔の特徴が確認されているさまざまな **Person** オブジェクトのデータベース、つまり **PersonGroup** と比較されます。

> [!IMPORTANT]
> この例を実行するには、まず、「[人物グループを作成してトレーニングする](#create-and-train-a-person-group)」のコードを実行する必要があります。

### <a name="get-a-test-image"></a>テスト画像を取得する

次のコードは、プロジェクトのルートで画像 _test-image-person-group.jpg_ を探し、画像内の顔を検出します。 この画像は、**PersonGroup** の管理に使用される画像と同じ場所 (https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images ) にあります。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>顔を識別する

**identify** メソッドは、検出された顔の配列を受け取り、それらを **PersonGroup** と比較します。 検出された顔を **Person** と照合できる場合は、結果を保存します。 このコードは、詳細な一致結果をコンソールに出力します。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>顔を確認する

確認操作では、顔 ID と、別の顔 ID または **Person** オブジェクトのいずれかを取得し、同じ人に属しているかどうかを判断します。

次のコードでは、2 つのソース画像から顔を検出し、ターゲット画像から検出された顔と照らしてそれらを確認します。

### <a name="get-test-images"></a>テスト イメージを取得する

次のコード ブロックでは、確認操作のソース画像とターゲット画像を指す変数を宣言します。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>確認対象の顔を検出する

次のコードは、ソース画像とターゲット画像から顔を検出して、それらを変数に保存します。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>確認の結果を取得する

次のコードでは、各ソース画像をターゲット画像と比較し、同じ人物のものであるかどうかを示すメッセージを出力します。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="run-the-application"></a>アプリケーションの実行

`python` コマンドを使用して、アプリケーション ディレクトリから顔認識アプリを実行します。

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

このクイックスタートで作成してある **PersonGroup** を削除したい場合は、スクリプトで次のコードを実行します。

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Python 用の Face クライアント ライブラリを使用して基本的な顔認識タスクを行う方法について学習しました。 次は、リファレンス ドキュメントを参照して、ライブラリの詳細について学習してください。

> [!div class="nextstepaction"]
> [Face API リファレンス (Python)](/python/api/azure-cognitiveservices-vision-face/)

* [Face サービスとは](../../overview.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) にあります。
