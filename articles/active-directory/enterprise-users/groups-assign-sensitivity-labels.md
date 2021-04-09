---
title: グループに秘密度ラベルを割り当てる - Azure AD | Microsoft Docs
description: 秘密度ラベルをグループに割り当てる方法について説明します。 トラブルシューティングの情報を参照し、使用可能な他のリソースを確認してください。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a78cf9b7d78078030ac0db8bd2f0fddb93a8dda4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97881398"
---
# <a name="assign-sensitivity-labels-to-microsoft-365-groups-in-azure-active-directory"></a>Azure Active Directory で Microsoft 365 グループに秘密度ラベルを割り当てる

Azure Active Directory (Azure AD) では、[Microsoft 365 コンプライアンス センター](https://sip.protection.office.com/homepage)によって公開されている秘密度ラベルを Microsoft 365 グループに適用することがサポートされています。 秘密度ラベルは、Outlook、Microsoft Teams、SharePoint などのサービス全体で、グループに対して適用されます。 Microsoft 365 アプリのサポートの詳細については、[Microsoft 365 での秘密度ラベルのサポート](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels)に関する記事を参照してください。

> [!IMPORTANT]
> この機能を構成するには、Azure AD 組織にアクティブな Azure Active Directory Premium P1 ライセンスが少なくとも 1 つ必要です。

## <a name="enable-sensitivity-label-support-in-powershell"></a>PowerShell で秘密度ラベルのサポートを有効にする

公開されているラベルをグループに適用するには、まずこの機能を有効にする必要があります。 次の手順を実行すると、Azure AD でこの機能が有効になります。

1. コンピューターで Windows PowerShell ウィンドウを開きます。 これは昇格された特権がなくても開くことができます。
1. 次のコマンドを実行して、コマンドレットを実行する準備をします。

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    **[アカウントにサインインする]** ページで、管理者アカウントとパスワードを入力してサービスに接続し、 **[サインイン]** を選択します。
1. Azure AD 組織の現在のグループ設定を取得します。

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > この Azure AD 組織のグループ設定が作成されていない場合、上記のコマンドレットで、"引数が null であるため、パラメーター 'Id' にバインドできません" というエラーが表示されます。 この場合、まず設定を作成する必要があります。 「[グループの設定を構成するための Azure Active Directory コマンドレット](../enterprise-users/groups-settings-cmdlets.md)」の手順に従って、この Azure AD 組織のグループ設定を作成します。

1. 次に、現在のグループ設定を表示します。

    ```PowerShell
    $Setting.Values
    ```

1. 次に、この機能を有効にします。

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. 次に、変更を保存し、設定を適用します。

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

また、秘密度ラベルを Azure AD に同期する必要があります。 手順については、「[コンテナーの秘密度ラベルを有効化してラベルを同期する方法](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#how-to-enable-sensitivity-labels-for-containers-and-synchronize-labels)」を参照してください。

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Azure portal で新しいグループにラベルを割り当てる

1. [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[グループ]** を選択し、 **[新しいグループ]** を選択します。
1. **[新しいグループ]** ページで、 **[Office 365]** を選択し、新しいグループに必要な情報を入力して、一覧から秘密度ラベルを選択します。

   ![[新しいグループ] ページで秘密度ラベルを割り当てる](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. 変更を保存し、 **[作成]** を選択します。

グループが作成され、選択したラベルに関連付けられているサイトおよびグループの設定が自動的に適用されます。

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Azure portal で既存のグループにラベルを割り当てる

1. グループ管理者のアカウントを使用するか、またはグループの所有者として [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[グループ]** を選びます。
1. **[すべてのグループ]** ページから、ラベルを適用するグループを選択します。
1. 選択したグループのページで **[プロパティ]** を選択し、一覧から秘密度ラベルを選択します。

   ![グループの [概要] ページで秘密度ラベルを割り当てる](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. **[保存]** を選択して変更を保存します。

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Azure portal で既存のグループからラベルを削除する

1. グローバル管理者かグループ管理者のアカウントを使用するか、またはグループの所有者として [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[グループ]** を選びます。
1. **[すべてのグループ]** ページから、ラベルを削除するグループを選択します。
1. その **[グループ]** ページで、 **[プロパティ]** を選択します。
1. **[削除]** を選択します。
1. **[保存]** を選択して変更を保存します。

## <a name="using-classic-azure-ad-classifications"></a>従来の Azure AD 分類を使用する

この機能を有効にすると、グループの "従来の" 分類が、既存のグループとサイトについてのみ表示され、秘密度ラベルをサポートしていないアプリでグループを作成する場合にのみ、これらを新しいグループに使用する必要があります。 管理者は必要に応じて、これらを後で秘密度ラベルに変換できます。 従来の分類とは、Azure AD PowerShell で `ClassificationList` 設定の値を定義することによって設定した以前の分類のことです。 この機能を有効にすると、それらの分類はグループに適用されなくなります。

## <a name="troubleshooting-issues"></a>問題のトラブルシューティング

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>グループの割り当てに秘密度ラベルを使用できない

[秘密度ラベル] オプションは、次のすべての条件が満たされている場合にのみ、グループに対して表示されます。

1. ラベルは、この Azure AD 組織の Microsoft 365 コンプライアンス センターで公開されています。
1. 機能が有効になっており、Azure AD PowerShell モジュールで EnableMIPLabels が True に設定されています。
1. ラベルは、Security & Compliance PowerShell モジュールの Execute-AzureAdLabelSync コマンドレットで、Azure AD に同期されます。
1. グループは Microsoft 365 グループです。
1. 組織に、アクティブな Azure Active Directory Premium P1 ライセンスがある。
1. 現在サインインしているユーザーに、ラベルを割り当てるための十分な権限がある。 そのユーザーは、全体管理者、グループ管理者、またはグループの所有者のいずれかである必要があります。

ラベルをグループに割り当てるには、上記のすべての条件が満たされていることを確認してください。

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>割り当てたいラベルが一覧にありません

探しているラベルが一覧にない場合は、次のいずれかの理由が考えられます。

- そのラベルが、Microsoft 365 コンプライアンス センターで公開されていない。 これは、公開されなくなったラベルにも当てはまります。 詳細については、管理者にご確認ください。
- ラベルは公開されているが、サインインしているユーザーにはそのラベルを使用できない。 ラベルにアクセスする方法の詳細については、管理者にご確認ください。

### <a name="how-to-change-the-label-on-a-group"></a>グループに対するラベルを変更する方法

ラベルは、次に示すように、既存のグループに割り当てるのと同じ手順を使用することによりいつでも交換できます。

1. グローバル管理者かグループ管理者のアカウントを使用するか、またはグループの所有者として [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[グループ]** を選びます。
1. **[すべてのグループ]** ページから、ラベルを適用するグループを選択します。
1. 選択したグループのページで **[プロパティ]** を選択し、一覧から新しい秘密度ラベルを選択します。
1. **[保存]** を選択します。

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>公開されているラベルに加えたグループ設定変更が、グループに対して更新されない

ベスト プラクティスとして、ラベルをグループに適用した後で、そのラベルのグループ設定を変更することはお勧めしません。 公開されたラベルに関連付けられているグループ設定を [Microsoft 365 コンプライアンス センター](https://sip.protection.office.com/homepage)で変更しても、それらのポリシー変更は影響を受けるグループに自動的には適用されません。

変更が必要な場合は、[Azure AD PowerShell スクリプト](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)を使用して、影響を受けるグループに更新を手動で適用します。 この方法を使用すると、既存のすべてのグループに新しい設定が適用されます。

## <a name="next-steps"></a>次のステップ

- [Microsoft Teams、Microsoft 365 グループ、SharePoint サイトで秘密度ラベルを使用する](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Azure AD PowerShell スクリプトを使用して、ラベルのポリシーを手動で変更した後にグループを更新する](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [グループの設定を編集する](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [PowerShell コマンドを使用してグループを管理する](../enterprise-users/groups-settings-v2-cmdlets.md)
