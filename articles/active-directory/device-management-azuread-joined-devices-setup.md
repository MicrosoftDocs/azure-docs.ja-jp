---
title: "Azure Active Directory 参加済みデバイスの構成方法 | Microsoft Docs"
description: "Azure Active Directory 参加済みデバイスの構成方法について説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: 303660fa53919ec22a032931aab729fd36676b82
ms.contentlocale: ja-jp
ms.lasthandoff: 08/30/2017

---
# <a name="how-to-configure-azure-active-directory-joined-devices"></a>Azure Active Directory 参加済みデバイスの構成方法

Azure Active Directory (Azure AD) のデバイス管理を使用して、ユーザーがセキュリティとコンプライアンスの基準と一致するデバイスからリソースにアクセスしていることを保証できます。 詳しくは、「[Azure Active Directory のデバイス管理の概要](device-management-introduction.md)」を参照してください。

職場所有の Windows 10 デバイスを Azure AD の管理下に置く場合は、Azure AD 参加済みデバイスを構成します。 このトピックでは、その関連手順を示します。 


## <a name="prerequisites"></a>前提条件

Windows 10 デバイスを参加させるには、デバイスを登録できるようにデバイス登録サービスを構成する必要があります。 デバイスを Azure AD テナントに参加させるアクセス許可を持つだけでなく、構成された最大数よりも少ない数のデバイスを登録する必要があります。 詳しくは、「[デバイス設定の構成](device-management-azure-portal.md#configure-device-settings)」を参照してください。



## <a name="what-you-should-know"></a>知っておくべきこと


- Windows は、Azure AD 内の組織のディレクトリにデバイスを参加させます。

- Multi-Factor Authentication で必要な情報の入力を求められる場合があります。 この認証は、IT 管理者が構成できます。

- Azure AD によって、このデバイスをモバイル デバイス管理に登録する必要があるかどうかがチェックされ、必要に応じて登録されます。

- 管理対象ユーザーの場合は、自動サインインによりデスクトップに移動します。

- フェデレーション ユーザーの場合は、資格情報を使用してサインインする必要があります。


## <a name="joining-a-device"></a>デバイスを参加させる

このセクションでは、Windows 10 デバイスを Azure AD に参加させる手順を示します。 Azure AD にデバイスが正常に参加すると、**[職場または学校にアクセスする]** ダイアログの **[Connected to \<your Azure AD\>]\(<ユーザーの Azure AD> に接続中\)** エントリで、そのことが示されます。

![接続中](./media/device-management-azuread-joined-devices-setup/13.png)


**Windows 10 デバイスを参加させるには:**

1. **[スタート]** メニューの **[設定]** をクリックします。

    ![設定](./media/device-management-azuread-joined-devices-setup/01.png)

2. **[アカウント]** をクリックします。

    ![アカウント](./media/device-management-azuread-joined-devices-setup/02.png)


3. **[職場または学校にアクセスする]** をクリックします。

    ![職場または学校にアクセスする](./media/device-management-azuread-joined-devices-setup/03.png)

4. **[職場または学校にアクセスする]** ダイアログ ボックスで、**[接続]** をクリックします。

    ![接続](./media/device-management-azuread-joined-devices-setup/04.png)


5. **[職場または学校アカウントの設定]** ダイアログ ボックスで、**[このデバイスを Azure Active Directory に参加させる]** をクリックします。

    ![接続](./media/device-management-azuread-joined-devices-setup/08.png)


6. **[Let's get you signed in]\(サインインしましょう\)** ダイアログ ボックスで、アカウント名 (たとえば someone@example.com) を入力し、**[次へ]** をクリックします。

    ![サインインしましょう](./media/device-management-azuread-joined-devices-setup/10.png)


6. **[パスワードの入力]** ダイアログ ボックスで、パスワードを入力し、**[サインイン]** をクリックします。

    ![パスワードの入力](./media/device-management-azuread-joined-devices-setup/05.png)


7. **[これがあなたの組織のネットワークであることを確認してください]** ダイアログ ボックスで、**[参加]** をクリックします。

    ![これがあなたの組織のネットワークであることを確認してください](./media/device-management-azuread-joined-devices-setup/11.png)


8. **[準備が完了しました]** ダイアログ ボックスで、**[完了]** をクリックします。

    ![準備が完了しました](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>確認

デバイスが Azure AD に参加しているかどうかを確認するには、デバイスで **[職場または学校にアクセスする]** ダイアログを確認します。

![接続中](./media/device-management-azuread-joined-devices-setup/13.png)

代わりに、`dsregcmd /status` というコマンドを実行することもできます。  
正常に参加しているデバイスでは、**AzureAdJoined** が **Yes** になります。

![接続中](./media/device-management-azuread-joined-devices-setup/14.png)

Azure AD ポータルでデバイスの設定を確認することもできます。

![接続中](./media/device-management-azuread-joined-devices-setup/15.png)

詳細については、[デバイスの検索](device-management-azure-portal.md#locate-devices)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。 

- [Azure Active Directory のデバイス管理の概要](device-management-introduction.md)
- [Azure Portal によるデバイスの管理](device-management-azure-portal.md)
- 




