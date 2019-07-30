---
title: Azure Red Hat OpenShift の Azure Active Directory 統合 | Microsoft Docs
description: Microsoft Azure Red Hat OpenShift クラスターでアプリをテストするために Azure AD のセキュリティ グループとユーザーを作成する方法について説明します。
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/13/2019
ms.openlocfilehash: 00609905d09f8d414660c21805c6efca5eb30843
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669397"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift の Azure Active Directory 統合

Azure Active Directory (Azure AD) テナントをまだ作成していない場合は、これらの手順を続行する前に [Azure Red Hat OpenShift 用の Azure AD テナントの作成](howto-create-tenant.md)に関する記事の手順を行います。

Microsoft Azure Red Hat OpenShift には、クラスターに代わってタスクを実行するためのアクセス許可が必要です。 サービス プリンシパルとして使用する Azure AD ユーザー、Azure AD セキュリティ グループ、Azure AD アプリの登録が組織にまだない場合は、次の手順に従って作成します。

## <a name="create-a-new-azure-active-directory-user"></a>新しい Azure Active Directory ユーザーを作成する

[Azure portal](https://portal.azure.com) で、ポータルの右上のユーザー名の下にテナントが表示されていることを確認します。

![右上にテナントが表示されているポータルのスクリーンショット](./media/howto-create-tenant/tenant-callout.png) 不適切なテナントが表示される場合は、右上の自分のユーザー名をクリックし、 **[ディレクトリの切り替え]** をクリックして **[すべてのディレクトリ]** リストから正しいテナントを選択します。

Azure Red Hat OpenShift クラスターにサインインするために使用する新しい Azure Active Directory グローバル管理者ユーザーを作成します。

1. [[ユーザー - すべてのユーザー]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) ブレードに移動します。
2. **[+ 新しいユーザー]** をクリックし、 **[ユーザー]** ウィンドウを開きます。
3. このユーザーの **[名前]** を入力します。
4. 末尾に `.onmicrosoft.com` を追加して作成したテナントの名前に基づいて **[ユーザー名]** を作成します。 たとえば、「 `yourUserName@yourTenantName.onmicrosoft.com` 」のように入力します。 このユーザー名をメモします。 これは、クラスターにサインインするときに必要になります。
5. **[ディレクトリ ロール]** をクリックして [ディレクトリ ロール] ウィンドウを開き、 **[グローバル管理者]** を選択してから、ウィンドウの下部にある **[OK]** をクリックします。
6. **[ユーザー]** ウィンドウで **[パスワードを表示]** をクリックして一時パスワードをメモします。 初めてサインインした後は、リセットするように求められます。
7. ウィンドウの下部にある **[作成]** をクリックしてユーザーを作成します。

## <a name="create-an-azure-ad-security-group"></a>Azure AD セキュリティ グループを作成する

クラスター管理者アクセスを与える目的で、Azure AD セキュリティ グループのメンバーシップが OpenShift グループ "osa-customer-admins" に同期されます。 指定しない場合、クラスター管理者アクセスは与えられません。

1. [[Azure Active Directory グループ]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) ブレードを開きます。
2. **[+ 新しいグループ]** をクリックします。
3. グループ名と説明を指定します。
4. **[グループの種類]** を **[セキュリティ]** に設定します。
5. **[メンバーシップの種類]** を **[割り当て済み]** を選択します。

    前の手順で作成した Azure AD ユーザーをこのセキュリティ グループに追加します。

6. **[メンバー]** をクリックし、 **[メンバーの選択]** ウィンドウを開きます。
7. メンバーの一覧で、上記で作成した Azure AD ユーザーを選択します。
8. ポータルの下部で **[選択]** をクリックし、 **[作成]** をクリックしてセキュリティ グループを作成します。

    グループ ID 値を書き留めます。

9. グループが作成されると、すべてのグループの一覧に表示されます。 新しいグループをクリックします。
10. ページが表示されたら、 **[オブジェクト ID]** をコピーします。 [Azure Red Hat OpenShift クラスターの作成](tutorial-create-cluster.md)のチュートリアルでは、この値を `GROUPID` と呼びます。

## <a name="create-an-azure-ad-app-registration"></a>Azure AD アプリの登録を作成する

`az openshift create` コマンドの `--aad-client-app-id` フラグを省略することで、クラスター作成の一環として Azure Active Directory (Azure AD) アプリの登録を自動作成できます。 このチュートリアルでは、完全を期す目的で、Azure AD アプリの登録を作成する方法を示します。

サービス プリンシパルとして使用する Azure Active Directory (Azure AD) アプリの登録が組織にまだない場合は、次の手順に従って作成します。

1. [[アプリの登録]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) ブレードを開き、 **[+ 新しい登録]** をクリックします。
2. **[アプリケーションの登録]** ウィンドウで、アプリケーションの登録の名前を入力します。
3. **[サポートされているアカウントの種類]** で **[この組織のディレクトリ内のアカウントのみ]** を確実に選択します。 これは最も確実な選択肢です。
4. 後で、クラスターの URI が確認されたとき、リダイレクト URI が追加されます。 **[登録]** ボタンをクリックして Azure AD アプリケーションの登録を作成します。
5. 表示されたページで、 **[アプリケーション (クライアント) ID]** をコピーします。 [Azure Red Hat OpenShift クラスターの作成](tutorial-create-cluster.md)のチュートリアルでは、この値を `APPID` と呼びます。

![アプリ オブジェクト ページのスクリーンショット](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>クライアント シークレットの作成

Azure Active Directory に対してアプリを認証するためのクライアント シークレットを生成します。

1. アプリの登録ページの **[管理]** セクションで、 **[証明書とシークレット]** をクリックします。
2. **[証明書とシークレット]** ウィンドウで、 **[+ 新しいクライアント シークレット]** をクリックします。  **[クライアント シークレットの追加]** ウィンドウが表示されます。
3. **[説明]** を入力します。
4. **[有効期限]** を任意の期間に設定します。たとえば、 **[年 2]** にします。
5. **[追加]** をクリックします。ページの **[クライアント シークレット]** セクションにキー値が表示されます。
6. キー値をコピーします。 [Azure Red Hat OpenShift クラスターの作成](tutorial-create-cluster.md)のチュートリアルでは、この値を `SECRET` と呼びます。

![[証明書とシークレット] ウィンドウのスクリーンショット](./media/howto-create-tenant/create-key.png)

Azure アプリケーション オブジェクトの詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)」を参照してください。

新しい Azure AD アプリケーションの作成の詳細については、[Azure Active Directory v1.0 エンドポイントへのアプリの登録](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)に関する記事を参照してください。

## <a name="add-api-permissions"></a>API アクセス許可を追加する

1. **[管理]** セクションで **[API のアクセス許可]** をクリックします。
2. **[アクセス許可の追加]** をクリックし、 **[Azure Active Directory Graph]** 、 **[委任されたアクセス許可]** の順に選択します。
3. 下の一覧で **[ユーザー]** を展開し、**User.Read** が有効であることを確認します。
4. 上にスクロールして **[アプリケーションのアクセス許可]** を選択します。
5. 下のリストで **[ディレクトリ]** を展開し、**Directory.ReadAll** を有効にします。
6. **[アクセス許可の追加]** をクリックして変更を適用します。
7. この時点で、API アクセス許可パネルに *User.Read* と *Directory.ReadAll* の両方が表示されているはずです。 *Directory.ReadAll* の隣の **[Admin consent required]\(管理者の同意が必要\)** 列の警告に注意してください。
8. *Azure サブスクリプション管理者*である場合、下の **[Grant admin consent for *Subscription Name*]\(<サブスクリプション名> に管理者の同意を付与する\)** をクリックします。 *Azure サブスクリプション管理者*でない場合、管理者からの同意を要求します。
![API アクセス許可パネルのスクリーンショット。 User.Read および Directory.ReadAll アクセス許可が追加され、Directory.ReadAll に管理者の同意が必要](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> クラスター管理者グループの同期は、同意が付与された後でないと機能しません。 チェックマークが付いた緑色の円と、"Granted for *Subscription Name*" (<サブスクリプション名> に許可済み) というメッセージが *[Admin consent required]\(管理者の同意が必要\)* 列に表示されます。

管理者およびその他のロールの管理の詳細については、[Azure サブスクリプション管理者の追加または変更](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator)に関する記事を参照してください。

## <a name="resources"></a>リソース

* [Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [クイック スタート:Azure Active Directory v1.0 エンドポイントを使用してアプリを登録する](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>次の手順

[Azure Red Hat OpenShift の前提条件](howto-setup-environment.md)をすべて満たすと、最初のクラスターを作成する準備は完了です。

次のチュートリアルを試してください。
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift クラスターを作成する](tutorial-create-cluster.md)
