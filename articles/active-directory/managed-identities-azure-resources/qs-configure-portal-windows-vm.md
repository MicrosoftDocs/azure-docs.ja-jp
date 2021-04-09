---
title: Azure portal を使用してマネージド ID を構成する - Azure AD
description: Azure portal を使用して、Azure VM で Azure リソースのマネージド ID を構成するための詳細な手順について説明します。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78c973537e958b7199c137c81e72091f70b84ddb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590989"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Azure portal を使用して Azure VM で Azure リソースのマネージド ID を構成する

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure portal を使用して、システムとユーザーによって Azure 仮想マシン (VM) に割り当てられたマネージド ID を有効および無効にする方法について説明します。 

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

このセクションでは、Azure portal を使用して VM のシステム割り当てマネージド ID を有効および無効にする方法について説明します。

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>VM の作成中にシステム割り当てマネージド ID を有効にする

VM の作成中に VM でシステム割り当てマネージド ID を有効にするには、アカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールの割り当てが必要です。  Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

- **[管理]** タブの **[ID]** セクションで、 **[マネージド サービス ID]** を **[オン]** に切り替えます。  

![VM の作成中にシステム割り当て ID を有効にする](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

VM を作成するには、次のクイック スタートを参照してください。 

- [Azure Portal で Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Azure Portal で Linux 仮想マシンを作成する](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>既存の VM でシステム割り当てマネージド ID を有効にする

もともとシステム割り当てマネージド ID をプロビジョニングされていなかった VM でシステム割り当てマネージド ID を有効にするには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールの割り当てが必要です。  Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。

2. 目的の仮想マシンに移動して **[ID]** を選択します。

3. **[システム割り当て済み]** にある **[状態]** で **[オン]** を選択して、 **[保存]** をクリックします。

   ![[ID (プレビュー)] ページのスクリーンショット。[システム割り当て済み] 状態が [オン] に設定されている。](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>VM からシステム割り当てマネージド ID を削除する

VM からシステム割り当てマネージド ID を削除するには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールの割り当てが必要です。  Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

仮想マシンでシステム割り当てマネージド ID が不要になった場合:

1. VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。 

2. 目的の仮想マシンに移動して **[ID]** を選択します。

3. **[システム割り当て済み]** にある **[状態]** で **[オフ]** を選択して、 **[保存]** をクリックします。

   ![[構成] ページのスクリーンショット](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

 このセクションでは、Azure portal を使用して、VM との間でユーザー割り当てマネージド ID を追加および削除する方法について説明します。

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>VM の作成中にユーザー割り当て ID を割り当てる

ユーザー割り当て ID を VM に割り当てるには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールと[マネージド ID オペレーター](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ロールの割り当てが必要です。 Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

現在 Azure portal では、VM 作成中のユーザー割り当てマネージド ID の割り当てはサポートされていません。 代わりに、VM の作成に関する次のいずれかのクイック スタート記事を参照して、まず VM を作成してください。そして、その VM にユーザー割り当てマネージド ID を割り当てる方法について詳しく説明している、次のセクションに進んでください。

- [Azure Portal で Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Azure Portal で Linux 仮想マシンを作成する](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>ユーザー割り当てマネージド ID を既存の VM に割り当てる

ユーザー割り当て ID を VM に割り当てるには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールと[マネージド ID オペレーター](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ロールの割り当てが必要です。 Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。
2. 目的の VM に移動して、 **[ID]** 、 **[ユーザー割り当て済み]** 、 **\+[追加]** の順にクリックします。

   ![[ID] ページのスクリーンショット。[ユーザー割り当て済み] が選択され、[追加] ボタンが強調表示されている。](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. VM に追加したいユーザー割り当て ID をクリックして、 **[追加]** をクリックします。

    ![VM へのユーザー割り当てマネージド ID の追加](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>VM からユーザー割り当てマネージド ID を削除する

VM からユーザー割り当て ID を削除するには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールの割り当てが必要です。 Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。
2. 目的の VM に移動します。次に、 **[ID]** 、 **[ユーザー割り当て済み]** 、削除したいユーザー割り当てマネージド ID の名前を順にクリックしてから、 **[削除]** をクリックします (確認ウィンドウで **[はい]** をクリックします)。

   ![VM からのユーザー割り当てマネージド ID の削除](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>次のステップ

- Azure portal を使用して、Azure VM のマネージド ID に[別の Azure リソースへのアクセス](howto-assign-access-portal.md)を許可します。