---
title: 管理単位を追加および削除する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で管理単位を使用してロールのアクセス許可のスコープを制限します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0706fad1e5340625c32eab691ac3e4d58eeafc9f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012117"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Azure Active Directory で管理単位を管理する

Azure Active Directory (Azure AD) では、きめ細かい管理制御を行うために、1 つ以上の管理単位に制限されたスコープでユーザーを Azure AD ロールに割り当てることができます。

## <a name="get-started"></a>はじめに

1. [Graph Explorer](https://aka.ms/ge) を使用して以降の手順からクエリを実行するには、以下を実行してください。

    a. Azure Portal で、Azure AD に移動します。 
    
    b. アプリケーションの一覧で、 **[Graph エクスプローラー]** を選択します。
    
    c. **[アクセス許可]** ウィンドウで、 **[Graph エクスプローラーに管理者の同意を与えます]** を選択します。

    ![[Graph エクスプローラーに管理者の同意を与えます] リンクを示すスクリーンショット。](./media/admin-units-manage/select-graph-explorer.png)


1. [Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/) を使用します。

## <a name="add-an-administrative-unit"></a>管理単位を追加する

管理単位は、Azure portal または PowerShell を使用して追加できます。

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

1. Azure Portal で、Azure AD に移動します。 次に、左側のウィンドウで、 **[管理単位]** を選択します。

    ![Azure AD の [管理単位] リンクのスクリーンショット。](./media/admin-units-manage/nav-to-admin-units.png)

1. ウィンドウの上部にある **[追加]** ボタンを選択し、 **[名前]** ボックスに管理単位の名前を入力します。 必要に応じて、管理単位の説明を追加します。

    ![[追加] ボタンと、管理単位の名前を入力するための [名前] ボックスを示すスクリーンショット。](./media/admin-units-manage/add-new-admin-unit.png)

1. 青い **[追加]** ボタンを選択して、管理単位を確定します。

### <a name="use-powershell"></a>PowerShell の使用

次のコマンドを実行する前に [Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/) をインストールします。

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

必要に応じて、引用符で囲まれた値を変更できます。

### <a name="use-microsoft-graph"></a>Microsoft Graph の使用

要求

```http
POST /administrativeUnits
```

本文

```http
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>管理単位を削除する

Azure AD では、管理ロールのスコープの単位として不要になった管理単位を削除できます。

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

1. Azure portal で、**Azure AD** に移動し、 **[管理単位]** を選択します。 
1. 削除する管理単位を選択してから、 **[削除]** を選択します。 
1. 管理単位の削除を確認するには、 **[はい]** を選択します。 管理単位が削除されます。

![管理単位の [削除] ボタンと確認ウィンドウのスクリーンショット。](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>PowerShell の使用

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -ObjectId $adminUnitObj.ObjectId
```

特定の環境で必要に応じて、引用符で囲まれた値を変更できます。

### <a name="use-the-graph-api"></a>Graph API の使用

要求

```http
DELETE /administrativeUnits/{admin-unit-id}
```

Body

```http
{}
```

## <a name="next-steps"></a>次のステップ

* [管理単位のユーザーを管理する](admin-units-add-manage-users.md)
* [管理単位のグループを管理する](admin-units-add-manage-groups.md)
