---
title: ポータルで Azure AD アプリとサービス プリンシパルを作成する
titleSuffix: Microsoft identity platform
description: Azure Resource Manager でロールベースのアクセス制御を使用してリソースへのアクセスを管理するために、新しい Azure Active Directory アプリとサービス プリンシパルを作成します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.date: 06/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: 6adc3a8af90f6f05f640de97b8fa74c8d40e0329
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175548"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>方法:リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する

この記事では、ロール ベースのアクセス制御で使用できる、新しい Azure Active Directory (Azure AD) のアプリケーションとサービス プリンシパルを作成する方法について説明します。 リソースへのアクセスや変更を行う必要があるアプリケーション、ホステッド サービス、または自動化されたツールがある場合は、アプリの ID を作成できます。 この ID は、サービス プリンシパルと呼ばれます。 リソースへのアクセスはサービス プリンシパルに割り当てられているロールによって制限されるため、どのリソースに、どのレベルでアクセスできるかを制御することができます。 セキュリティ上の理由から、自動化ツールにはユーザー ID でのログインを許可するのではなく、常にサービス プリンシパルを使用することを推奨します。

この記事では、Azure portal でサービス プリンシパルを作成するためのポータルの使用方法を示します。 ここでは、シングル テナント アプリケーション (1 つの組織内でのみ実行することを目的としたアプリケーション) に焦点を絞って説明します。 一般に、組織内で実行される基幹業務アプリケーションには、シングル テナント アプリケーションが使用されます。  [Azure PowerShell を使用して、サービス プリンシパルを作成する](howto-authenticate-service-principal-powershell.md)こともできます。

> [!IMPORTANT]
> サービス プリンシパルを作成する代わりに、アプリケーション ID 用に Azure リソースのマネージド ID を使用することを検討します。 コードが、マネージド ID をサポートするサービス上で実行され、Azure AD 認証をサポートするリソースにアクセスする場合、マネージド ID は優れた選択肢となります。 Azure リソースのマネージド ID の詳細 (どのサービスが現在マネージド ID をサポートしているかなど) については、「[Azure リソースのマネージド ID とは](../managed-identities-azure-resources/overview.md)」を参照してください。

## <a name="app-registration-app-objects-and-service-principals"></a>アプリの登録、アプリ オブジェクト、サービス プリンシパル
Azure portal を使用してサービス プリンシパルを直接作成することはできません。  Azure portal 経由でアプリケーションを登録するときに、アプリケーション オブジェクトとサービス プリンシパルは自動的にホーム ディレクトリまたはテナントに作成されます。  アプリの登録、アプリケーション オブジェクト、サービス プリンシパルの関係について詳しくは、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](app-objects-and-service-principals.md)」を参照してください。

## <a name="permissions-required-for-registering-an-app"></a>アプリの登録に必要なアクセス許可

アプリケーションを Azure AD テナントに登録し、Azure サブスクリプションでそのアプリケーションにロールを割り当てるために、十分なアクセス許可を持っている必要があります。

### <a name="check-azure-ad-permissions"></a>Azure AD のアクセス許可を確認する

1. **[Azure Active Directory]** を選択します。
1. 自分のロールを確認します。 **[ユーザー]** ロールを持っている場合は、管理者以外のユーザーがアプリケーションを登録できることを確認する必要があります。

   ![自分のロールを見つける。 ユーザーの場合、確実に管理者以外がアプリを登録できるようにする](./media/howto-create-service-principal-portal/view-user-info.png)

1. 左側のウィンドウで、 **[ユーザー設定]** を選択します。
1. **[アプリの登録]** 設定を確認します。 この値は、管理者だけが設定できます。 **[はい]** に設定されている場合は、Azure AD テナント内のすべてのユーザーがアプリを登録できます。

アプリの登録の設定が **[いいえ]** に設定されている場合は、管理者ロールを持つユーザーのみが、これらの種類のアプリケーションを登録できます。 使用可能な管理者ロールと各ロールに与えられている Azure AD での具体的なアクセス許可については、[Azure AD の組み込みロール](../roles/permissions-reference.md#all-roles)に関するページを参照してください。 アカウントにユーザー ロールが割り当てられているが、アプリの登録の設定が管理者ユーザーに制限されている場合は、アプリの登録のすべての側面について作成と管理が可能な管理者ロールの 1 つを、登録を行うユーザーに割り当てるか、ユーザーがアプリを登録できるようにするよう、管理者に依頼してください。

### <a name="check-azure-subscription-permissions"></a>Azure サブスクリプションのアクセス許可を確認する

Azure サブスクリプションで AD アプリにロールを割り当てるには、アカウントが `Microsoft.Authorization/*/Write` アクセス権を持っている必要があります。 このアクションは、[所有者](../../role-based-access-control/built-in-roles.md#owner)ロールまたは[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)ロールを通じて許可されます。 アカウントに **共同作成者** ロールが割り当てられている場合は、適切なアクセス許可がありません。 この場合、サービス プリンシパルにロールを割り当てようとすると、エラーが発生します。

サブスクリプションのアクセス許可を確認するには、次の手順に従います。

1. **[サブスクリプション]** を検索して選択するか、**ホーム** ページの **[サブスクリプション]** を選択します。

   ![検索](./media/howto-create-service-principal-portal/select-subscription.png)

1. サービス プリンシパルを作成するサブスクリプションを選択します。

   ![割り当てのためのサブスクリプションを選択する](./media/howto-create-service-principal-portal/select-one-subscription.png)

   探しているサブスクリプションが表示されない場合は、**グローバル サブスクリプション フィルター** を選択します。 必要なサブスクリプションがポータルで選択されていることを確認してください。

1. **[アクセス許可]** を選択します。 次に、 **[Click here to view complete access details for this subscription] (このサブスクリプションの完全なアクセスの詳細を表示するにはここをクリック)** を選択します。

   ![サービス プリンシパルを作成するサブスクリプションを選択する](./media/howto-create-service-principal-portal/view-details.png)

1. **[ロールの割り当て]** で **[表示]** を選択して割り当て済みのロールを表示し、AD アプリにロールを割り当てるための適切なアクセス許可があるかどうかを確認します。 ない場合は、サブスクリプション管理者に連絡して、ユーザー アクセス管理者ロールに追加してもらいます。 次の図では、ユーザーには所有者ロールが割り当てられているので、このユーザーには適切なアクセス許可があります。

   ![この例では、ユーザーには所有者ロールが割り当てられています](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="register-an-application-with-azure-ad-and-create-a-service-principal"></a>Azure AD にアプリケーションを登録し、サービス プリンシパルを作成する

では早速、ID の作成を開始しましょう。 問題が発生した場合は、「[必要なアクセス許可](#permissions-required-for-registering-an-app)」をチェックして、使用しているアカウントで ID を作成できることを確認してください。

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> で Azure アカウントにサインインします。
1. **[Azure Active Directory]** を選択します。
1. **[アプリの登録]** を選択します。
1. **[新規登録]** を選択します。
1. アプリケーションに名前を付ける サポートされているアカウントを選択します。これにより、アプリケーションを使用できるユーザーを決定します。 **[リダイレクト URI]** で、作成するアプリケーションの種類で **[Web]** を選択します。 アクセス トークンの送信先の URI を入力します。 [ネイティブ アプリケーション](../manage-apps/application-proxy-configure-native-client-application.md)の資格情報を作成することはできません。 そのタイプを自動化されたアプリケーションに使用することはできません。 値を設定したら、 **[登録]** を選択します。

   ![アプリケーションの名前を入力する](./media/howto-create-service-principal-portal/create-app.png)

Azure AD アプリケーションとサービス プリンシパルが作成されました。

> [!NOTE]
> Azure AD に同じ名前の複数のアプリケーションを登録することはできますが、その場合、アプリケーションのアプリケーション (クライアント) ID は異なっている必要があります。

## <a name="assign-a-role-to-the-application"></a>アプリケーションにロールを割り当てる

サブスクリプション内のリソースにアクセスするには、アプリケーションにロールを割り当てる必要があります。 どのロールがそのアプリケーションに適切なアクセス許可を提供するかを判断します。 利用できるロールの詳細については、「[Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

スコープは、サブスクリプション、リソース グループ、またはリソースのレベルで設定できます。 アクセス許可は、スコープの下位レベルに継承されます。 たとえば、アプリケーションをリソース グループの *閲覧者* ロールに追加すると、そのリソース グループと、その中にあるどのリソースも読み取りができることになります。

1. Azure portal で、アプリケーションを割り当てるスコープのレベルを選択します。 たとえば、サブスクリプションのスコープでロールを割り当てるには、 **[サブスクリプション]** を検索して選択するか、 **[ホーム]** ページで **[サブスクリプション]** を選択します。

   ![たとえば、サブスクリプションのスコープでロールを割り当て](./media/howto-create-service-principal-portal/select-subscription.png)

1. アプリケーションを割り当てる特定のサブスクリプションを選択します。

   ![割り当てのためのサブスクリプションを選択する](./media/howto-create-service-principal-portal/select-one-subscription.png)

   探しているサブスクリプションが表示されない場合は、**グローバル サブスクリプション フィルター** を選択します。 必要なサブスクリプションがポータルで選択されていることを確認してください。

1. **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当ての追加]** を選択します。
1. アプリケーションに割り当てるロールを選択します。 たとえば、アプリケーションがインスタンスの **再起動**、**開始**、**停止** などのアクションを実行できるようにするには、 **[共同作成者]** ロールを選択します。  [使用可能なロール](../../role-based-access-control/built-in-roles.md)の詳細を参照してください。既定では、Azure AD アプリケーションは、使用可能なオプションに表示されません。 アプリケーションを見つけるには、名前を検索し、その名前を選択します。

   ![アプリケーションに割り当てるロールを選択します。](./media/howto-create-service-principal-portal/select-role.png)

1. **[保存]** を選択して、ロールの割り当てを完了します。 該当のスコープのロールを持つユーザーの一覧に、アプリケーションが表示されます。

サービス プリンシパルが設定されました。 それを使用してスクリプトまたはアプリの実行を開始できます。 サービス プリンシパル (アクセス許可、ユーザーが承諾したアクセス許可、承諾したユーザーの参照、アクセス許可の確認、サインイン情報の参照など) を管理するには、**エンタープライズ アプリケーション** に移動します。

次のセクションでは、プログラムでサインインするときに必要な値を取得する方法を示します。

## <a name="get-tenant-and-app-id-values-for-signing-in"></a>サインインするためのテナントとアプリ ID の値を取得する

プログラムでサインインするときは、認証要求およびアプリケーション ID と共にテナント ID を渡します。  証明書または認証キーも必要です (次のセクションで説明します)。 これらの値を取得するには、次の手順に従います。

1. **[Azure Active Directory]** を選択します。
1. Azure AD の **[アプリの登録]** から、アプリケーションを選択します。
1. ディレクトリ (テナント) ID をコピーし、アプリケーション コードに保存します。

    ![ディレクトリ (テナント ID) をコピーし、自分のアプリ コードに保存する](./media/howto-create-service-principal-portal/copy-tenant-id.png)

    ディレクトリ (テナント) ID は、既定のディレクトリの概要ページでも確認できます。

1. **アプリケーション ID** をコピーし、アプリケーション コードに保存します。

   ![アプリケーション (クライアント) ID をコピーする](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="authentication-two-options"></a>認証: 2 つのオプション

サービス プリンシパルで使用できる認証には、パスワードベースの認証 (アプリケーション シークレット) と証明書ベースの認証の 2 種類があります。 "*証明書を使用することをお勧めします*" が、アプリケーション シークレットを作成することもできます。

### <a name="option-1-upload-a-certificate"></a>オプション 1: 証明書のアップロード

既存の証明書がある場合は、それを使用できます。  必要に応じて、*テスト目的でのみ* 自己署名証明書を作成できます。 自己署名証明書を作成するには、PowerShell を開き、次のパラメーターを使用して [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) を実行し、コンピューター上のユーザー証明書ストアに証明書を作成します。

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Windows コントロール パネルからアクセスできる [[Manage User Certificate]\(ユーザー証明書の管理)](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) MMC スナップインを使用して、この証明書をファイルにエクスポートします。

1. **[スタート]** メニューから **[ファイル名を指定して実行]** メニューを選択し、「**certmgr.msc**」と入力します。

   現在のユーザーの証明書マネージャー ツールが表示されます。

1. 証明書を表示するには、左ペインの **[証明書 - 現在のユーザー]** の下の **[個人]** ディレクトリを展開します。
1. 作成した証明書を右クリックし、 **[すべてのタスク] -> [エクスポート]** を選択します。
1. 証明書のエクスポート ウィザードに従います。  秘密キーをエクスポートしたり、.CER ファイルにエクスポートしたりしないでください。

証明書をアップロードするには、次の手順に従います。

1. **[Azure Active Directory]** を選択します。
1. Azure AD の **[アプリの登録]** から、アプリケーションを選択します。
1. **[証明書とシークレット]** を選択します。
1. **[証明書のアップロード]** を選択し、証明書 (既存の証明書、またはエクスポートした自己署名証明書) を選択します。

    ![[証明書のアップロード] を選択して、追加するものを選択する](./media/howto-create-service-principal-portal/upload-cert.png)

1. **[追加]** を選択します。

アプリケーション登録ポータルで証明書をアプリケーションに登録した後、クライアント アプリケーション コードで証明書を使用できるようにします。

### <a name="option-2-create-a-new-application-secret"></a>オプション 2:新しいアプリケーション シークレットを作成する

証明書を使用しないように選択した場合は、新しいアプリケーション シークレットを作成できます。

1. **[Azure Active Directory]** を選択します。
1. Azure AD の **[アプリの登録]** から、アプリケーションを選択します。
1. **[証明書とシークレット]** を選択します。
1. **[クライアント シークレット] -> [新しいクライアント シークレット]** を選択します。
1. シークレットの説明と期間を指定します。 完了したら、 **[追加]** をクリックします。

   クライアント シークレットを保存すると、クライアント シークレットの値が表示されます。 キーは後で取得できないため、この値をコピーしておきます。 キーの値は、アプリケーションとしてサインインするためにアプリケーション ID と共に指定します。 アプリケーションが取得できる場所にキー値を保存します。

   ![後からこれを取得することはできないので、このシークレット値をコピーする](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>リソースに対するアクセス ポリシーを構成する
アプリケーションからアクセスする必要があるリソースに対する追加のアクセス許可の構成が必要になる場合があることに注意してください。 たとえば、キー、シークレット、または証明書へのアクセス権をアプリケーションに付与するには、[キー コンテナーのアクセス ポリシーも更新する](../../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies)必要があります。

1. **Azure portal** で、キー コンテナーに移動し、<a href="https://portal.azure.com/" target="_blank">[アクセス ポリシー]</a> を選択します。
1. **[アクセス ポリシーの追加]** を選択し、アプリケーションに付与するキー、シークレット、証明書のアクセス許可を選択します。  以前に作成したサービス プリンシパルを選択します。
1. **[追加]** を選択してアクセス ポリシーを追加し、 **[保存]** を選択して変更をコミットします。
    ![アクセス ポリシーの追加](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="next-steps"></a>次のステップ
* [Azure PowerShell を使用してサービス プリンシパルを作成する](howto-authenticate-service-principal-powershell.md)方法について学習します。
* セキュリティ ポリシーを指定する方法については、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) に関するページを参照してください。
* ユーザーに対して許可または拒否される場合がある使用可能なアクションの一覧については、「[Azure Resource Manager のリソース プロバイダー操作](../../role-based-access-control/resource-provider-operations.md)」を参照してください。
* **Microsoft Graph** を使用したアプリ登録の操作の詳細については、[アプリケーション](/graph/api/resources/application) API リファレンスを参照してください。
