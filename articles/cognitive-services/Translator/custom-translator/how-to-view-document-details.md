---
title: ドキュメントの詳細 - Custom Translator
titleSuffix: Azure Cognitive Services
description: ドキュメント一覧ページには、ワークスペースの最初の 10 個のドキュメントが表示されます。 各ドキュメントについて、名前、ペアリング、種類、言語、アップロードのタイム スタンプ、およびドキュメントをアップロードしたユーザーのメール アドレスが表示されます。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: d380c8bcdcb4954901df0b460269493c5be0a6de
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777390"
---
# <a name="view-document-details"></a>ドキュメントの詳細の表示

ドキュメント一覧ページには、ワークスペースの最初の 10 個のドキュメントが表示されます。 各ドキュメントについて、名前、ペアリング、種類、言語、アップロードのタイム スタンプ、およびドキュメントをアップロードしたユーザーのメール アドレスが表示されます。

個々のドキュメントをクリックすると、ドキュメントの詳細ページが表示されます。 ドキュメント詳細ページには、ドキュメントから抽出された文の一覧が表示されます。

- 既定では、"ソース" 言語がドロップダウン フィールドで選択されていますが、ターゲット言語の文を表示するように切り替えることができます。
- 既定では、1 ページに 20 個の文が表示されます。 改ページ コントロールを使用してページを切り替えることができます。

![ドキュメントの詳細](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>ドキュメントの削除

ドキュメントを削除するには、ドキュメントを削除できるワークスペース所有者である必要があります。 さらに、ドキュメントがモデルによって使用されている場合、つまりトレーニング プロセスの一部またはデプロイ プロセスの一部である場合は、ドキュメントを削除できません。

1. ドキュメント ページに移動します
2.  任意のドキュメント レコードをマウスでポイントし、ごみ箱アイコンをクリックします。

    ![ドキュメントの削除](media/how-to/how-to-delete-document-1.png)

3.  削除を確認します。

    ![削除の確認](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>次の手順

- [モデルをトレーニングする方法](how-to-train-model.md)について説明します。
