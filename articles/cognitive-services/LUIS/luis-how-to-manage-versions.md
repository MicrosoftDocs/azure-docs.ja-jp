---
title: バージョンの管理
titleSuffix: Language Understanding - Azure Cognitive Services
description: バージョンを使用して、さまざまなモデルを構築および公開できます。 モデルに変更を加える前に、現在のアクティブなモデルをアプリの別のバージョンに複製することをお勧めします。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/29/2019
ms.author: diberry
ms.openlocfilehash: dfe23baa67c87b04a65630611ef71758beda268d
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58754905"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>バージョンを使用してステージング アプリまたは運用環境アプリに影響を与えることなく編集とテストを行う

バージョンを使用して、さまざまなモデルを構築および公開できます。 モデルに変更を加える前に、現在のアクティブなモデルをアプリの別の[バージョン](luis-concept-version.md)に複製することをお勧めします。 

バージョンを操作するには、**[マイ アプリ]** ページで名前を選択してアプリを開き、上部のバーにある **[管理]** を選択し、左側のナビゲーションで **[バージョン]** を選択します。 

バージョンの一覧には、公開されているバージョン、公開されている場所、現在アクティブであるバージョンが表示されます。 

[![管理 セクションの バージョン ページ](./media/luis-how-to-manage-versions/versions-import.png "管理 セクションの バージョン ページ")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>バージョンを複製する

1. 複製するバージョンを選択し、ツール バーから **[複製]** を選択します。 

2. **[Clone version]\(複製のバージョン\)** ダイアログ ボックスで、新しいバージョンの名前 ("0.2" など) を入力します。

   ![[Clone version]\(複製のバージョン\) ダイアログ ボックス](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > バージョン ID は、文字、数字、または '.' のみで構成できます。文字数は 10 文字までです。
 
   指定した名前の新しいバージョンが作成され、アクティブなバージョンとして設定されます。

## <a name="set-active-version"></a>アクティブなバージョンを設定する

一覧からバージョンを選択し、ツール バーから **[Make Active]\(アクティブにする\)** を選択します。 

[![管理 セクションの バージョン ページ、バージョンの作成アクション](./media/luis-how-to-manage-versions/versions-other.png "[管理] セクションの [バージョン] ページ、バージョンの作成アクション")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>バージョンをインポートする

1. ツール バーから **[Import version]\(バージョンのインポート\)** を選択します。 

2. **[Import new version]\(新しいバージョンのインポート\)** ポップアップ ウィンドウで、新しい 10 文字のバージョン名を入力します。 JSON ファイルのバージョンが既にアプリに存在する場合、設定する必要があるのはバージョン ID のみです。

    ![[管理] セクションの [バージョン] ページ、新しいバージョンのインポート](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    バージョンをインポートすると、新しいバージョンがアクティブなバージョンになります。

### <a name="import-errors"></a>インポート エラー

* トークナイザー エラー:インポート中に**トークナイザー エラー**が発生した場合、アプリで現在使用しているものと異なる[トークナイザー](luis-language-support.md#custom-tokenizer-versions)を使用しているバージョンをインポートしようとしています。 これを修正するには、「[トークナイザーのバージョン間の移行](luis-language-support.md#migrating-between-tokenizer-versions)」を参照してください。

<a name = "export-version"></a>

## <a name="other-actions"></a>その他のアクション

* バージョンを**削除**するには、一覧からバージョンを選択し、ツール バーから **[削除]** を選択します。 **[OK]** を選びます。 
* バージョンの**名前を変更**するには、一覧からバージョンを選択し、ツール バーから **[名前の変更]** を選択します。 新しい名前を入力して **[完了]** を選択します。 
* バージョンを**エクスポート**するには、一覧からバージョンを選択し、ツール バーから **[Export app]\(アプリのエクスポート\)** を選択します。 ファイルはローカル コンピューターにダウンロードされます。 

