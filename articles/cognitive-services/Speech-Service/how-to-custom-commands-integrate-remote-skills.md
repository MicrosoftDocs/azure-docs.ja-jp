---
title: 方法:Custom Commands アプリケーションをリモートのスキルとしてエクスポートする - Speech サービス
titleSuffix: Azure Cognitive Services
description: この記事では、Custom Commands アプリケーションをスキルとしてエクスポートする方法について説明します
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: sausin
ms.openlocfilehash: 77ade17803a35491712ec6df70aed9eb7b4883eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95025876"
---
# <a name="export-custom-commands-application-as-a-remote-skill"></a>Custom Commands アプリケーションをリモートのスキルとしてエクスポートする

この記事では、Custom Commands アプリケーションをリモートのスキルとしてエクスポートする方法について説明します。

## <a name="prerequisites"></a>前提条件
> [!div class="checklist"]
> * [Bot Framework スキルに関する理解](/azure/bot-service/skills-conceptual)
> * [スキル マニフェストに関する理解](https://aka.ms/speech/cc-skill-manifest)
> * [Bot Framework ボットからのスキルの呼び出し方法](/azure/bot-service/skills-about-skill-consumers)
> * 既存の Custom Commands アプリケーション。 Custom Commands アプリケーションがない場合は、「[クイックスタート: カスタム コマンドを使用して音声アシスタントを作成する](quickstart-custom-commands-application.md)」を参照して試してみてください。

## <a name="custom-commands-as-remote-skills"></a>リモートのスキルとしての Custom Commands
* Bot Framework のスキルは、会話のユースケースをカバーする再利用可能な会話スキル構成要素であり、数分以内にボットに広範な機能を追加できるようになります。 この詳細については、[Bot Framework のスキル](https://microsoft.github.io/botframework-solutions/overview/skills/)に関するページを参照してください。
* Custom Commands アプリケーションは、スキルとしてエクスポートできます。 このスキルはその後、Bot Framework ボットからリモート スキル プロトコルを介して呼び出すことができます。

## <a name="configure-an-application-to-be-exposed-as-a-remote-skill"></a>リモートのスキルとして公開されるようにアプリケーションを構成する

### <a name="application-level-settings"></a>アプリケーション レベルの設定
1. 左側のパネルで、 **[設定]**  >  **[Remote skills]\(リモートのスキル\)** の順に選択します。
1. **[Remote skills enabled]\(リモートのスキルが有効\)** トグルをオンに設定します。

### <a name="authentication-to-skills"></a>スキルへの認証
1. 認証を有効にする場合は、カスタム コマンド アプリケーションを呼び出すように構成する Bot Framework ボットの Microsoft アプリケーション ID を追加します。
      > [!div class="mx-imgBorder"]
      > ![MSA ID をスキルに追加する](media/custom-commands/skill-add-msa-id.png)

1. 少なくとも 1 つのエントリを一覧に追加すると、アプリケーションで認証が有効になり、許可されているボットだけがアプリケーションを呼び出すことができるようになります。
> [!TIP]
>  認証を無効にするには、許可リストからすべての Microsoft アプリケーション ID を削除します。 

 ### <a name="enabledisable-commands-to-be-exposed-as-skills"></a>スキルとして公開されるコマンドを有効または無効にする

どのコマンドをリモートのスキルを介してエクスポートするかを選択することができます。

1. スキルを介してコマンドを公開するには、 **[Enable commands for skills]\(スキルのコマンドを有効にする\)** の下にある **[Enable a new command]\(新しいコマンドを有効にする\)** を選択します。
1. ドロップダウンから、追加するコマンドを選択します。
1. **[保存]** を選択します。

### <a name="configure-triggering-utterances-for-commands"></a>コマンドのトリガーとなる発話を構成する
Custom Commands によって、スキルのトリガーとなる発話を生成するために、コマンド用に構成された例文が使用されます。 これらの **トリガーとなる発話** は、**ディスパッチャー** セクションの [**スキル マニフェスト**](https://microsoft.github.io/botframework-solutions/skills/handbook/manifest/)の生成に使用されます。

作成者として、スキルのトリガーとなる発話を生成するためにどの **例文** を使用するかを制御したい場合があります。
1. 既定では、コマンドからのすべての **トリガー例** がマニフェスト ファイルに含まれます。
1. 1 つの例を明示的に削除する場合は、 **[Enabled commands for skills]\(スキルの有効化されたコマンド\)** セクションで、コマンド上の **[編集]** アイコンを選択します。
    > [!div class="mx-imgBorder"]
    > ![スキルの有効化されたコマンドを編集する](media/custom-commands/skill-edit-enabled-command.png)

1. 次に、省略する例文の上で、**右クリック** >  **[Disable Example Sentence]\(例文を無効にする\)** を選択します。
    > [!div class="mx-imgBorder"]
    > ![例を無効にする](media/custom-commands/skill-disable-example-sentences.png)

1. **[保存]** を選択します。
1. このウィンドウには新しい例を追加できないことに気づかれるでしょう。 そうする必要がある場合は、処理を進めて設定セクションを終了し、 **[コマンド]** アコーディオンから関連するコマンドを選択します。 この時点で、 **[Example sentences]\(例文\)** セクションに新しいエントリを追加できます。 この変更は、コマンドのリモートのスキル設定値に自動的に反映されます。

> [!IMPORTANT]
> 既存の例文に **String > Catalog** データ型への参照が含まれている場合、これらの文はスキルのトリガーとなる発話のリストから自動的に除外されます。 

## <a name="download-skill-manifest"></a>スキル マニフェストをダウンロードする
1. アプリケーションを **公開** した後は、スキル マニフェスト ファイルをダウンロードできます。
1. スキル マニフェストを使用して、Custom Commands スキルに呼び出すように Bot Framework コンシューマー ボットを構成します。
> [!IMPORTANT]
> スキル マニフェストをダウンロードするには、Custom Commands アプリケーションを **公開** する必要があります。 </br>
> さらに、アプリケーションに **何らかの変更** を加えた場合は、最新の変更をマニフェスト ファイルに反映させるために、アプリケーションを再度公開する必要があります。

> [!NOTE]
> アプリケーションの公開に関する問題が発生し、エラーがスキルのトリガーとなる発話を示している場合は、 **[Enabled commands for skills]\(スキルの有効化されたコマンド\)** の構成を再確認してください。 公開された各コマンドには、少なくとも 1 つの有効なトリガーとなる発話が必要です。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [クライアントからコマンドを更新する](./how-to-custom-commands-update-command-from-client.md)
