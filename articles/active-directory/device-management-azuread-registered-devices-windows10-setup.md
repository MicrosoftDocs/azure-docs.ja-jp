---
title: "Azure Active Directory 登録済みデバイスの設定 | Microsoft Docs"
description: "Azure Active Directory 登録済みデバイスを設定する方法について説明します。"
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
ms.date: 08/27/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 427bf9a0487c771e57ac53a9bb0b7d7e2f6bdca2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Azure Active Directory 登録済み Windows 10 デバイスの設定

Azure Active Directory (Azure AD) のデバイス管理を使用して、ユーザーがセキュリティとコンプライアンスの基準と一致するデバイスからリソースにアクセスしていることを保証できます。 詳細については、「[Azure Active Directory のデバイス管理の概要](device-management-introduction.md)」を参照してください。

**Bring Your Own Device (BYOD)** シナリオを有効にする場合は、Azure AD 登録済みデバイスを構成します。 Azure AD では、Windows 10、iOS、Android、および macOS で Azure AD 登録済みデバイスを構成できます。 このトピックでは、Windows 10 デバイスの関連手順を示します。 


## <a name="before-you-begin"></a>開始する前に

Windows 10 デバイスを登録するには、デバイスを登録できるようにデバイス登録サービスを構成する必要があります。 デバイスを Azure AD テナントに登録するアクセス許可を持つだけでなく、構成された最大数よりも少ない数のデバイスを登録する必要があります。 詳細については、「[デバイスの設定の構成](device-management-azure-portal.md#configure-device-settings)」を参照してください。

## <a name="what-you-should-know"></a>知っておくべきこと

デバイスを登録するときは、以下の点に注意してください。

- Windows では、Azure AD 内の組織のディレクトリにデバイスが登録されます。

- Multi-Factor Authentication で必要な情報の入力を求められる場合があります。 この認証は、IT 管理者が構成できます。

- Azure AD によって、このデバイスをモバイル デバイス管理に登録する必要があるかどうかがチェックされ、必要に応じて登録されます。

- 管理対象ユーザーの場合は、自動サインインによりデスクトップに移動します。

- フェデレーション ユーザーの場合は、資格情報を入力するための Windows サインイン画面が表示されます。


## <a name="registering-a-device"></a>デバイスの登録

このセクションでは、Windows 10 デバイスを Azure AD に登録する手順を示します。 Azure AD にデバイスが正常に登録されると、**[職場または学校にアクセスする]** ダイアログの **[職場または学校アカウント]** エントリで、そのことが示されます。

![登録](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**Windows 10 デバイスを登録するには:**

1. **[スタート]** メニューの **[設定]** をクリックします。

    ![設定](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. **[アカウント]** をクリックします。

    ![アカウント](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. **[職場または学校にアクセスする]** をクリックします。

    ![職場または学校にアクセスする](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. **[職場または学校にアクセスする]** ダイアログ ボックスで、**[接続]** をクリックします。

    ![接続](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. **[職場または学校アカウントの設定]** ダイアログ ボックスで、アカウント名 (例: someone@example.com) を入力し、**[次へ]** をクリックします。

    ![接続](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. **[パスワードの入力]** ダイアログ ボックスで、パスワードを入力し、**[次へ]** をクリックします。

    ![接続](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. **[準備が完了しました]** ダイアログ ボックスで、**[完了]** をクリックします。

    ![接続](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>確認

デバイスが Azure AD に参加しているかどうかを確認するには、デバイスで **[職場または学校にアクセスする]** ダイアログを確認します。

![登録](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

また、Azure AD ポータルでデバイスの設定を確認することもできます。

![登録](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>次のステップ

- 詳細については、「[Azure Active Directory のデバイス管理の概要](device-management-introduction.md)」を参照してください。

- Azure AD ポータルでのデバイス管理の詳細については、[Azure Portal によるデバイスの管理](device-management-azure-portal.md)に関するページを参照してください。




