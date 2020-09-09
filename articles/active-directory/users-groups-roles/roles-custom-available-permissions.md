---
title: カスタム管理者ロールの使用可能なアクセス許可 - Azure AD |Microsoft Docs
description: ID 管理を委任するためのカスタム管理者ロールのアクセス許可。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c11723efe3fac236fce49c1f92fa338d4e58b59
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84732108"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Azure Active Directory でのアプリケーション登録のサブタイプとアクセス許可

この記事では、Azure Active Directory (Azure AD) のカスタム ロール定義に対して現在利用可能なアプリ登録アクセス許可について説明します。

## <a name="permissions-for-managing-single-directory-applications"></a>シングルディレクトリ アプリケーションを管理するためのアクセス許可

カスタム ロールのアクセス許可を選択する場合は、シングルディレクトリ アプリケーションのみを管理するためのアクセス権を付与するオプションがあります。 シングルディレクトリ アプリケーションは、アプリケーションが登録されている Azure AD 組織内のユーザーのみが利用できます。 シングルディレクトリ アプリケーションは、 **[サポートされているアカウントの種類]** が "Accounts in this organizational directory only" (この組織のディレクトリ内のアカウントのみ) に設定されているものとして定義されます。 Graph API では、シングルディレクトリ アプリケーションの signInAudience プロパティが "AzureADMyOrg" に設定されています。

シングルディレクトリ アプリケーションのみを管理するためのアクセス権を付与するには、サブタイプ **applications.myOrganization** で次のアクセス許可を使用します。 たとえば、microsoft.directory/applications.myOrganization/basic/update です。

サブタイプ、アクセス許可、プロパティ セットという一般的な用語の意味については、[カスタム ロールの概要](roles-custom-overview.md)に関するページの説明を参照してください。 次の情報は、アプリケーションの登録に固有のものです。

### <a name="create-and-delete"></a>作成と削除

アプリケーションの登録を作成する機能を許可するために使用できるアクセス許可は 2 つあり、それぞれ動作が異なります。

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft.directory/applications/createAsOwner

このアクセス許可を割り当てると、作成者は、作成されたアプリの登録の最初の所有者として追加され、作成されたアプリの登録は、その作成者の 250 という作成オブジェクト クォータのカウント対象になります。

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft.directory/applications/create

このアクセス許可を割り当てると、作成者は、作成されたアプリの登録の最初の所有者としては追加されず、作成されたアプリの登録は、その作成者の 250 という作成オブジェクト クォータのカウント対象になりません。 担当者がディレクトリ レベルのクォータに達するまでアプリの登録を作成できないようにするものはないため、このアクセス許可は慎重に使用してください。 両方のアクセス許可が割り当てられている場合は、このアクセス許可が優先されます。

両方のアクセス許可が割り当てられている場合、/create アクセス許可が優先されます。 /createAsOwner アクセス許可では作成者を最初の所有者として自動的に追加しませんが、Graph API または PowerShell コマンドレットを使用する場合は、アプリの登録の作成時に所有者を指定できます。

作成のアクセス許可は、 **[New registration]\(新規登録\)** コマンドへのアクセス権を付与します。

[これらのアクセス許可は [New Registration] (新規登録) ポータル コマンドへのアクセス権を付与します](./media/roles-create-custom/new-custom-role.png)

アプリの登録を削除する権限を付与するために使用できるアクセス許可には、次の 2 つがあります。

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

サブタイプに関係なく、アプリの登録を削除する権限を付与します。つまり、シングルテナント アプリケーションとマルチテナント アプリケーションの両方です。

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft.directory/applications.myOrganization/delete

組織内のアカウントまたはシングルテナント アプリケーションのみがアクセスできるものに限定して、アプリの登録を削除する権限を付与します (myOrganization サブタイプ)。

![これらのアクセス許可は [Delete app registration] (アプリの登録の削除) コマンドへのアクセス権を付与する](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> 作成のアクセス許可を含むロールを割り当てるとき、ロールの割り当てはディレクトリ スコープで行う必要があります。 リソース スコープで割り当てられた作成のアクセス許可は、アプリの登録を作成する権限を付与しません。

### <a name="read"></a>Read

組織内のすべてのメンバー ユーザーは、既定でアプリ登録情報を読み取ることができます。 一方、ゲスト ユーザーおよびアプリケーション サービス プリンシパルはできません。 ゲスト ユーザーまたはアプリケーションにロールを割り当てる予定の場合は、適切な読み取りアクセス許可を含める必要があります。

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft.directory/applications/allProperties/read

資格情報など、いかなる状況でも読み取ることができないプロパティを除き、シングルテナントおよびマルチテナントのアプリケーションのすべてのプロパティを読み取る権限。

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>microsoft.directory/applications.myOrganization/allProperties/read

microsoft.directory/applications/allProperties/read と同じアクセス許可を付与しますが、シングルテナント アプリケーションのみが対象となります。

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applications/owners/read

シングルテナントおよびマルチテナント アプリケーションの所有者プロパティを読み取る権限を付与します。 アプリケーション登録の所有者ページのすべてのフィールドへのアクセスを許可します。

![このアクセス許可はアプリ登録の所有者ページへのアクセスを許可します](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>microsoft.directory/applications/standard/read

標準アプリケーション登録プロパティの読み取りアクセスを許可します。 これには、アプリケーション登録ページ間のプロパティが含まれます。

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>microsoft.directory/applications.myOrganization/standard/read

microsoft.directory/applications/standard/read と同じアクセス許可を付与しますが、シングルテナント アプリケーションのみが対象となります。

### <a name="update"></a>更新

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft.directory/applications/allProperties/update

シングルディレクトリとマルチディレクトリのアプリケーションのすべてのプロパティを更新する権限。

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft.directory/applications.myOrganization/allProperties/update

microsoft.directory/applications/allProperties/update と同じアクセス許可を付与しますが、シングルテナント アプリケーションのみが対象となります。

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

シングルディレクトリとマルチディレクトリ上でサポートされているアカウントの種類 (signInAudience) のプロパティを更新する権限。

![このアクセス許可では、認証ページでアプリ登録がサポートされているアカウントの種類のプロパティへのアクセスが許可されます。](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft.directory/applications.myOrganization/audience/update

microsoft.directory/applications/audience/update と同じアクセス許可を付与しますが、シングルテナント アプリケーションのみが対象となります。

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

シングルテナントおよびマルチテナント アプリケーションの応答 URL、サインアウト URL、暗黙的なフロー、発行元ドメインの各プロパティを更新する権限。 サポートされているアカウントの種類を除き、アプリケーション登録の認証ページのすべてのフィールドへのアクセスを許可します。

![サポートされているアカウントの種類を除き、アプリ登録認証へのアクセスを許可する](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directory/applications.myOrganization/authentication/update

microsoft.directory/applications/authentication/update と同じアクセス許可を付与しますが、シングルテナント アプリケーションのみが対象となります。

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

シングルテナントおよびマルチテナント アプリケーションの、名前、ロゴ、ホーム ページ URL、サービス使用条件 URL、プライバシーに関する声明の URL の各プロパティを更新する権限。 アプリケーション登録のブランド化ページのすべてのフィールドへのアクセスを許可します。

![このアクセス許可はアプリ登録のブランド化ページへのアクセスを許可します](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft.directory/applications.myOrganization/basic/update

microsoft.directory/applications/basic/update と同じアクセス許可を付与しますが、シングルテナント アプリケーションのみが対象となります。

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

シングルテナントおよびマルチテナント アプリケーションの、証明書とクライアント シークレットの各プロパティを更新する権限。 アプリケーション登録の証明書およびシークレット ページのすべてのフィールドへのアクセスを許可します。

![このアクセス許可はアプリ登録の証明書およびシークレット ページへのアクセスを許可します](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft.directory/applications.myOrganization/credentials/update

microsoft.directory/applications/credentials/update と同じアクセス許可を付与しますが、シングルディレクトリ アプリケーションのみが対象となります。

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

シングルテナントおよびマルチテナントの所有者プロパティを更新する権限。 アプリケーション登録の所有者ページのすべてのフィールドへのアクセスを許可します。

![このアクセス許可はアプリ登録の所有者ページへのアクセスを許可します](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft.directory/applications.myOrganization/owners/update

microsoft.directory/applications/owners/update と同じアクセス許可を付与しますが、シングルテナント アプリケーションのみが対象となります。

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

シングルテナントおよびマルチテナント アプリケーションの、委任されたアクセス許可、アプリケーションのアクセス許可、承認されたクライアント アプリケーション、必要なアクセス許可、同意の付与の各プロパティを更新する権限。 同意を実行する権限を付与しません。 アプリケーション登録の [API のアクセス許可] および [API の公開] ページのすべてのフィールドへのアクセスを許可します。

![このアクセス許可はアプリ登録の [API のアクセス許可] ページへのアクセスを許可します](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![このアクセス許可はアプリ登録の [API の公開] ページへのアクセスを許可します](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directory/applications.myOrganization/permissions/update

microsoft.directory/applications/permissions/update と同じアクセス許可を付与しますが、シングルテナント アプリケーションのみが対象となります。

## <a name="required-license-plan"></a>必要とされるライセンス プラン

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>次のステップ

- [Azure portal、Azure AD PowerShell、および Graph API](roles-create-custom.md) を使用してカスタム ロールを作成する
- [カスタム ロールの割り当てを表示する](roles-view-assignments.md)
