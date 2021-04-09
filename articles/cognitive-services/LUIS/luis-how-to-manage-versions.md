---
title: バージョンの管理 - LUIS
titleSuffix: Azure Cognitive Services
description: バージョンを使用して、さまざまなモデルを構築および公開できます。 モデルに変更を加える前に、現在のアクティブなモデルをアプリの別のバージョンに複製することをお勧めします。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: b93e9fd4047ee3731ef6fe4652bb92ce5145987d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018822"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>バージョンを使用してステージング アプリまたは運用環境アプリに影響を与えることなく編集とテストを行う

バージョンを使用して、さまざまなモデルを構築および公開できます。 モデルに変更を加える前に、現在のアクティブなモデルをアプリの別の[バージョン](./luis-concept-app-iteration.md)に複製することをお勧めします。

アクティブなバージョンは、意図、エンティティ、特徴、およびパターンについて LUIS ポータル上の **[ビルド]** セクションで編集しているバージョンです。 オーサリング API を使用している場合、バージョン固有の REST API の呼び出しではルートにそのバージョンが含まれているため、アクティブなバージョンを設定する必要はありません。

バージョンを操作するには、 **[マイ アプリ]** ページで名前を選択してアプリを開き、上部のバーにある **[管理]** を選択し、左側のナビゲーションで **[バージョン]** を選択します。

バージョンの一覧には、公開されているバージョン、公開されている場所、現在アクティブであるバージョンが表示されます。

## <a name="clone-a-version"></a>バージョンを複製する

1. 複製するバージョンを選択し、ツール バーから **[複製]** を選択します。

2. **[Clone version]\(複製のバージョン\)** ダイアログ ボックスで、新しいバージョンの名前 ("0.2" など) を入力します。

   ![[Clone version]\(複製のバージョン\) ダイアログ ボックス](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)

     > [!NOTE]
     > バージョン ID は、文字、数字、または '.' のみで構成できます。文字数は 10 文字までです。

   指定した名前の新しいバージョンが作成され、アクティブなバージョンとして設定されます。

## <a name="set-active-version"></a>アクティブなバージョンを設定する

一覧からバージョンを選択し、ツール バーから **[アクティブにする]** を選択します。

## <a name="import-version"></a>バージョンをインポートする

アプリケーションの `.json` または `.lu` バージョンをインポートできます。

1. ツール バーの **[インポート]** を選択し、形式を選択します。

2. **[Import new version]\(新しいバージョンのインポート\)** ポップアップ ウィンドウで、新しい 10 文字のバージョン名を入力します。 ファイルのバージョンが既にアプリに存在する場合は、バージョン ID のみを設定する必要があります。

    ![[管理] セクションの [バージョン] ページ、新しいバージョンのインポート](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    バージョンをインポートすると、新しいバージョンがアクティブなバージョンになります。

### <a name="import-errors"></a>インポート エラー

* トークナイザー エラー:インポート中に **トークナイザー エラー** が発生した場合、アプリで現在使用しているものと異なる [トークナイザー](luis-language-support.md#custom-tokenizer-versions)を使用しているバージョンをインポートしようとしています。 これを修正するには、「[トークナイザーのバージョン間の移行](luis-language-support.md#migrating-between-tokenizer-versions)」を参照してください。

<a name = "export-version"></a>

## <a name="other-actions"></a>その他のアクション

* バージョンを **削除** するには、一覧からバージョンを選択し、ツール バーから **[削除]** を選択します。 **[OK]** を選択します。
* バージョンの **名前を変更** するには、一覧からバージョンを選択し、ツール バーから **[名前の変更]** を選択します。 新しい名前を入力して **[完了]** を選択します。
* バージョンを **エクスポート** するには、一覧からバージョンを選択し、ツール バーから **[Export app]\(アプリのエクスポート\)** を選択します。 [このアプリを LUIS コンテナーで使用](luis-container-howto.md)するには、バックアップ用にエクスポートする、またはソース管理に保存する JSON または LU を選択し、 **[Export for container]\(コンテナー用にエクスポート\)** を選択します。