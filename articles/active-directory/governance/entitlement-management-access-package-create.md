---
title: エンタイトルメント管理で新しいアクセス パッケージを作成する - Azure AD
description: Azure Active Directory エンタイトルメント管理で共有するリソースの新しいアクセス パッケージを作成する方法を説明します。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3df08272b352ee789c9879b1118105c435cffbd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103011087"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理で新しいアクセス パッケージを作成する

アクセス パッケージを使用すると、リソースとポリシーを 1 回だけ設定して、アクセス パッケージの存続期間中にアクセスを自動的に管理できます。 この記事では、新しいアクセス パッケージの作成方法について説明します。

## <a name="overview"></a>概要

すべてのアクセス パッケージは、カタログという名前のコンテナーに配置する必要があります。 カタログは、アクセス パッケージに追加できるリソースを定義します。 カタログを指定しない場合、アクセス パッケージは一般カタログに格納されます。 現時点では、既存のアクセス パッケージを別のカタログに移動することはできません。

アクセス パッケージ マネージャーの場合は、所有しているカタログにリソースを追加することはできません。 カタログで利用可能なリソースの使用に制限されます。 カタログにリソースを追加する必要がある場合は、カタログ所有者に問い合わせてください。

すべてのアクセス パッケージには、少なくとも 1 つのポリシーが必要です。 ポリシーは、アクセス パッケージを要求できるユーザーを指定するほか、承認とライフサイクルの設定も指定します。 新しいアクセス パッケージを作成するときに、ディレクトリ内のユーザー、ディレクトリ内にいないユーザー、および管理者直接割り当てのみに対する初期ポリシーを作成することも、後でポリシーを作成することもできます。

![アクセス パッケージを作成します。](./media/entitlement-management-access-package-create/access-package-create.png)

新しいアクセス パッケージを作成する手順の概要を次に示します。

1. [Identity Governance] で、新しいアクセス パッケージを作成するプロセスを開始します。

1. アクセス パッケージを作成するカタログを選択します。

1. アクセス パッケージにカタログからリソースを追加します。

1. リソースごとにリソースの役割を割り当てます。

1. アクセスを要求できるユーザーを指定します。

1. 承認設定を指定します。

1. ライフサイクルの設定を指定します。

## <a name="start-new-access-package"></a>新しいアクセス パッケージを開始する

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[Azure Active Directory]** をクリックしてから **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックします。

1. **[New access package]\(新しいアクセス パッケージ\)** をクリックします。
   
    ![Azure portal でのエンタイトルメント管理](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>基本

**[基本]** タブでは、アクセス パッケージの名前を指定し、アクセス パッケージを作成するカタログを指定します。

1. アクセス パッケージの表示名と説明を入力します。 ユーザーがアクセス パッケージの要求を送信するときに、この情報が表示されます。

1. **[カタログ]** ドロップダウン リストで、アクセス パッケージを作成するカタログを選択します。 たとえば、要求できるすべてのマーケティング リソースを管理するカタログ所有者が存在するとします。 この場合、マーケティング カタログを選択できます。

    アクセス パッケージを作成する権限を持つカタログのみが表示されます。 アクセス パッケージを既存のカタログ内に作成するには、グローバル管理者またはユーザー管理者、あるいはそのカタログ内のカタログ所有者またはアクセス パッケージ マネージャーである必要があります。

    ![[アクセス パッケージ] - [基本]](./media/entitlement-management-access-package-create/basics.png)

    グローバル管理者、ユーザー管理者、カタログ作成者が、一覧表示されていない新しいカタログにアクセス パッケージを作成する場合は、 **[新規作成]** をクリックします。 カタログの名前と説明を入力し、 **[作成]** をクリックします。

    作成するアクセス パッケージとそれに含まれるすべてのリソースが、新しいカタログに追加されます。 後でカタログ所有者をさらに追加することもできます。

1. **[次へ]** をクリックします。

## <a name="resource-roles"></a>リソース ロール

**[リソース ロール]** タブでは、アクセス パッケージに含めるリソースを選択します。 アクセス パッケージを要求して受け取るユーザーは、そのアクセス パッケージ内のすべてのリソース ロールを受け取ります。

1. 追加するリソースの種類をクリックします ( **[Groups and Teams]\(Groups と Teams\)** 、 **[アプリケーション]** 、または **[SharePoint サイト]** )。

1. 表示される選択ウィンドウで、一覧から 1 つまたは複数のリソースを選択します。

    ![アクセス パッケージ - リソース ロール](./media/entitlement-management-access-package-create/resource-roles.png)

    一般カタログまたは新しいカタログにアクセス パッケージを作成する場合は、自分が所有するディレクトリから任意のリソースを選択できます。 少なくともグローバル管理者、ユーザー管理者、またはカタログ作成者である必要があります。

    既存のカタログにアクセス パッケージを作成する場合は、カタログ内に既にある任意のリソースを、それを所有していなくても選択できます。

    グローバル管理者、ユーザー管理者、またはカタログ所有者の場合は、まだカタログに存在しないが自分が所有するリソースを選択する追加のオプションがあります。 選択されたカタログ内に現在存在しないリソースを選択した場合、それらのリソースも、他のカタログ管理者がアクセス パッケージの構築に使用できるようにカタログに追加されます。 カタログに追加できるすべてのリソースを表示するには、[選択] ペインの上部にある **[すべて表示]** チェック ボックスをオンにします。 選択したカタログに現在あるリソースのみを選択する場合は、チェック ボックス **[すべて表示]** をオフのままにします (既定の状態)。

1. リソースを選択したら、**[ロール]** 一覧で、リソースに対してユーザーに割り当てるロールを選択します。

    ![[アクセス パッケージ] - リソース ロールの選択](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. **[次へ]** をクリックします。

>[!NOTE]
>動的なグループは、カタログおよびアクセス パッケージに追加できます。 ただし、アクセス パッケージで動的グループ リソースを管理する場合は、所有者ロールのみを選択できます。

## <a name="requests"></a>Requests

**[Requests]** タブで、アクセス パッケージと承認設定を要求できるユーザーを指定する最初のポリシーを作成します。 その後、要求ポリシーをさらに作成して、追加のユーザー グループがアクセス パッケージに独自の承認設定を要求できるようにすることができます。

![[アクセス パッケージ] - [Requests] タブ](./media/entitlement-management-access-package-create/requests.png)

このアクセス パッケージを要求できるようにするユーザーに応じて、次のいずれかのセクションの手順を行います。

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>確認と作成

**[Review + create]\(確認と作成\)** タブでは、設定の確認と検証エラーのチェックを行うことができます。

1. アクセス パッケージの設定を確認します

    ![[アクセス パッケージ] - [ポリシーを有効にする] 設定](./media/entitlement-management-access-package-create/review-create.png)

1. **[作成]** をクリックしてアクセス パッケージを作成します。

    新しいアクセス パッケージがアクセス パッケージの一覧に表示されます。

## <a name="creating-an-access-package-programmatically"></a>プログラムによるアクセス パッケージの作成

Microsoft Graph を使用して、アクセス パッケージを作成することもできます。  委任された `EntitlementManagement.ReadWrite.All` アクセス許可を持つアプリケーションを有する適切なロールのユーザーは、API を呼び出して、次のことを行うことができます

1. [カタログ内の accessPackageResources を一覧表示し](/graph/api/accesspackagecatalog-list?tabs=http&view=graph-rest-beta)、カタログにまだ存在しないすべてのリソースに対して [accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?tabs=http&view=graph-rest-beta) を作成します。
1. accessPackageCatalog 内の各 accessPackageResource の [accessPackageResourceRoles](/graph/api/accesspackage-list-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) を一覧表示します。 このロールの一覧は、後で accessPackageResourceRoleScope を作成するときにロールを選択するために使用されます。
1. [accessPackage を作成します](/graph/tutorial-access-package-api?view=graph-rest-beta)。
1. [accessPackageAssignmentPolicy を作成します](/graph/api/accesspackageassignmentpolicy-post?tabs=http&view=graph-rest-beta)。
1. アクセス パッケージ内で必要なリソース ロールごとに [accessPackageResourceRoleScope](/graph/api/accesspackage-post-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) を作成します。

## <a name="next-steps"></a>次の手順

- [リンクを共有してアクセス パッケージを要求する](entitlement-management-access-package-settings.md)
- [アクセス パッケージのリソースのロールを変更する](entitlement-management-access-package-resources.md)
- [アクセス パッケージにユーザーを直接割り当てる](entitlement-management-access-package-assignments.md)
- [アクセス パッケージのアクセス レビューを作成する](entitlement-management-access-reviews-create.md)
