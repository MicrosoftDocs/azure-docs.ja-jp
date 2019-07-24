---
title: Windows Virtual Desktop プレビューでテナントを作成する - Azure
description: Windows Virtual Desktop プレビューのテナントを Azure Active Directory に設定する方法を説明します。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 1c66b3de9e18cb74c43f20499e4065c7ec7ae5ca
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58801681"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>チュートリアル:Windows Virtual Desktop プレビューでテナントを作成する

Windows Virtual Desktop プレビューでのテナントの作成は、デスクトップ仮想ソリューションを構築するうえでの最初の手順です。 テナントは、1 つまたは複数のホスト プールのグループです。 各ホスト プールは複数のセッション ホストで構成されていて、Azure において仮想マシンとして動作し、Windows Virtual Desktop サービスに登録されます。 各ホスト プールはさらに、リモート デスクトップおよびリモート アプリケーションのリソースをユーザーに発行するために使用される、1 つ以上のアプリ グループで構成されます。 テナントを使用すると、ホスト プールの構築、アプリ グループの作成、ユーザーの割り合て、サービスを通じた接続の作成を行えます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Active Directory のアクセス許可を Windows Virtual Desktop サービスに付与する。
> * Azure Active Directory テナント内のユーザーに TenantCreator アプリケーション ロールを割り当てる。
> * Windows Virtual Desktop テナントを作成する。

以下に、Windows Virtual Desktop テナントの設定に必要なものを示します。

* Windows Virtual Desktop ユーザー用の [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) テナント ID。
* Azure Active Directory テナント内のグローバル管理者アカウント。
   * これは、クラウド ソリューション プロバイダー (CSP) 組織がその顧客に Windows Virtual Desktop テナントを作成する場合にも当てはまります。 CSP 組織の場合は、顧客の Azure Active Directory のグローバル管理者としてサインインできる必要があります。
   * 管理者アカウントは、Windows Virtual Desktop テナントを作成する Azure Active Directory テナントから提供する必要があります。 このプロセスは、Azure Active Directory B2B (ゲスト) アカウントをサポートしません。
   * 管理者アカウントは、職場または学校アカウントである必要があります。
* Azure サブスクリプション ID

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>Azure Active Directory のアクセス許可を Windows Virtual Desktop プレビュー サービスに付与する

この Azure Active Directory のアクセス許可を Windows Virtual Desktop に既に付与してある場合、このセクションはスキップしてください。

Windows Virtual Desktop サービスにアクセス許可を付与すると、Azure Active Directory に対して、管理タスクおよびエンドユーザー タスクに関するクエリを実行できるようになります。

サービスにアクセス許可を付与するには:

1. ブラウザーを開いて、[Windows Virtual Desktop の同意ページ](https://rdweb.wvd.microsoft.com)にアクセスします。
2. **[Consent Option]\(同意オプション\)** > **[サーバー アプリ]** で、Azure Active Directory テナント名またはディレクトリ ID を入力して、**[送信]** を選択します。
        クラウド ソリューション プロバイダーのお客様の場合、ID は、パートナー ポータルから取得したお客様の Microsoft ID です。 エンタープライズのお客様の場合、ID は、**[Azure Active Directory]** > **[プロパティ]** > **[ディレクトリ ID]** の下にあります。
3. グローバル管理者アカウントを使用して Windows Virtual Desktop の同意ページにサインインします。 たとえば、Contoso 組織に属しているとしたら、アカウントは admin@contoso.com や admin@contoso.onmicrosoft.com になるでしょう。  
4. **[Accept]\(承認\)** を選択します。
5. 1 分間待機します。
6. [Windows Virtual Desktop の同意ページ](https://rdweb.wvd.microsoft.com)に戻ります。
7. **[Consent Option]\(同意オプション\)** > **[クライアント アプリ]** に移動して、同じ Azure AD テナント名またはディレクトリ ID を入力してから、**[送信]** を選択します。
8. 前の手順 3. で行ったように、グローバル管理者として Windows Virtual Desktop の同意ページにサインインします。
9. **[Accept]\(承認\)** を選択します。

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Azure Active Directory テナント内のユーザーに TenantCreator アプリケーション ロールを割り当てる

Azure Active Directory ユーザーに TenantCreator アプリケーション ロールを割り当てると、そのユーザーは、Azure Active Directory に関連付けられた Windows Virtual Desktop テナントを作成できます。 TenantCreator ロールを割り当てるには、グローバル管理者アカウントを使用する必要があります。

グローバル管理者アカウントを使用して TenantCreator アプリケーション ロールを割り当てるには:

1. ブラウザーを開き、グローバル管理者アカウントを使用して [Azure Active Directory ポータル](https://aad.portal.azure.com)に接続します。
   - 複数の Azure AD テナントを操作している場合、プライベート ブラウザー セッションを開き、URL をアドレスにコピーして貼り付けるのがベスト プラクティスです。
2. **[エンタープライズ アプリケーション]** を選択して、「**Windows Virtual Desktop**」を検索します。 前のセクションで同意した 2 つのアプリケーションが表示されます。 これらの 2 つのアプリについて、**[Windows Virtual Desktop]** を選択します。
3. **[ユーザーとグループ]**、**[ユーザーの追加]** の順に選択します。
4. **[割り当ての追加]** ブレードで [ユーザーとグループ] を選択します。
5. 自分の Windows Virtual Desktop テナントを作成するユーザー アカウントを検索します。
   - わかりやすいように、これはグローバル管理者アカウントにできます。
6. ユーザー アカウントを選択し、**[選択]** ボタンをクリックしてから、**[割り当て]** を選択します。

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Windows Virtual Desktop プレビュー テナントを作成する

Azure Active Directory に対してクエリを実行するアクセス許可を Windows Virtual Desktop サービスに付与し、TenantCreator ロールをユーザー アカウントに割り当てたところで、Windows Virtual Desktop テナントを作成できます。

まず、まだ行っていない場合は、PowerShell セッションで使用する [Windows Virtual Desktop モジュールをダウンロードしてインポートします](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)。

次のコマンドレットと共に TenantCreator ユーザー アカウントを使用して、Windows Virtual Desktop にサインインします。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

その後、Azure Active Directory テナントに関連付けられた新しい Windows Virtual Desktop テナントを作成します。

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

かっこで囲まれた値は、実際の組織およびテナントに関連する値に置き換える必要があります。 たとえば、お客様が Contoso 組織の Windows Virtual Desktop TenantCreator であるとします。 実行するコマンドレットは次のようになります。

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>次の手順

テナントを作成したら、ホスト プールを作成する必要があります。 ホスト プールについて詳しく確認するために、Windows Virtual Desktop でホスト プールを作成するためのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Windows Virtual Desktop のホスト プールのチュートリアル](./create-host-pools-azure-marketplace.md)
