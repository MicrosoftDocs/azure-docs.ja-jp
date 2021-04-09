---
title: 'Azure AD Connect: 次のステップと Azure AD Connect の管理方法 | Microsoft Docs'
description: Azure AD Connect の既定の構成と運用タスクを拡張する方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d6689b4b67b8462e983ae9b111e0fbc60c422b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89657117"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>次のステップと Azure AD Connect の管理方法
この記事の操作手順を使用して、組織のニーズや要件を満たすように Azure Active Directory (Azure AD) Connect をカスタマイズします。  

## <a name="add-additional-sync-admins"></a>その他の同期管理者の追加
既定では、インストールを行ったユーザーとローカルの管理者のみが、インストールされている同期エンジンを管理できます。 同期エンジンにアクセスして管理できるその他のユーザーを追加するには、ローカル サーバーで ADSyncAdmins というグループを見つけ、そのグループにユーザーを追加します。

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Azure AD Premium および Enterprise Mobility Suite のユーザーにライセンスを割り当てる
ユーザーがクラウドに同期されたため、Microsoft 365 などのクラウド アプリを利用できるように、そのユーザーにライセンスを割り当てる必要があります。

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Azure AD Premium または Enterprise Mobility Suite のライセンスを割り当てるには

1. Azure Portal に管理者としてサインインします。
2. 左側で、 **[Active Directory]** を選択します。
3. **[Active Directory]** ページで、設定するユーザーが存在するディレクトリをダブルクリックします。
4. ディレクトリ ページの上部にある **[ライセンス]** を選択します。
5. **[ライセンス]** ページで、 **[Active Directory Premium]** または **[Enterprise Mobility Suite]** を選択し、 **[割り当て]** をクリックします。
6. ダイアログ ボックスで、ライセンスを割り当てるユーザーを選択し、チェック マーク アイコンをクリックして変更を保存します。

## <a name="verify-the-scheduled-synchronization-task"></a>スケジュールされた同期タスクを確認する
Azure Portal を使用して、同期の状態を確認します。

### <a name="to-verify-the-scheduled-synchronization-task"></a>スケジュールされた同期タスクを確認するには
1. Azure Portal に管理者としてサインインします。
2. 左側で、 **[Active Directory]** を選択します。
3. 左側で **[Azure AD Connect]** を選びます。
4. ページの上部にある最後の同期に注意してください。

![ディレクトリ同期時刻](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>スケジュールされた同期タスクを開始する
同期タスクを実行する必要がある場合、以下により実行できます。

1. Azure AD Connect デスクトップのショートカットをダブルクリックしてウィザードを開始します。
2. **[構成]** をクリックします。
3. タスク画面で **[同期オプションのカスタマイズ]** を選択し、 **[次へ]** をクリックします。
4. Azure ADの資格情報を入力します。
5. **[次へ]** をクリックします。 **[次へ]** をクリックします。  **[次へ]** をクリックします。
5.  **[構成の準備完了]** 画面で、 **[構成が完了したら、同期プロセスを開始してください]** チェック ボックスがオンになっていることを確認します。
6.  **[構成]** をクリックします。

Azure AD Connect 同期スケジューラについて詳しくは、[Azure AD Connect スケジューラ](how-to-connect-sync-feature-scheduler.md)に関する記事をご覧ください。

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Azure AD Connect で利用可能なその他のタスク
Azure AD Connect の初回インストール後は、Azure AD Connect のスタート ページまたはデスクトップのショートカットから、いつでもウィザードを再び起動できます。  もう一度ウィザードを実行すると、追加のタスクの形式で新しいオプションがいくつか表示されます。  

次の表に、これらのタスクの概要と各タスクの簡単な説明を示します。

![追加のタスクの一覧](./media/how-to-connect-post-installation/addtasks2.png)

| 追加のタスク | 説明 |
| --- | --- |
|**プライバシー設定**|Microsoft と共有している利用統計情報データを表示します。|
|**現在の構成を表示する**|現在の Azure AD Connect のソリューションを表示します。  これには、全般設定、同期されたディレクトリ、同期設定が含まれます。 |
| **同期オプションをカスタマイズする** |構成への Active Directory フォレストの追加、ユーザー、グループ、デバイス、パスワード ライトバックなどの同期オプションの有効化など、現在の構成を変更します。 |
|**デバイス オプションの構成**|同期に使用できるデバイス オプション|
|**ディレクトリ スキーマの更新**|同期のための新しいオンプレミス ディレクトリ オブジェクトを追加することができます。|
|**ステージング モードの構成** |すぐには同期されず、Azure AD やオンプレミスの Active Directory にエクスポートされない情報をステージングします。  この機能では、事前に同期のプレビューを表示できます。 |
|**ユーザー サインインの変更**|ユーザーがサインインを使用している認証方法を変更します。|
|**フェデレーションの管理**|AD FS インフラストラクチャの管理、証明書の更新および AD FS サーバーの追加|
|**[トラブルシューティング]**|Azure AD Connect の問題のトラブルシューティングに役立ちます。|

## <a name="next-steps"></a>次のステップ
[オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)に関する記事をご覧ください。
