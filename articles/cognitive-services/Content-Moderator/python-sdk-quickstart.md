---
title: クイック スタート:Python 用 Content Moderator クライアント ライブラリ |Microsoft Docs
description: Python 用 Content Moderator クライアント ライブラリの使用を開始します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: pafarley
ms.openlocfilehash: f3b9a7aefc5fc347c4d5114575388914ea8d6fee
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698535"
---
# <a name="quickstart-content-moderator-client-library-for-python"></a>クイック スタート:Python 用 Content Moderator クライアント ライブラリ

Python 用 Content Moderator クライアント ライブラリの使用を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 Content Moderator は、テキスト、画像、ビデオのコンテンツに不快感を与える可能性がある内容、リスクのある内容、その他望ましくない可能性のある内容が含まれているかどうかを確認できるコグニティブ サービスです。 このサービスでは、そのような内容が検出されると、それに応じたラベル (フラグ) がコンテンツに適用されます。 その後は、法的規制に準拠したり、ユーザーに意図されている環境を維持したりするために、フラグの設定されたコンテンツをアプリで処理することができます。

Python 用 Content Moderator クライアント ライブラリは、次の目的で使用できます。

* [テキストのモデレート](#moderate-text)
* [カスタム用語リストの使用](#use-a-custom-terms-list)
* [画像のモデレート](#moderate-images)
* [カスタムの画像リストの使用](#use-a-custom-image-list)
* [レビューを作成する](#create-a-review)

[リファレンス ドキュメント](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [パッケージ (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [サンプル](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>設定

### <a name="create-a-content-moderator-azure-resource"></a>Content Moderator の Azure リソースを作成する

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) または [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) を使用して、ローカル コンピューター上に Content Moderator 用のリソースを作成します。 さらに、以下を実行できます。

* 7 日間有効な[試用版のキー](https://azure.microsoft.com/try/cognitive-services/#decision)を無料で入手する。 サインアップ後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis/)でこれを入手できます。  
* 自分のリソースを [Azure portal](https://portal.azure.com/) で表示する。

試用版のサブスクリプションまたはリソースからキーを取得した後、`CONTENT_MODERATOR_SUBSCRIPTION_KEY` という名前のキーの[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。
 
### <a name="create-a-python-script"></a>Python スクリプトを作成する

新しい Python スクリプトを作成して、希望するエディターまたは IDE で開きます。 その後、次の `import` ステートメントをファイルの先頭に追加します。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

次に、自分のリソースの Azure の場所用の変数と、環境変数としてのキーを作成します。 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、もう一度開く必要があります。

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

次のコマンドを使用して、Content Moderator クライアント ライブラリをインストールできます。

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>オブジェクト モデル

次に示すクラスでは、Content Moderator Python SDK の主要な機能の一部が処理されます。

|名前|説明|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|このクラスは、すべての Content Moderator の機能に必要です。 サブスクリプション情報を使用してこれをインスタンス化し、他のクラスのインスタンスを生成するために使用します。|
|[ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|このクラスでは、成人向けコンテンツ、個人情報、または人間の顔の画像を分析するための機能が提供されます。|
|[TextModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|このクラスでは、言語、冒涜的表現、エラー、個人情報のテキストを分析するための機能が提供されます。|
[ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|このクラスでは、ジョブ、カスタム ワークフロー、人によるレビューを作成するためのメソッド含む、レビュー API の機能が提供されます。|

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Python 用 Content Moderator クライアント ライブラリを使用して次のタスクを実行する方法が示されます。

* [クライアントを認証する](#authenticate-the-client)
* [テキストのモデレート](#moderate-text)
* [カスタム用語リストの使用](#use-a-custom-terms-list)
* [画像のモデレート](#moderate-images)
* [カスタムの画像リストの使用](#use-a-custom-image-list)
* [レビューを作成する](#create-a-review)

## <a name="authenticate-the-client"></a>クライアントを認証する

> [!NOTE]
> このクイック スタートでは、`CONTENT_MODERATOR_SUBSCRIPTION_KEY` という名前の Content Moderator キーの[環境変数が作成](../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)されていることを前提としています。

ご利用のエンドポイントとキーを使用してクライアントをインスタンス化します。 キーを使用して [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) オブジェクトを作成し、それをエンドポイントと共に使用して、[ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python) オブジェクトを作成します。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>テキストのモデレート

次のコードでは、Content Moderator クライアントを使用してテキストの本文が分析され、結果がコンソールに出力されます。 まず、自分のプロジェクトのルートに **text_files/** フォルダーを作成し、*content_moderator_text_moderation.txt* ファイルを追加します。 このファイルに独自のテキストを追加するか、次のサンプル テキストを使用します。

```
Is this a grabage email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
Crap is the profanity here. Is this information PII? phone 3144444444
```

新しいフォルダーに参照を追加します。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

その後、次のコードを Python スクリプトに追加します。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>カスタム用語リストの使用

次のコードでは、テキストのモデレートのカスタム用語リストを管理する方法が示されます。 [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) クラスを使用して、用語リストを作成したり、個々の用語を管理したり、リストに対してテキストの他の本文を選別したりすることができます。

### <a name="get-sample-text"></a>サンプル テキストを取得する

このサンプルを使用するには、自分のプロジェクトのルートに **text_files/** フォルダーを作成し、*content_moderator_term_list.txt* ファイルを追加します。 このファイルには、用語のリストと照らし合わせて検査される、有機的なテキストが含まれている必要があります。 次のサンプル テキストを使用することができます。

```
This text contains the terms "term1" and "term2".
```

まだ定義していない場合は、フォルダーに参照を追加します。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>リストを作成する

次のコードを自分の Python スクリプトに追加して、カスタム用語リストを作成し、その ID 値を保存します。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>リストの詳細を定義する

リストの ID を使用して、その名前と説明を編集できます。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>リストに用語を追加する

次のコードでは、リストに `"term1"` と `"term2"` の用語が追加されます。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>リスト内の用語をすべて取得する

リスト ID を使用して、リスト内の用語をすべて返すことができます。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>リスト インデックスを更新する

リストから用語を追加または削除した場合、更新されたリストを使用するには、インデックスを更新する必要があります。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>リストに対してテキストを選別する

カスタム用語リストの主要な機能は、テキストの本文をリストと比較し、一致する用語があるかどうかを見つけることです。 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>リストから用語を削除する

次のコードで、リストから項目 `"term1"` が削除されます。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>リストからすべての用語を削除する

次のコードを使用して、そのすべての用語のリストをクリアします。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-list"></a>リストを削除する

次のコードを使用して、カスタム用語リストを削除します。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>画像のモデレート

次のコードでは、Content Moderator クライアントが [ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python) オブジェクトとともに使用され、成人向けコンテンツやきわどい内容の画像が分析されます。

### <a name="get-images"></a>画像を取得する

分析する画像に対する参照を定義します。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

その後、次のコードを追加して、画像を反復処理します。 このセクションの残りのコードは、このループ内で実行されます。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>成人向けコンテンツやきわどい内容を確認する

次のコードでは、指定された URL にある画像の成人向けコンテンツやきわどい内容が確認され、結果がコンソールに出力されます。 これらの用語の意味の情報については、[画像のモデレートの概念](./image-moderation-api.md)に関するページを参照してください。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>表示可能なテキストを確認する

次のコードでは、画像の表示可能なテキスト コンテンツが確認され、結果がコンソールに出力されます。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>顔を確認する

次のコードでは、画像の人間の顔が確認され、結果がコンソールに出力されます。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>カスタムの画像リストの使用

次のコードでは、画像のモデレートに対してカスタムの画像リストを管理する方法が示されます。 この機能は、排除する画像の同じセットのインスタンスをプラットフォームで頻繁に受け取る場合に便利です。これらの特定の画像のリストを保持することで、パフォーマンスを向上させることができます。 [ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) クラスによって、画像リストを作成したり、リストの個々の画像を管理したり、リストと他の画像を比較したりすることができます。

次のテキスト変数を作成して、このシナリオで使用する画像の URL を格納します。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> これは適切なリスト自体ではありませんが、コードの `add images` セクションに追加される画像の非公式リストです。


### <a name="create-an-image-list"></a>イメージ リストを作成する

次のコードを追加して、画像リストを作成し、その ID に参照を保存します。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>リストに画像を追加する

次のコードで、自分の画像をすべてリストに追加します。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

スクリプト内の他の場所で **add_images** ヘルパー関数を定義します。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>リスト内の画像を取得する

次のコードでは、リストにあるすべての画像の名前が出力されます。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>リストの詳細を更新する

リスト ID を使用して、リストの名前と説明を更新できます。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>リストの詳細を取得する

次のコードを使用して、リストの現在の詳細を出力します。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>リスト インデックスを更新する

画像を追加または削除した後で、他の画像の選別に使用する前に、リスト インデックスを更新する必要があります。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>画像をリストと照合する

画像リストの主要な機能は、新しい画像を比較し、一致するものがあるかどうかを確認することです。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>リストから画像を削除する

次のコードでは、リストから項目が削除されます。 この場合、これはリスト カテゴリに一致しない画像です。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>リストからすべての画像を削除する

次のコードを使用して、画像リストをクリアします。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>画像リストを削除する

次のコードを使用して、指定した画像リストを削除します。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>レビューを作成する

Content Moderator Python SDK を使用して、人間のモデレーターがレビューできるように、[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)にコンテンツをフィードすることができます。 レビュー ツールの詳細については、[概念的なガイド](./review-tool-user-guide/human-in-the-loop.md)に関するページを参照してください。

次のコードでは、[ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) クラスを使用してレビューが作成され、その ID が取得されて、レビュー ツールの Web ポータルから人間による入力を受け取った後、その詳細が確認されます。

### <a name="get-review-credentials"></a>レビューの資格情報を取得する

まず、レビュー ツールにサインインし、チーム名を取得します。 次に、それをコード内の適切な変数に割り当てます。 必要に応じて、コールバック エンドポイントを設定して、レビューのアクティビティの更新を受け取ることができます。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>画像レビューを作成する

次のコードを追加して、指定した画像の URL のレビューを作成して投稿します。 このコードでは、レビュー ID への参照を保存します。 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>レビューの詳細を取得する

次のコードを使用して、指定したレビューの詳細を確認します。 レビューを作成したら、自分でレビュー ツールにアクセスし、コンテンツとやりとりすることができます。 完了したら、このコードをもう一度実行すると、レビュー プロセスの結果が取得されます。

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

このシナリオでコールバック エンドポイントを使用した場合、次の形式でイベントを受信する必要があります。

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `python` コマンドを使用して、アプリケーションを実行します。

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Content Moderator Python ライブラリを使用して、モデレーション タスクを行う方法について学習しました。 次は、概念的なガイドを読んで、画像や他のメディアのモデレーションに関する詳細について学習します。

> [!div class="nextstepaction"]
>[画像のモデレートの概念](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Azure Content Moderator とは](./overview.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision) にあります。