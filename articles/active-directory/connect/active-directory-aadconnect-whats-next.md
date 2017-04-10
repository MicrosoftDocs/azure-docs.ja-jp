---
title: "Azure AD Connect: 次のステップと Azure AD Connect の管理方法 | Microsoft Docs"
description: "Azure AD Connect の既定の構成と運用タスクを拡張する方法について説明します。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: dde6242211ca3d8d7b47c0ec4a01965e1439adeb
ms.openlocfilehash: d3b900b40f683cd446b41696c4f9e617299bf0b1
ms.lasthandoff: 02/22/2017


---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>次のステップと Azure AD Connect の管理方法
この記事の操作手順を使用して、組織のニーズや要件を満たすように Azure Active Directory (Azure AD) Connect をカスタマイズします。  

## <a name="add-additional-sync-admins"></a>その他の同期管理者の追加
既定では、インストールを行ったユーザーとローカルの管理者のみが、インストールされている同期エンジンを管理できます。 同期エンジンにアクセスして管理できるその他のユーザーを追加するには、ローカル サーバーで ADSyncAdmins というグループを見つけ、そのグループにユーザーを追加します。

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Azure AD Premium および Enterprise Mobility Suite のユーザーにライセンスを割り当てる
ユーザーがクラウドに同期されたため、Office 365 などのクラウド アプリを利用できるように、そのユーザーにライセンスを割り当てる必要があります。

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Azure AD Premium または Enterprise Mobility Suite のライセンスを割り当てるには

1. Azure Portal に管理者としてサインインします。
2. 左側で、 **[Active Directory]**を選択します。
3. **[Active Directory]** ページで、設定するユーザーが存在するディレクトリをダブルクリックします。
4. ディレクトリ ページの上部にある **[ライセンス]**を選択します。
5. **[ライセンス]** ページで、**[Active Directory Premium]** または **[Enterprise Mobility Suite]** を選択し、**[割り当て]** をクリックします。
6. ダイアログ ボックスで、ライセンスを割り当てるユーザーを選択し、チェック マーク アイコンをクリックして変更を保存します。

## <a name="verify-the-scheduled-synchronization-task"></a>スケジュールされた同期タスクを確認する
Azure Portal を使用して、同期の状態を確認します。

### <a name="to-verify-the-scheduled-synchronization-task"></a>スケジュールされた同期タスクを確認するには
1. Azure Portal に管理者としてサインインします。
2. 左側で、 **[Active Directory]**を選択します。
3. **[Active Directory]** ページで、設定するユーザーが存在するディレクトリをダブルクリックします。
4. ディレクトリ ページの上部にある **[ディレクトリ統合]**を選択します。
5. **[ローカル Active Directory との統合]** で、最後の同期時刻を確認します。

<center>![ディレクトリ同期時刻](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="start-a-scheduled-synchronization-task"></a>スケジュールされた同期タスクを開始する
同期タスクを実行する必要がある場合は、Azure AD Connect ウィザードをもう一度実行して行うことができます。  Azure AD 資格情報を入力する必要があります。  ウィザードで、**[同期オプションのカスタマイズ]** タスクを選択し、**[次へ]** をクリックして移動します。 最後に、**[初期構成が完了したら、同期処理を開始してください。]** チェック ボックスがオンになっていることをご確認ください。

<center>![同期の開始](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Azure AD Connect 同期スケジューラについて詳しくは、[Azure AD Connect スケジューラ](active-directory-aadconnectsync-feature-scheduler.md)に関する記事をご覧ください。

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Azure AD Connect で利用可能なその他のタスク
Azure AD Connect の初回インストール後は、Azure AD Connect のスタート ページまたはデスクトップのショートカットから、いつでもウィザードを再び起動できます。  もう一度ウィザードを実行すると、追加のタスクの形式で新しいオプションがいくつか表示されます。  

次の表に、これらのタスクの概要と各タスクの簡単な説明を示します。

![追加のタスクの一覧](./media/active-directory-aadconnect-whats-next/addtasks.png)

| 追加のタスク | 説明 |
| --- | --- |
| **選択したシナリオを表示する** |現在の Azure AD Connect のソリューションを表示します。  これには、全般設定、同期されたディレクトリ、同期設定が含まれます。 |
| **同期オプションをカスタマイズする** |構成への Active Directory フォレストの追加、ユーザー、グループ、デバイス、パスワード ライトバックなどの同期オプションの有効化など、現在の構成を変更します。 |
| **ステージング モードを有効にする** |すぐには同期されず、Azure AD やオンプレミスの Active Directory にエクスポートされない情報をステージングします。  この機能では、事前に同期のプレビューを表示できます。 |

## <a name="next-steps"></a>次のステップ
[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)に関する記事をご覧ください。

