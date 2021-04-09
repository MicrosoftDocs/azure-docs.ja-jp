---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.date: 06/04/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: aahi
ms.reviewer: roy-har
ms.openlocfilehash: a021b7c5b26e1bca1e3f623ad762c2f3ee8d6020
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97820621"
---
pizza アプリを作成します。

1. [pizza-app-for-luis-v6.json](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) を選択し、`pizza-app-for-luis.json` ファイルの GitHub ページを表示します。
1. **[Raw]** ボタンを右クリックするか長押しし、 **[名前を付けてリンク先を保存]** を選択して `pizza-app-for-luis.json` をコンピューターに保存します。
1. [LUIS ポータル](https://www.luis.ai)にサインインします。
1. [[マイ アプリ]](https://www.luis.ai/applications) を選択します。
1. **[マイアプリ]** ページで、 **[+ New app for conversation]\(+ 会話用の新しいアプリ\)** を選択します。
1. **[JSON としてインポート]** を選択します。
1. **[Import new app]\(新しいアプリのインポート\)** ダイアログで、 **[ファイルの選択]** ボタンを選択します。
1. ダウンロードした `pizza-app-for-luis.json` ファイルを選択し、 **[開く]** を選択します。
1. **[新しいアプリのインポート]** ダイアログの **[名前]** フィールドに Pizza アプリの名前を入力し、 **[完了]** ボタンを選択します。

アプリがインポートされます。

**[How to create an effective LUIS app]\(効果的な LUIS アプリを作成する方法\)** ダイアログが表示されたら閉じます。

## <a name="train-and-publish-the-pizza-app"></a>Pizza アプリをトレーニングして発行する

**[意図]** ページに Pizza アプリの意図一覧が表示されるはずです。

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

## <a name="add-an-authoring-resource-to-the-pizza-app"></a>作成リソースを Pizza アプリに追加する

1. **[管理]** を選択します。
1. **[Azure リソース]** を選択します。
1. **[Authoring Resource]\(作成リソース\)** を選択します。
1. **[Change authoring resource]\(作成リソースを変更する\)** を選択します。

作成リソースがある場合は、作成リソースの **[テナント名]** 、 **[サブスクリプション名]** 、および **[LUIS リソース名]** を入力します。

作成リソースがない場合は、次のようにします。

1. **[新しいリソースの作成]** を選択します。
1. **[テナント名]** 、 **[リソース名]** 、 **[サブスクリプション名]** 、 **[Azure リソース グループ名]** を入力します。

これで Pizza アプリを使用する準備ができました。

## <a name="record-the-access-values-for-your-pizza-app"></a>Pizza アプリのアクセス値を記録する

新しい Pizza アプリを使用するには、Pizza アプリのアプリ ID、オーサリング キー、作成エンドポイントが必要です。 予測を取得するには、個別の予測エンドポイントと予測キーが必要です。

これらの値を見つけるには:

1. **[意図]** ページから **[管理]** を選択します。
1. **[アプリケーション設定]** ページから **[アプリ ID]** を記録します。
1. **[Azure リソース]** を選択します。
1. **[Authoring Resource]\(作成リソース\)** を選択します。
1. **[Authoring Resource]\(作成リソース\)** と **[予測リソース]** タブで、**主キー** を記録します。 この値が、オーサリング キーです。
1. **[エンドポイント URL]** を記録します。 この値が、作成エンドポイントです。
