---
title: "Microsoft Azure Multi-Factor Authentication におけるユーザーの状態"
description: "Azure MFA におけるユーザーの状態について説明します。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 999361daa2faebe3e88cab0b6085a938d6f40e9d
ms.openlocfilehash: 3079969783f589cb90f17eac116c5d57884db49f
ms.lasthandoff: 02/17/2017


---
# <a name="user-states-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication におけるユーザーの状態
Azure Multi-factor Authentication のユーザー アカウントには、次の&3; つの異なる状態があります。

| 状態 | 説明 | 非ブラウザー アプリに影響があるか | 
|:---:|:---:|:---:|
| 無効 |新しいユーザーの既定の状態は、Azure Multi-Factor Authentication (MFA) に登録されていません。 |なし |
| 有効 |ユーザーは Azure MFA にサインインできますが、登録されていません。 次回のサインイン時に登録することを求められます。 |いいえ。  これらは登録プロセスが完了するまで機能し続けます。 |
| 適用 |ユーザーは、Azure MFA にサインインして Azure MFA に対する登録プロセスを完了しています。 |はい。  アプリはアプリ パスワードを必要とします。 |

## <a name="changing-a-user-state"></a>ユーザーの状態の変化
ユーザーの状態は、管理者がユーザーをAzure MFA に登録し、ユーザーが登録プロセスを完了したかどうかを反映します。

すべてのユーザーの状態は、*[無効]* から始まります。 管理者がユーザーを Azure MFA に登録すると、ユーザーの状態は *[有効]* に変わります。 [有効] 状態のときにユーザーがサインインして登録プロセスを完了すると、ユーザーの状態は *[適用]* に変わります。  

### <a name="view-user-states"></a>ユーザーの状態を表示する

ユーザーの状態を表示および管理できるページにアクセスするには、次の手順に従います。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)に管理者としてサインインします。
2. 左側で、 **[Active Directory]**を選択します。
3. 表示するユーザーのディレクトリを選択します。
   ![ディレクトリの選択 - スクリーンショット](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. **[ユーザー]** を選択します。
5. ページ下部の **[Multi-Factor Authentication の管理]** をクリックします。 
   ![[Multi-Factor Authentication の管理] の選択 - スクリーンショット](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. 新しいタブが開き、ユーザーの状態が表示されます。
   ![多要素認証のユーザーの状態 - スクリーンショット](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-state-from-disabled-to-enabled"></a>ユーザーの状態を [無効] から [有効] に変更するには

1. 前述の手順を使用して、Multi-Factor Authentication のユーザー ページを取得します。 
2. Azure MFA で有効にするユーザーを見つけます。 上部でビューを変更することが必要になる場合があります。 状態が **[無効]** であることを確認します。
   ![ユーザーを見つける - スクリーンショット](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. ユーザーの名前の横にあるチェック ボックスをオンにします。
4. 右側にある [クイック操作] の下の **[有効にする]** をクリックします。
   ![選択したユーザーを有効にする - スクリーンショット](./media/multi-factor-authentication-get-started-cloud/user1.png)
5. **[多要素認証を有効にする]** をクリックします。
   ![多要素認証を有効にする - スクリーンショット](./media/multi-factor-authentication-get-started-cloud/enable2.png)
6. ユーザーの状態が **[無効]** から **[有効]** に変わります。
   ![ユーザーが有効になっていることの確認 - スクリーンショット](./media/multi-factor-authentication-get-started-cloud/user.png)

有効にした後は、ユーザーにメールで通知する必要があります。 次回のサインイン時に登録を求められること、および一部の非ブラウザー アプリでは&2; 段階認証は機能しない場合があるという内容を含めます。 ユーザーが参照できるように、[Azure MFA エンドユーザー ガイド](./end-user/multi-factor-authentication-end-user.md)に関するページのリンクを含めることもできます。 

### <a name="to-change-the-state-from-enabledenforced-to-disabled"></a>ユーザーの状態を有効/強制から無効に変更するには

1. 「[ユーザーの状態を表示する](#view-user-states)」の手順を使用して、Multi-Factor Authentication のユーザー ページを取得します。
6. 無効にするユーザーを見つけます。 上部でビューを変更することが必要になる場合があります。 状態が **[有効]** または **[適用]** であることを確認します。
7. ユーザーの名前の横にあるチェック ボックスをオンにします。
8. 右側にある [クイック操作] の下の **[無効にする]** をクリックします。
   ![ユーザーを無効にする - スクリーンショット](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
9. 操作の確認を求めるメッセージが表示されます。 **[はい]**をクリックします。
10. ユーザーが正常に無効になった場合、成功メッセージが表示されます。 **[閉じる]**をクリックします。


