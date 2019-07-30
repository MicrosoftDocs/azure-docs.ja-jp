---
title: ポータルでの Azure アプリ ID の作成 | Microsoft Docs
description: Azure Resource Manager でロール ベースのアクセス制御と共に使用してリソースへのアクセスを管理できる、新しい Azure Active Directory のアプリケーションとサービス プリンシパルを作成する方法について説明します。
services: active-directory
documentationcenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 825966fbb0db537aad8de39e69e17418e6432b44
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324676"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>方法:リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する

この記事では、ロール ベースのアクセス制御で使用できる、新しい Azure Active Directory (Azure AD) のアプリケーションとサービス プリンシパルを作成する方法について説明します。 リソースへのアクセスや変更を行う必要があるコードがある場合は、そのアプリの ID を作成できます。 この ID は、サービス プリンシパルと呼ばれます。 その後、サービス プリンシパルに必要なアクセス許可を割り当てることができます。 この記事では、ポータルを使用してサービス プリンシパルを作成する方法について説明します。 ここでは、シングル テナント アプリケーション (1 つの組織内でのみ実行することを目的としたアプリケーション) に焦点を絞って説明します。 一般に、組織内で実行される基幹業務アプリケーションには、シングル テナント アプリケーションが使用されます。

> [!IMPORTANT]
> サービス プリンシパルを作成する代わりに、アプリケーション ID 用に Azure リソースのマネージド ID を使用することを検討します。 コードが、マネージド ID をサポートするサービス上で実行され、Azure AD 認証をサポートするリソースにアクセスする場合、マネージド ID は優れた選択肢となります。 Azure リソースのマネージド ID の詳細 (どのサービスが現在マネージド ID をサポートしているかなど) については、「[Azure リソースのマネージド ID とは](../managed-identities-azure-resources/overview.md)」を参照してください。

## <a name="create-an-azure-active-directory-application"></a>Azure Active Directory アプリケーションを作成する

では早速、ID の作成を開始しましょう。 問題が発生した場合は、「[必要なアクセス許可](#required-permissions)」をチェックして、使用しているアカウントで ID を作成できることを確認してください。

1. [Azure Portal](https://portal.azure.com) で Azure アカウントにサインインします。
1. **[Azure Active Directory]** を選択します。
1. **[アプリの登録]** を選択します。
1. **[新規登録]** を選択します。
1. アプリケーションに名前を付ける サポートされているアカウントを選択します。これにより、アプリケーションを使用できるユーザーを決定します。 **[リダイレクト URI]** で、作成するアプリケーションの種類で **[Web]** を選択します。 アクセス トークンの送信先の URI を入力します。 [ネイティブ アプリケーション](../manage-apps/application-proxy-configure-native-client-application.md)の資格情報を作成することはできません。 そのタイプを自動化されたアプリケーションに使用することはできません。 値を設定したら、 **[登録]** を選択します。

   ![アプリケーションの名前を入力する](./media/howto-create-service-principal-portal/create-app.png)

Azure AD アプリケーションとサービス プリンシパルが作成されました。

## <a name="assign-the-application-to-a-role"></a>アプリケーションをロールに割り当てる

サブスクリプション内のリソースにアクセスするには、アプリケーションをロールに割り当てる必要があります。 どのロールがそのアプリケーションに適切なアクセス許可を提供するかを判断します。 利用可能なロールについては、「[RBAC: 組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

スコープは、サブスクリプション、リソース グループ、またはリソースのレベルで設定できます。 アクセス許可は、スコープの下位レベルに継承されます。 たとえば、アプリケーションをリソース グループの閲覧者ロールに追加すると、アプリケーションではリソース グループとそれに含まれているすべてのリソースを読み取ることができます。

1. アプリケーションを割り当てるスコープのレベルに移動します。 たとえば、サブスクリプション スコープでロールを割り当てるには、 **[すべてのサービス]** および **[サブスクリプション]** を選択します。

   ![たとえば、サブスクリプションのスコープでロールを割り当て](./media/howto-create-service-principal-portal/select-subscription.png)

1. アプリケーションを割り当てる特定のサブスクリプションを選択します。

   ![割り当てのためのサブスクリプションを選択する](./media/howto-create-service-principal-portal/select-one-subscription.png)

   探しているサブスクリプションが表示されない場合は、**グローバル サブスクリプション フィルター**を選択します。 必要なサブスクリプションがポータルで選択されていることを確認してください。

1. **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当ての追加]** を選択します。
1. アプリケーションに割り当てるロールを選択します。 アプリケーションがインスタンスの**再起動**、**開始**、**停止**などのアクションを実行できるようにするには、 **[共同作成者]** ロールを選択します。 既定では、Azure AD アプリケーションは、使用可能なオプションに表示されません。 アプリケーションを見つけるには、名前を検索し、その名前を選択します。

   ![アプリケーションに割り当てるロールを選択します。](./media/howto-create-service-principal-portal/select-role.png)

1. **[保存]** を選択して、ロールの割り当てを完了します。 該当のスコープのロールに割り当てられたユーザーの一覧にアプリケーションが表示されます。

サービス プリンシパルが設定されました。 それを使用してスクリプトまたはアプリの実行を開始できます。 次のセクションでは、プログラムでサインインするときに必要な値を取得する方法を示します。

## <a name="get-values-for-signing-in"></a>サインインするための値を取得する

プログラムでサインインするときは、認証要求でテナント ID を渡す必要があります。 また、アプリケーションの ID と認証キーも必要です。 これらの値を取得するには、次の手順に従います。

1. **[Azure Active Directory]** を選択します。
1. Azure AD の **[アプリの登録]** から、アプリケーションを選択します。
1. ディレクトリ (テナント) ID をコピーし、アプリケーション コードに保存します。

    ![ディレクトリ (テナント ID) をコピーし、自分のアプリ コードに保存する](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. **アプリケーション ID** をコピーし、アプリケーション コードに保存します。

   ![アプリケーション (クライアント) ID をコピーする](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>証明書とシークレット
デーモン アプリケーションでは、Azure AD で認証する 2 つの形式の資格情報 (証明書とアプリケーション シークレット) を使用できます。  証明書を使用するようお勧めしますが、新しいアプリケーション シークレットを作成することもできます。

### <a name="upload-a-certificate"></a>証明書のアップロード

既存の証明書がある場合は、それを使用できます。  必要に応じて、テスト目的で自己署名証明書を作成することもできます。 PowerShell を開いて、次のパラメーターを使用して [New-selfsignedcertificate](/powershell/module/pkiclient/new-selfsignedcertificate) を実行して、コンピューター上のユーザー証明書ストアに自己署名証明書を作成します: `$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature`。  Windows コントロール パネルからアクセスできる [[Manage User Certificate]\(ユーザー証明書の管理)](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)MMC スナップインを使用して、この証明書をエクスポートします。

証明書をアップロードするには、次の手順に従います。

1. **[証明書とシークレット]** を選択します。
1. **[証明書のアップロード]** を選択し、証明書 (既存の証明書、またはエクスポートした自己署名証明書) を選択します。

    ![[証明書のアップロード] を選択して、追加するものを選択する](./media/howto-create-service-principal-portal/upload-cert.png)

1. **[追加]** を選択します。

アプリケーション登録ポータルで証明書をアプリケーションに登録したら、クライアント アプリケーションで証明書を使用できるようにする必要があります。

### <a name="create-a-new-application-secret"></a>新しいアプリケーション シークレットを作成する

証明書を使用しないように選択した場合は、新しいアプリケーション シークレットを作成できます。

1. **[証明書とシークレット]** を選択します。
1. **[クライアント シークレット] -> [新しいクライアント シークレット]** を選択します。
1. シークレットの説明と期間を指定します。 完了したら、 **[追加]** をクリックします。

   クライアント シークレットを保存すると、クライアント シークレットの値が表示されます。 キーは後で取得できないため、この値をコピーしておきます。 キー値は、アプリケーションとしてサインインする際にアプリケーション ID と共に入力します。 アプリケーションが取得できる場所にキー値を保存します。

   ![後からこれを取得することはできないので、このシークレット値をコピーする](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="required-permissions"></a>必要なアクセス許可

アプリケーションを Azure AD テナントに登録し、Azure サブスクリプションでそのアプリケーションをロールに割り当てるための十分なアクセス許可が必要です。

### <a name="check-azure-ad-permissions"></a>Azure AD のアクセス許可を確認する

1. **[Azure Active Directory]** を選択します。
1. 自分のロールを確認します。 **[ユーザー]** ロールを持っている場合は、管理者以外のユーザーがアプリケーションを登録できることを確認する必要があります。

   ![自分のロールを見つける。 ユーザーの場合、確実に管理者以外がアプリを登録できるようにする](./media/howto-create-service-principal-portal/view-user-info.png)

1. **[ユーザー設定]** を選択します。
1. **[アプリの登録]** 設定を確認します。 この値は、管理者だけが設定できます。 **[はい]** に設定されている場合は、Azure AD テナント内のすべてのユーザーがアプリを登録できます。

アプリの登録の設定が **[いいえ]** に設定されている場合は、管理者ロールを持つユーザーのみが、これらの種類のアプリケーションを登録できます。 使用可能な管理者ロールと各ロールに与えられている Azure AD での具体的なアクセス許可については、[利用可能なロール](../users-groups-roles/directory-assign-admin-roles.md#available-roles)と[ロールのアクセス許可](../users-groups-roles/directory-assign-admin-roles.md#role-permissions)に関するページを参照してください。 アカウントがユーザー ロールに割り当てられているが、アプリの登録の設定が管理者ユーザーに制限されている場合は、管理者に、アプリの登録のすべての側面を作成および管理できるいずれかの管理者ロールに自分を割り当てるか、ユーザーがアプリを登録できるようにするよう依頼してください。

### <a name="check-azure-subscription-permissions"></a>Azure サブスクリプションのアクセス許可を確認する

Azure サブスクリプションで、AD アプリをロールに割り当てるには、アカウントに `Microsoft.Authorization/*/Write` アクセス権が必要です。 このアクションは、[所有者](../../role-based-access-control/built-in-roles.md#owner)ロールまたは[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)ロールを通じて許可されます。 アカウントが**共同作成者**ロールに割り当てられている場合は、適切なアクセス許可がありません。 この場合、サービス プリンシパルをロールに割り当てようとすると、エラーが発生します。

サブスクリプションのアクセス許可を確認するには、次の手順に従います。

1. 右上隅にあるアカウントを選択し、 **[...] -> [アクセス許可]** を選択します。

   ![自分のアカウントとユーザーのアクセス許可を選択する](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. ドロップダウン リストから、サービス プリンシパルを作成するサブスクリプションを選択します。 次に、 **[Click here to view complete access details for this subscription] (このサブスクリプションの完全なアクセスの詳細を表示するにはここをクリック)** を選択します。

   ![サービス プリンシパルを作成するサブスクリプションを選択する](./media/howto-create-service-principal-portal/view-details.png)

1. **[ロールの割り当て]** を選択して割り当て済みのロールを表示し、AD アプリをロールに割り当てるための適切なアクセス許可があるかどうかを確認します。 ない場合は、サブスクリプション管理者に連絡して、ユーザー アクセス管理者ロールに追加してもらいます。 次の図では、ユーザーは所有者ロールに割り当てられているので、このユーザーには適切なアクセス許可があります。

   ![この例では、ユーザーが所有者ロールに割り当てられています](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>次の手順

* マルチテナント アプリケーションのセットアップについては、「 [Azure Resource Manager API を使用した承認の開発者ガイド](../../azure-resource-manager/resource-manager-api-authentication.md)」を参照してください。
* セキュリティ ポリシーを指定する方法については、「[Azure のロールベースのアクセス制御](../../role-based-access-control/role-assignments-portal.md)」を参照してください。  
* ユーザーに対して許可または拒否される場合がある使用可能なアクションの一覧については、「[Azure Resource Manager のリソース プロバイダー操作](../../role-based-access-control/resource-provider-operations.md)」を参照してください。
