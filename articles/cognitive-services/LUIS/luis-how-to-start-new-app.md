---
title: 新しいアプリの作成
titleSuffix: Language Understanding - Azure Cognitive Services
description: Language Understanding (LUIS) Web ページで、アプリケーションを作成し、管理します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: b8b0cebf4ba47f875caacfcfbf89b84551b41333
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341853"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>LUIS ポータルでの新しい LUIS アプリの作成
LUIS アプリを作成するにはいくつかの方法があります。 [LUIS](https://www.luis.ai) ポータル内または LUIS オーサリング[API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) を使用して LUIS アプリを作成できます。

## <a name="using-the-luis-portal"></a>LUIS ポータルを使用する
LUIS ポータルでは、新しいアプリをいくつかの方法で作成できます。

* 空のアプリから始めて、意図、発話、およびエンティティを作成する。
* 空のアプリから始めて、[事前構築済みのドメイン](luis-how-to-use-prebuilt-domains.md)を追加する。
* 意図、発話、およびエンティティが既に格納されている JSON ファイルから LUIS アプリをインポートする。

## <a name="using-the-authoring-apis"></a>オーサリング API を使用する
オーサリング API を使用していくつかの方法で新しいアプリを作成できます。

* 空のアプリから[始め](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)て、意図、発話、およびエンティティを作成する
* 事前構築済みのドメインから[始め](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5)る  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

## <a name="create-new-app-in-luis"></a>LUIS で新しいアプリを作成する

1. **[マイ アプリ]** ページで、 **[新しいアプリの作成]** を選択します。

    ![LUIS アプリの一覧](./media/luis-create-new-app/apps-list.png)


2. ダイアログ ボックスで、アプリケーションに "TravelAgent" という名前を付けます。

    ![[新しいアプリの作成] ダイアログ ボックス](./media/luis-create-new-app/create-app.png)

3. アプリケーションのカルチャを選択 (TravelAgent アプリの場合は、英語を選択) し、 **[完了]** を選択します。 

    > [!NOTE]
    > カルチャは、アプリケーションを作成した後に変更できません。 

## <a name="import-an-app-from-file"></a>ファイルからアプリをインポートする

1. **[マイ アプリ]** ページで、 **[Import new app]\(新しいアプリのインポート\)** を選択します。
1. ポップアップ ダイアログで有効なアプリ JSON ファイルを選択し、 **[完了]** を選択します。

### <a name="import-errors"></a>インポート エラー

次のエラーが発生する可能性があります。 

* その名前のアプリは既に存在します。 これを修正するには、アプリを再インポートし、**オプション名**を新しい名前に設定します。 

## <a name="export-app-for-backup"></a>バックアップ用にアプリをエクスポートする

1. **[マイ アプリ]** ページで **[エクスポート]** を選択します。
1. **[JSON としてエクスポート]** を選択します。 アクティブなバージョンのアプリがブラウザーによってダウンロードされます。
1. このファイルをバックアップ システムに追加して、モデルをアーカイブしてください。

## <a name="export-app-for-containers"></a>コンテナーのアプリをエクスポートする

1. **[マイ アプリ]** ページで **[エクスポート]** を選択します。
1. **[Export as container]\(コンテナーとしてエクスポートする\)** を選択して、エクスポートする公開スロット (実稼働またはステージ) を選択します。
1. [LUIS コンテナー](luis-container-howto.md)でこのファイルを使用します。 

    LUIS コンテナーで使用するモデルとして、トレーニング済みではあるものの、まだ発行されていないモデルをエクスポートしたい場合は、 **[バージョン]** ページに移動してそこからエクスポートしてください。 

## <a name="delete-app"></a>アプリの削除

1. **[マイ アプリ]** ページで、アプリの行の末尾にある 3 つのドット (...) を選択します。
1. メニューで **[削除]** を選択します。
1. 確認ウィンドウで **[OK]** を選択します。

## <a name="next-steps"></a>次の手順

アプリでの最初のタスクは、[意図の追加](luis-how-to-add-intents.md)です。
