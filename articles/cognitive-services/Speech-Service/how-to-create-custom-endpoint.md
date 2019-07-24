---
title: Azure 上で Speech Services を使用してカスタム音声エンドポイントを作成する | Microsoft Docs
description: Azure Speech Services を使用してカスタム Speech-to-Text エンドポイントを作成する方法を説明します。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 03/12/2018
ms.author: panosper
ms.openlocfilehash: 1f7a84d187ba6279caad4926d54bfc56254152af
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863000"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>カスタム Speech-to-Text エンドポイントの作成

カスタム音響モデルまたは言語モデルを作成したら、カスタム Speech-to-Text エンドポイントに展開できます。

## <a name="create-an-endpoint"></a>エンドポイントの作成
新しいカスタム エンドポイントを作成するには、ページの一番上の **[Custom Speech]** メニューで **[エンドポイント]** を選択します。 この操作により、現在のカスタム エンドポイントの表を含む **[エンドポイント]** ページが表示されます。 まだエンドポイントを作成していない場合には表は空です。 現在のロケールが表のタイトルに反映されます。

別の言語のデプロイを作成するには、**[Change Locale]\(ロケールの変更\)** を選択します。 詳細については、サポートされている言語に関するページを参照してください。

新しいエンドポイントを作成するには、**[新規作成]** を選択します。 **[エンドポイントの作成]** ウィンドウで、カスタム デプロイメントの **[名前]** ボックスと **[説明]** ボックスに情報を入力します。

**[サブスクリプション]** コンボ ボックスで、使用するサブスクリプションを選択します。 S0 (有料) サブスクリプションである場合、同時要求を選択できます。

コンテンツのログのオンまたはオフを選択することもできます。 つまり、エンドポイント トラフィックを格納するかどうかを選択します。 選択されていない場合、トラフィックの格納は抑制されます。 ログに記録されるすべてのコンテンツに関して、[エンドポイント] の [詳細表示] にダウンロード リンクが表示されます。

**[Acoustic Model]\(音響モデル\)** リストで必要な音響モデルを選択し、**[Language Model]** リストで必要な言語モデルを選択します。 音響モデルと言語モデルの選択肢には、必ず基本の Microsoft モデルが含まれます。 基本モデルを選択すると組み合わせが制限されます。 会話の基本モデルを検索とディクテーションの基本モデルと混在させることはできません。

> [!NOTE]
> チェック ボックスをオンにして、使用条件と価格情報に同意したことを確認します。
>

音響モデルと言語モデルを選択したら、**[作成]** を選択します。 この操作によって、**[デプロイ]** ページに戻ります。 表に、新しいエンドポイントに対応するエントリが含まれています。 エンドポイントが作成中の場合、状態は現在の状態を反映します。 新しいエンドポイントをカスタム モデルでインスタンス化するには、最大で 30 分かかることがあります。 デプロイの状態が *[完了]* の場合、エンドポイントは使用可能です。

デプロイの準備ができたら、エンドポイント名がリンクとして表示されます。 リンクを選択すると、**[エンドポイント情報]** ページが表示され、HTTP 要求または Microsoft Cognitive Services Speech クライアント ライブラリ (Web ソケット使用) で使用するカスタム エンドポイントの URL が表示されます。

## <a name="next-steps"></a>次の手順

その他のチュートリアルについては、以下を参照してください。
- [Speech Services 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
- [カスタム音響モデルを作成する](how-to-customize-acoustic-models.md)
- [カスタム言語モデルを作成する](how-to-customize-language-model.md)
