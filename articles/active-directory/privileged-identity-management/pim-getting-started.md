---
title: PIM の使用を開始する - Azure Active Directory | Microsoft Docs
description: Azure portal で Azure AD Privileged Identity Management (PIM) を有効にして使用を開始する方法を説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 408991ffc3922986234f7d40e1cd589b1d126ba1
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476483"
---
# <a name="start-using-pim"></a>PIM の使用を開始する

組織内のアクセス権は、Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用して管理、制御、監視することができます。 その対象には、Azure リソース、Azure AD、Microsoft の各種オンライン サービス (Office 365、Microsoft Intune など) へのアクセスが含まれます。

この記事では、PIM を有効にして使用を開始する方法について説明します。

## <a name="prerequisites"></a>前提条件

PIM を使用するには、次のいずれかのライセンスが必要です。

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

詳細については、「[License requirements to use PIM (PIM を使用するためのライセンスの要件)](subscription-requirements.md)」を参照してください。

## <a name="first-person-to-use-pim"></a>PIM を使用する最初のユーザー

ディレクトリ内で PIM を使用する最初のユーザーには、そのディレクトリの[セキュリティ管理者](../users-groups-roles/directory-assign-admin-roles.md#security-administrator)ロールと[特権ロール管理者](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)ロールが自動的に割り当てられます。 ユーザーの Azure AD ロールの割り当てを管理できるのは特権ロール管理者だけです。 さらに、初回の検出と割り当てを体験できる[セキュリティ ウィザード](pim-security-wizard.md)の実行を選択することもできます。

## <a name="enable-pim"></a>PIM を有効にする

ディレクトリで PIM の使用を開始するには、まず PIM を有効にする必要があります。

1. ディレクトリのグローバル管理者として [Azure portal](https://portal.azure.com/) にサインインします。

    ディレクトリで PIM を有効にする操作は、Microsoft アカウント (例: @outlook.com) ではなく組織アカウント (例: @yourdomain.com) を持つグローバル管理者が行う必要があります。

1. **[すべてのサービス]** をクリックし、**Azure AD Privileged Identity Management** サービスを探します。

    ![[すべてのサービス] でのAzure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. PIM クイック スタートをクリックして開きます。

1. 一覧で、 **[PIM を承認する]** をクリックします。

    ![PIM に同意して PIM を有効にする](./media/pim-getting-started/consent-pim.png)

1. **[ID を確認]** をクリックし、Azure MFA で ID を確認します。 アカウントの選択が求められます。

    ![本人確認のためにアカウント ウィンドウを選択する](./media/pim-getting-started/pick-account.png)

1. 確認のためにさらに情報が必要な場合は、そのプロセスが示されます。 詳細については、「[2 段階認証のサポートを受ける](https://go.microsoft.com/fwlink/p/?LinkId=708614)」を参照してください。

    ![組織が詳細情報を必要とする場合の [詳細情報が必要] ウィンドウ](./media/pim-getting-started/more-information-required.png)

    たとえば、電話確認の提供を求められる場合があります。

    ![連絡方法を確認する追加のセキュリティ確認ページ](./media/pim-getting-started/additional-security-verification.png)

1. 確認プロセスを完了したら、 **[同意]** ボタンをクリックします。

1. 表示されたメッセージ内で、 **[はい]** をクリックして、PIM サービスに同意します。

    ![PIM メッセージに同意して同意プロセスを完了する](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Azure AD ロールを取得するために PIM にサインアップする

ディレクトリに対して PIM を有効にしたら、Azure AD ロールを管理するために PIM をサインアップする必要があります。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

    ![Azure AD ロールを取得するために PIM にサインアップする](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. **[サインアップ]** をクリックします。

1. 表示されたメッセージ内で、 **[はい]** をクリックして PIM にサインアップして、Azure AD ロールを管理します。

    ![Azure AD ロール メッセージを取得するために PIM にサインアップする](./media/pim-getting-started/sign-up-pim-message.png)

    サインアップを完了すると、Azure AD オプションが有効になります。 場合によっては、ポータルを更新する必要があります。

    PIM で保護する Azure リソースを検出して選択する方法については、「[PIM で管理する Azure リソースを検出する](pim-resource-roles-discover-resources.md)」を参照してください。

## <a name="navigate-to-your-tasks"></a>タスクへの移動

PIM を設定すると、ID 管理タスクを実行できるようになります。

![タスクと管理のオプションが表示されている PIM のナビゲーション ウィンドウ](./media/pim-getting-started/pim-quickstart-tasks.png)

| タスク + 管理 | 説明 |
| --- | --- |
| **自分のロール**  | 自分に割り当てられている適格でアクティブなロールの一覧が表示されます。 ここでは、割り当てられている適格なロールをアクティブにできます。 |
| **個人の要求** | 適格なロール割り当てのアクティブ化の保留中要求が表示されます。 |
| **申請の承認** | ディレクトリ内のユーザーによる適格なロールのアクティブ化要求のうち、自分が承認するものの一覧が表示されます。 |
| **アクセスのレビュー** | 自分に完了が割り当てられているアクティブなアクセス レビューが一覧表示されます (自分自身のアクセスをレビューするものと、他のユーザーのアクセスをレビューするものの両方)。 |
| **Azure AD ロール** | Azure AD ロールの割り当てを管理するための、特権ロール管理者向けのダッシュボードと設定が表示されます。 このダッシュボードは、特権ロール管理者以外に対しては無効になっています。 これらのユーザーは、[自分のビュー] という特殊なダッシュボードにアクセスできます。 [自分のビュー] ダッシュボードには、テナント全体ではなく、ダッシュボードにアクセスしているユーザーに関する情報のみが表示されます。 |
| **Azure リソース** | Azure リソース ロールの割り当てを管理するための、特権ロール管理者向けのダッシュボードと設定が表示されます。 このダッシュボードは、特権ロール管理者以外に対しては無効になっています。 これらのユーザーは、[自分のビュー] という特殊なダッシュボードにアクセスできます。 [自分のビュー] ダッシュボードには、テナント全体ではなく、ダッシュボードにアクセスしているユーザーに関する情報のみが表示されます。 |

## <a name="add-a-pim-tile-to-the-dashboard"></a>ダッシュボードに PIM タイルを追加する

PIM を開きやすくするには、Azure portal のダッシュボードに PIM タイルを追加する必要があります。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. **[すべてのサービス]** をクリックし、**Azure AD Privileged Identity Management** サービスを探します。

    ![[すべてのサービス] でのAzure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. PIM クイック スタートをクリックして開きます。

1. PIM クイック スタート ブレードをダッシュボードにピン留めするには、 **[ダッシュボードにブレードをピン留め]** をオンにします。

    ![ダッシュボードに PIM ブレードをピン留めするための押しピン アイコン](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Azure ダッシュボードでは、タイルが次のように表示されます。

    ![ダッシュボードの PIM クイック スタート タイル](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>次の手順

- [PIM で Azure AD ロールを割り当てる](pim-how-to-add-role-to-user.md)
- [PIM で管理する Azure リソースを検出する](pim-resource-roles-discover-resources.md)
