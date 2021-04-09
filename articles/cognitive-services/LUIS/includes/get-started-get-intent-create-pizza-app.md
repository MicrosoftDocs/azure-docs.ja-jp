---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.date: 12/29/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: aahi
ms.reviewer: roy-har
ms.openlocfilehash: 7aa2fba6ef551a745ccaf5b00f36021b9d8680ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97820728"
---
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

**[効果的な LUIS アプリの作成方法]** ダイアログが表示されたら閉じます。

## <a name="train-and-publish-the-pizza-app"></a>Pizza アプリをトレーニングして発行する

**[意図]** ページに Pizza アプリの意図一覧が表示されるはずです。

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

これで Pizza アプリを使用する準備ができました。

## <a name="record-the-app-id-prediction-key-and-prediction-endpoint-of-your-pizza-app"></a>Pizza アプリのアプリ ID、予測キー、予測エンドポイントを記録する

新しい Pizza アプリを使用するには、Pizza アプリのアプリ ID、予測キー、予測エンドポイントが必要です。

これらの値を見つけるには:

1. **[意図]** ページから **[管理]** を選択します。
1. **[アプリケーション設定]** ページから **[アプリ ID]** を記録します。
1. **[Azure リソース]** を選択します。
1. **[Azure リソース]** ページから **[主キー]** を記録します。 この値は予測キーです。
1. **[エンドポイント URL]** を記録します。 この値は予測エンドポイントです。
