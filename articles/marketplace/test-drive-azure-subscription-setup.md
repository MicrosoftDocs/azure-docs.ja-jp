---
title: ホストされた体験版用に Azure Marketplace サブスクリプションを設定する
description: Dynamics 365 for Customer Engagement と Dynamics 365 for Operations の体験版用に Azure Marketplace サブスクリプションを設定する方法について説明します
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 11/09/2020
ms.openlocfilehash: 60eeceac916a7f8c64214b7a74a8cf60fd1ec8ac
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986126"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>ホストされた体験版用に Azure Marketplace サブスクリプションを設定する

この記事では、体験版用に Azure Marketplace サブスクリプションと **Dynamics 365 for Customer Engagement** または **Dynamics 365 for Operations** 環境を設定する方法について説明します。

## <a name="set-up-for-dynamics-365-for-customer-engagement"></a>Dynamics 365 for Customer Engagement の設定

1. 管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。
2. 右上隅のアカウント アイコンの上にカーソルを移動して、Dynamics 365 体験版インスタンスに関連付けられたテナント内にいることを確認します。 正しいテナント内でない場合は、アカウント アイコンを選択して正しいテナントに切り替えます。

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="正しいテナントの選択。":::

3. **Dynamics 365 Customer Engagement Plan** ライセンスが使用可能であることを確認します。

    [![プランのライセンスの確認。](media/test-drive/check-plan-license.png)](media/test-drive/check-plan-license.png#lightbox)

4. Azure で Azure Active Directory (AD) アプリを作成します。 AppSource では、このアプリを使用して、テナントでの体験版ユーザーのプロビジョニングとプロビジョニング解除が行われます。
    1. フィルター ペインから、 **[Azure Active Directory]** を選択します。
    2. **[アプリの登録]** を選択します。

        [![アプリの登録の選択。](media/test-drive/app-registrations.png)](media/test-drive/app-registrations.png#lightbox)

    3. **[新規登録]** を選択します。
    4. 適切なアプリケーション名を指定します。

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="アプリケーションの登録。":::

    5. [サポートされているアカウントの種類] で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント\)** を選択します。
    6. **[作成]** を選択して、アプリが作成されるまで待ちます。
    7. アプリが作成されたら、概要画面に表示される **アプリケーション ID** をメモします。 この値は、後で体験版を構成するときに必要になります。
    8. nativeclient のリダイレクト URI を追加するには、 **[認証]** ブレードを選択します。 **[プラットフォーム構成]** で、 **[プラットフォームの追加]**  >  **[モバイル]**  >  **[デスクトップ]** アプリケーション タイルを選択します。 **nativeclient** のリダイレクト URI を選択し、 **[構成]** を選択します。

        :::image type="content" source="./media/test-drive/configure-desktop-devices.png" alt-text="nativeclient のリダイレクト URI の追加。":::

    9. **[アプリケーションの管理]** で、 **[API のアクセス許可]** を選択します。
    10. **[アクセス許可の追加]** を選択し、次に **[Microsoft Graph API]** を選択します。
    11. **[アプリケーション]** アクセス許可カテゴリを選択し、次に **[Directory.Read.All]** と **[Directory.ReadWrite.All]** のアクセス許可を選択します。

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="アプリケーションのアクセス許可の設定。":::

    12. 許可リスト Azure AD アプリに対して **[Dynamics CRM - ユーザーの偽装]** アクセス権を追加するには、 **[アクセス許可の追加]** をもう一度選択します。

        :::image type="content" source="./media/test-drive/request-api-permissions.png" alt-text="アプリケーションのアクセス許可の要求。":::

    13. アクセス許可が追加されたら、 **[Grant admin consent for Microsoft]\(Microsoft に対する管理者の同意の付与\)** を選択します。
    14. メッセージ アラートから、 **[はい]** を選択します。

        [![アプリケーションのアクセス許可が正常に付与されたことを示しています。](media/test-drive/api-permissions-confirmation-customer.png)](media/test-drive/api-permissions-confirmation-customer.png#lightbox)

    15. Azure AD アプリのシークレットを作成するには、次のようにします。
        1. **[アプリケーションの管理]** から、 **[Certificate and secrets]\(証明書とシークレット\)** を選択します。
        2. [クライアント シークレット] で、 **[新しいクライアント シークレット]** を選択します。
        3. 説明 ("*体験版*" など) を入力し、適切な期間を選択します。 このキーの有効期限が切れると、体験版は中断されます。その時点で、新しいキーを生成して AppSource に提供する必要があります。
        4. **[追加]** を選択して、Azure アプリのシークレットを生成します。 このブレードを離れるとすぐに非表示になるため、この値をコピーします。 この値は、後で体験版を構成するときに必要になります。

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="クライアント シークレットの追加。":::

5. 場合によっては、ユーザーを Azure AD から CRM インスタンスに同期するのに、予想以上の時間がかかることがあります。 これを支援するために、ユーザーの同期を強制するプロセスが追加されましたが、これには Azure AD アプリケーションがパートナー センターによって許可リストに登録されている必要があります。 これを行うには、[Customer Engagement インスタンスへのユーザー同期](https://github.com/microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/CDS_Utility_to_ForceUserSync_in_CRM_Instance.md)に関する記事を参照してください。
6. アプリケーションにサービス プリンシパルの役割を追加して、Azure AD アプリで Azure テナントからユーザーを削除できるようにします。
    1. 管理レベルの PowerShell コマンド プロンプトを開きます。
    2. Install-Module MSOnline (MSOnline がインストールされていない場合、このコマンドを実行します)。
    3. Connect-MsolService (これにより、ポップアップ ウィンドウが表示されます。新しく作成された組織テナントを使用してサインインします)。
    4. $applicationId = **<YOUR_APPLICATION_ID>** 。
    5. $sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId。
    6. Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal。

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="アカウントへのサインイン。":::

7. 上で作成した Azure アプリをアプリケーション ユーザーとして体験版 CRM インスタンスに追加します。
    1. **Azure Active Directory** に新しいユーザーを追加します。 このユーザーを作成するには、(同じテナントに属している) **[名前]** と **[ユーザー名]** の値のみが必要です。他のフィールドは既定値のままにしておきます。 ユーザー名の値をコピーします。
    2. **CRM インスタンス** にサインインし、 **[設定]**  >  **[セキュリティ]**  >  **[ユーザー]** を選択します。
    3. ビューを **[アプリケーション ユーザー]** に変更します。

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="ユーザーのアカウント情報の設定。":::

    4. 新しいユーザーを追加します (フォームがアプリケーション ユーザー用であることを確認してください)。
    5. **[プライマリ電子メール]** と **[ユーザー名]** フィールドに同じユーザー名を入力します。 **[アプリケーション ID]** に「**Azure ApplicationId**」を追加します。
    6. 任意の **[フル ネーム]** を指定します。
    7. **[保存]** を選択します。
    8. **[ロールの管理]** を選択します。
    9. "*システム管理者*" など、読み取り、書き込み、およびロールの割り当ての特権を含むカスタムまたは OOB のセキュリティ ロールを割り当てます。

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="ロールの特権の選択。":::

    10. 体験版用に作成したカスタム セキュリティ ロールをアプリケーション ユーザーに割り当てます。

## <a name="set-up-for-dynamics-365-for-operations"></a>Dynamics 365 for Operations の設定

1. 管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。
2. 右上隅のアカウント アイコンの上にカーソルを移動して、Dynamics 365 体験版インスタンスに関連付けられたテナント内にいることを確認します。 正しいテナント内でない場合は、アカウント アイコンを選択して正しいテナントに切り替えます。

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="正しいテナントの選択。":::

3. Azure で Azure AD アプリを作成します。 AppSource では、このアプリを使用して、テナントでの体験版ユーザーのプロビジョニングとプロビジョニング解除が行われます。
    1. フィルター ペインから、 **[Azure Active Directory]** を選択します。
    2. **[アプリの登録]** を選択します。

        :::image type="content" source="./media/test-drive/app-registrations.png" alt-text="アプリの登録の選択。":::

    3. **[新規登録]** を選択します。
    4. 適切なアプリケーション名を指定します。

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="アプリケーションの登録。":::

    5. [サポートされているアカウントの種類] で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント\)** を選択します。
    6. **[作成]** を選択して、アプリが作成されるまで待ちます。
    7. アプリが作成されたら、概要画面に表示される **アプリケーション ID** をメモします。 この値は、後で体験版を構成するときに必要になります。
    8. **[アプリケーションの管理]** で、 **[API のアクセス許可]** を選択します。
    9. **[アクセス許可の追加]** を選択し、次に **[Microsoft Graph API]** を選択します。
    10. **[アプリケーション]** アクセス許可カテゴリを選択し、次に **[Directory.Read.All]** と **[Directory.ReadWrite.All]** のアクセス許可を選択します。

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="アプリケーションのアクセス許可の設定。":::

    11. **[アクセス許可の追加]** を選択します。
    12. アクセス許可が追加されたら、 **[Grant admin consent for Microsoft]\(Microsoft に対する管理者の同意の付与\)** を選択します。
    13. メッセージ アラートから、 **[はい]** を選択します。

        [![アプリケーションのアクセス許可が正常に付与されたことを示しています。](media/test-drive/api-permissions-confirmation-operations.png)](media/test-drive/api-permissions-confirmation-operations.png#lightbox)

    14. Azure AD アプリのシークレットを生成するには、次のようにします。
        1. **[アプリケーションの管理]** から、 **[Certificate and secrets]\(証明書とシークレット\)** を選択します。
        2. [クライアント シークレット] で、 **[新しいクライアント シークレット]** を選択します。
        3. 説明 ("*体験版*" など) を入力し、適切な期間を選択します。 このキーの有効期限が切れると、体験版は中断されます。その時点で、新しいキーを生成して AppSource に提供する必要があります。
        4. **[追加]** を選択して、Azure アプリのシークレットを生成します。 このブレードを離れるとすぐに非表示になるため、この値をコピーします。 この値は、後で体験版を構成するときに必要になります。

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="クライアント シークレットの追加。":::

4. アプリケーションにサービス プリンシパルの役割を追加して、Azure AD アプリで Azure テナントからユーザーを削除できるようにします。
    1. 管理レベルの PowerShell コマンド プロンプトを開きます。
    2. Install-Module MSOnline (MSOnline がインストールされていない場合、このコマンドを実行します)。
    3. Connect-MsolService (これにより、ポップアップ ウィンドウが表示されます。新しく作成された組織テナントを使用してサインインします)。
    4. $applicationId = **<YOUR_APPLICATION_ID>** 。
    5. $sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId。
    6. Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal。

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="アカウントへのサインイン。":::

5. 次に、上記のアプリを **Dynamics 365 for Operations** に追加して、アプリでユーザーを管理できるようにします。
    1. **Dynamics 365 for Operations** インスタンスを見つけます。
    2. 左上隅から、3 本線のメニュー (ハンバーガー) をクリックします。
    3. **[システム管理]** を選択します。
    4. **[Azure Active Directory アプリケーション]** を選択します。
    5. **[+新規]** を選択します。
    6. On-Behalf-Of アクションを実行する **Azure AD アプリのクライアント ID** を入力します。

    > [!NOTE]
    > アクションはこのユーザー ID の代わりに実行されます (通常、インスタンスのシステム管理者、または他のユーザーを追加する権限を持つユーザー)。

    [![アクションはこのユーザー ID の代わりに実行されます (通常、インスタンスのシステム管理者、または他のユーザーを追加する権限を持つユーザー)。](media/test-drive/system-admin-user-id.png)](media/test-drive/system-admin-user-id.png#lightbox)
<!--
## Next steps

- [Commercial marketplace partner and customer usage attribution](azure-partner-customer-usage-attribution.md) -->
