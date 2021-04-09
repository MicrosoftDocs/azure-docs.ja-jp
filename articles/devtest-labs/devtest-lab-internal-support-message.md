---
title: Azure DevTest Labs のラボに社内サポートの説明文を追加する
description: 社内サポートの説明文を Azure DevTest Labs のラボに投稿する方法を説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1c9920e6fe7fbfe2a8d0aeacb896150b342981b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480475"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs のラボに社内サポートの説明文を追加する

Azure DevTest Labs では、社内サポートの説明文を付けてラボをカスタマイズできます。これは、ラボに関するサポート情報をユーザーに示すものです。 たとえば、連絡先情報を指定すると、ユーザーがトラブルシューティングやラボのリソースへのアクセスに関して支援が必要な場合に、社内サポートへの連絡方法がわかります。 また、チームがサポートに連絡する前にアクセスできるように社内 Web サイトや FAQ へのリンクを付けることもできます。

社内サポートの説明文は、あまり頻繁に変わらないラボの情報を投稿するために使用します。 ラボ ポリシーの最近の更新など一時的な性質のラボ情報に関してユーザーに通知するには、[ラボへのお知らせの投稿](devtest-lab-announcements.md)に関する記事をご覧ください。

サポートの説明文が適切でなくなった場合には、簡単に無効にしたり編集したりすることができます。

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>サポートの説明文を既存のラボに追加する手順

1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
1. 必要に応じて、 **[All Services]\(その他のサービス\)** を選択し、一覧から **[DevTest Labs]** を選択します。 (お使いのラボは、 **[すべてのリソース]** の [ダッシュボード] に既に表示されている場合があります)。
1. ラボの一覧から、サポートの説明文を追加するラボを選択します。  
1. ラボの **[概要]** で、 **[Configuration and policies]\(構成とポリシー\)** を選択します。  

    ![[Configuration and policies]\(構成とポリシー\) ボタン](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. 左側の **[設定]** の下で **[社内サポート]** を選択します。

    ![[社内サポート] ボタン](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. このラボのユーザーへの社内サポートに関するメッセージを作成するには [有効] を **[はい]** に設定します。

1. **[サポートのメッセージ]** フィールドに、ラボ ユーザーに対して表示する社内サポートの説明文を入力します。 サポートのメッセージには Markdown を使用できます。 メッセージのテキストを入力すると同時に、画面の下部にある **[プレビュー]** 領域で、メッセージがユーザーにどのように表示されるかを確認できます。

    ![メッセージを作成する社内サポートの画面。](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. サポートの説明文を投稿する準備ができたら、**[保存]** を選択します。

このサポートのメッセージをラボのユーザーに表示する必要がなくなったら、**[社内サポート]** ページに戻り、**[有効]** を **[いいえ]** に設定します。

## <a name="steps-for-users-to-view-the-support-message"></a>ユーザーがサポートのメッセージを表示するための手順

1. [[Azure ポータル]](https://go.microsoft.com/fwlink/p/?LinkID=525040) から、ラボを選択します。

1. ラボの **[概要]** 領域で **[社内サポート]** を選択します。  

    ![[社内サポート] ボタン](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. サポートのメッセージが投稿されると、ユーザーは [社内サポート] ウィンドウでメッセージを表示できます。

    ![投稿されたサポートのメッセージを示す [社内サポート] ウィンドウ](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次のステップ
* 社内サポートの説明文は、通常、頻繁に変わらないサポート情報を提供するために使用されます。 ラボの一時的な変更や更新プログラムをユーザーに知らせるために、[ラボにお知らせを投稿する](devtest-lab-announcements.md)方法を学ぶこともできます。
* カスタマイズしたポリシーを使用して、サブスクリプションに他の制限と規則を適用する方法については[ポリシーとスケジュールの設定](devtest-lab-set-lab-policy.md)に関するページを参照してください。