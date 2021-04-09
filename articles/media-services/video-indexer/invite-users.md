---
title: Video Indexer にユーザーを招待する - Azure
titleSuffix: Azure Media Services
description: この記事では、Video Indexer にユーザーを招待する方法について説明します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 02/03/2021
ms.author: juliako
ms.openlocfilehash: 7d3415ffad4820d8651841398ec6d47352869ce2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100534741"
---
# <a name="quickstart-invite-users-to-video-indexer"></a>クイック スタート:Video Indexer にユーザーを招待する

同僚と共同作業を行うために、自分の Video Indexer アカウントに招待することができます。 

> [!NOTE]
> ユーザーを追加または削除できるのは、アカウントの管理者だけです。

## <a name="invite-new-users"></a>新しいユーザーを招待する

1. [Video Indexer](https://www.videoindexer.ai/) Web サイトにサインインします。 管理者アカウントを使用して接続していることを確認します。
1. 管理者には、右上隅に **[Share account]\(アカウントの共有\)** ボタンが表示されます。 このボタンをクリックして、ユーザーを招待することができます。 

    :::image type="content" source="./media/invite-users/share-account.png" alt-text="アカウントを共有する":::
1. **[Share this account with others]\(このアカウントを他のユーザーと共有する\)** ダイアログで、ご利用の Video Indexer アカウントに招待したいユーザーのメール アドレスを入力します。

    :::image type="content" source="./media/invite-users/share-account-others.png" alt-text="このアカウントにユーザーを招待する":::  
1. **[Invite]\(招待\)** を押すと、保留中の招待状のリストにそのユーザーが追加されます。 <br/>アカウントにまだ参加していない各招待者については、**招待を削除** するオプションと **招待 URL をコピーする** オプションのどちらかを選択できます。

    :::image type="content" source="./media/invite-users/invites-pending.png" alt-text="保留中の招待状":::  
1. 招待された人がアカウントに参加すると、3 つのオプションを選択できるようになります。 そのうち 2 つはロールに関するオプション、つまり、 **[共同作成者]** (既定) と **[所有者]** です。 または、 **[削除]** をクリックして招待者を削除することもできます。

    :::image type="content" source="./media/invite-users/joined-invitee-options.png" alt-text="参加している招待者":::  

    ユーザーは、削除されても通知を受け取りません。 削除されると、ユーザーはログインを承認されません。

## <a name="manage-roles-invite-more-users"></a>ロールの管理とその他のユーザーの招待

**[Share this account with others]\(このアカウントを他のユーザーと共有する\)** ダイアログは、前述のように **[Share account]\(アカウントの共有\)** をクリックして表示できるほか、 **[設定]** から表示することもできます。

1. 開いているアカウントの **[設定]** ボタンをクリックします。 

    :::image type="content" source="./media/invite-users/settings.png" alt-text="アカウント設定":::  
1. **[ロールの管理]** ボタンをクリックします。
1. 他のユーザーを招待するには、 **[Invite more people to this account]\(このアカウントに他のユーザーを招待する\)** をクリックします。

    :::image type="content" source="./media/invite-users/invite-more-people.png" alt-text="[その他のユーザーの招待]":::  
1. **[invite more people to this account]\(このアカウントに他のユーザーを招待する\)** をクリックすると、招待ダイアログが表示されます。
 
    :::image type="content" source="./media/invite-users/share-account-others.png" alt-text="このアカウントにユーザーを招待する":::  

## <a name="next-steps"></a>次のステップ

これで、[Video Indexer Web サイト](video-indexer-view-edit.md)または [Video Indexer 開発者ポータル](video-indexer-use-apis.md)を使用してビデオの分析情報を表示できます。

## <a name="see-also"></a>関連項目

- [Video Indexer の概要](video-indexer-overview.md)
- [サインアップして最初のビデオをアップロードする方法](video-indexer-get-started.md)
- [API の使用開始](video-indexer-use-apis.md)
