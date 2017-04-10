---
title: 'Troubleshooting: ''Active Directory'' item is missing or not available | Microsoft Docs'
description: "Microsoft Azure 管理ポータルに Active Directory メニュー項目が表示されないときの対処方法。"
services: active-directory
documentationcenter: na
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 3383020d-6397-43ea-b7aa-c6a9d6a1e3df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/07/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9c52e9257272c5b0c55c488d2c7ca6fef500260b


---
# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>トラブルシューティング: Active Directory 項目が不足しているか使用できない
Azure Active Directory の機能とサービスを使用するための手順の多くが、"Microsoft Azure 管理ポータルに移動し、**[Active Directory]** をクリックします。" という指示で始まります。 Active Directory 拡張機能またはメニュー項目が表示されない、または **[使用不可]** とマークされている場合はどうすればよいのでしょうか。 このトピックを役立ててください。 **Active Directory** が表示されない状況と使用できない状況について説明し、どのように対応するかについて説明しています。

## <a name="active-directory-is-missing"></a>Active Directory が見つからない
通常、 **Active Directory** 項目は、左側のナビゲーション メニューの中に表示されます。 Azure Active Directory に関する手順は、この項目が表示されていることを前提とします。

![画面ショット: Azure 内の Active Directory](./media/active-directory-troubleshooting/typical-view.png)

Active Directory 項目は、次のいずれかの条件が満たされている場合に左側のナビゲーション メニューの中に表示されます。 条件に該当しない場合、項目は表示されません。

* 現在のユーザーが Microsoft アカウント (旧 Windows Live ID) でサインインした。
  
    または
* Azure テナントがディレクトリを持っており、現在のアカウントがディレクトリ管理者である。
  
    または
* Azure テナントが少なくとも 1 つの Azure AD Access Control (ACS) 名前空間を持っている。 詳細については、「 [Access Control 名前空間](https://msdn.microsoft.com/library/azure/gg185908.aspx)」を参照してください。
  
    または
* Azure テナントが少なくとも 1 つの Azure Multi-factor Authentication プロバイダーを持っている。 詳細については、「 [Administering Azure Multi-Factor Authentication Providers (Azure Multi-Factor Authentication プロバイダーの管理)](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)」を参照してください。

Access Control 名前空間または Multi-factor Authentication プロバイダーを作成するには、**[+新規]** > **[App Services]** > **[Active Directory]** の順にクリックします。

ディレクトリに対する管理権限を取得するには、使用中のアカウントに管理者ロールを割り当てるように管理者に依頼してください。 詳細については、「 [管理者ロールの割り当て](active-directory-assign-admin-roles.md)」を参照してください。

## <a name="active-directory-is-not-available"></a>Active Directory を使用できない
**[+新規]** > **[App Services]** の順にクリックすると、**[Active Directory]** 項目が表示されます。 具体的に言うと、Active Directory 項目は、現在のユーザーがディレクトリ、Access Control、多要素認証プロバイダーなどの Active Directory の機能のいずれかを利用できる場合に表示されます。

ただし、ページの読み込み中は、項目は淡色表示され、 **[使用不可]**とマークされます。 これは一時的な状態です。 数秒待てば、項目は使用可能になります。 待ち時間が長いときは、Web ページを更新すれば、多くの場合、問題が解決します。

![画面ショット: Active Directory を使用できない](./media/active-directory-troubleshooting/not-available.png)




<!--HONumber=Nov16_HO3-->


