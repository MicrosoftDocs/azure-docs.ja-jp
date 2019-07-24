---
title: Conversation Learner モデルでバージョンのタグ付けを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルでバージョン管理とタグ付けを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 5073d3ab967c4c4e1b90636c247839875a6aa0d7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58086311"
---
# <a name="how-to-use-version-tagging"></a>バージョンのタグ付けを使用する方法

このチュートリアルでは、Conversation Learner モデルのバージョンにタグ付けし、どのバージョンが "ライブ" であるかを設定する方法を示します。  

## <a name="requirements"></a>必要条件
このチュートリアルでは、[Log Dialog]\(会話記録\) の Web UI ではなく、Bot Framework Emulator を使用して会話記録を作成する必要があります。  

このチュートリアルでは、汎用チュートリアルのボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細

モデルのタグ付けされたバージョンは静的です。それらを編集または変更することはできません。 モデルを編集するときは常に、マスター バージョンを編集しています。 新しいタグを追加すると、Conversation Learner によってその時点でのモデルのスナップショットがキャプチャされます。 

ボットではユーザーが "ライブ" バージョンとして選択したモデルのバージョンが使用されますが、その会話は "タグの編集" が "マスター" に設定されている場合にのみ表示できます。 モデルの "タグの編集" プロパティが "マスター" 以外に設定されている場合は、モデルのスナップショットを見ることはできますが、どのような変更も行うことはできません。

## <a name="steps"></a>手順

### <a name="install-the-bot-framework-emulator"></a>Bot Framework Emulator をインストールする

1. [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) に移動します。
2. エミュレーターをダウンロードしてインストールします。

### <a name="create-a-model"></a>モデルの作成

1. モデル一覧のホーム ページで [`New Model`]\(新しいモデル\) ボタンをクリックします。
2. [`Name`]\(名前\) フィールドに「Tutorial-18-Versioning」と入力して Enter キーを押します。
4. 左側のパネルで [Settings]\(設定\) をクリックします。
5. CONVERSATION_LEARNER_MODEL_ID フィールドの内容をクリップボードにコピーします。

### <a name="configure-the-emulator"></a>Emulator を構成する

1. Conversation Learner のルート フォルダーで ".env" ファイルを開きます。
2. 次のように ".env" ファイルに行を追加します。
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. コマンド プロンプトを終了し、次のコマンドを再度実行して、Conversation Learner サービスを再起動します。
    - `npm run tutorial-general`
4. Bot Framework Emulator で新しいボットの構成を作成し、エンドポイントの URL を `http://localhost:3978/api/messages` に設定します

### <a name="version-1"></a>Version 1

バージョン 1 に対して 1 つのアクションを作成します。

1. Web UI の左側のパネルで [Actions]\(アクション\) をクリックし、[`New Action`]\(新しいアクション\) ボタンをクリックします。
2. [Bot's Response]\(ボットの応答\) フィールドに、「hi there (version 1)」(これは (バージョン 1) です) と入力します。
3. `Save` ボタンをクリックします。

次に、これにモデルの "バージョン 1" としてタグを付けます。

1. 左側のパネルで [settings]\(設定\) をクリックし、![](../media/tutorial18_version_tags.PNG) [Version Tags]\(バージョン タグ\) アイコンをクリックして [`New Tag`]\(新しいタグ\) ボタンを表示して、それをクリックします。
    - "Version 1" という名前を付けます
1. [Live Tag]\(ライブ タグ\) ドロップ ダウンで、"Version 1" を選択します。  
    - このボットを使用するチャネルが、このモデルの "Version 1" を使用するようになります。
    - この Version 1 モデルのエンティティ、アクション、トレーニング会話は変更できなくなります。
    - [Editing Tag]\(タグの編集\) として "Version 1" を選択した場合、モデルを表示することだけができ、編集はできません。
    - [Editing Tag]\(タグの編集\) は [Master]\(マスター\) に設定したままにします。これはモデルで編集できる唯一のバージョンです。

[Version Tags]\(バージョン タグ\) グリッドに "Version 1" と表示されます。

### <a name="version-2"></a>Version 2

次に、モデルを編集して、Version 1 と区別します。

1. 左側のパネルで [Actions]\(アクション\) をクリックします。
2. [Actions]\(アクション\) グリッドで、"hi there (version 1)" (やぁ これはバージョン 1 です) をクリックします。
3. [Bot's response]\(ボットの応答\) フィールドを、「hi there (version 2)」(これは (バージョン 2) です) に変更します。
4. `Save` ボタンをクリックします。
5. `New Action` ボタンをクリックします。
6. [Bot's response]\(ボットの応答\) フィールドに、「bye bye (version 2)」(バイバイ (バージョン 2)) と入力します。

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>Bot Framework Emulator で Version 1 が使用されていることを確認する

1. Bot Framework Emulator で、「Hey there」(やぁ) と入力します。
2. ボットから "hi there (version 1)" という応答が返されることに注意してください。
    - これは Version 1 が "ライブ" であることを示します。

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>Conversation Learner の Web UI で会話のログを表示する

1. 左側のパネルで [Log Dialogs]\(会話記録\) をクリックします。
    - 会話がまったく表示されない場合は、更新ボタンをクリックします。
2. グリッドに "Version 1" タグが表示されることを確認します。
3. グリッドで、"hi there (version 1)" をクリックします

> [!NOTE]
> 現在使用可能なすべての Conversation Learner 機能から選択して修正できますが、これらの編集は Version 1 ではなく Master に対して行われます。

以上、バージョン管理の動作、および Bot Framework Emulator を使用してボットを操作する方法について見てきました。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [デモ - パスワードのリセット](./demo-password-reset.md)
