---
title: アプリ登録管理のためのカスタム管理者ロールのアクセス許可 - Azure Active Directory | Microsoft Docs
description: ID 管理を委任するためのカスタム管理者ロールのアクセス許可。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99f31c5928273973a9089ae9ef1fd184cdb78bbb
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033350"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Azure Active Directory でのアプリケーション登録のサブタイプとアクセス許可

この記事では、Azure Active Directory (Azure AD) のカスタム ロール定義に対して現在利用可能なアプリ登録アクセス許可について説明します。

## <a name="single-tenant-v-multi-tenant-permissions"></a>シングルテナントと マルチテナントのアクセス許可

カスタム ロールのアクセス許可は、シングルテナント アプリケーションとマルチテナント アプリケーションで異なります。 シングルテナント アプリケーションは、アプリケーションが登録されている Azure AD 組織内のユーザーのみが利用できます。 マルチテナント アプリケーションは、すべての Azure AD 組織で利用できます。 シングルテナント アプリケーションは、 **[Supported account types]\(サポートされているアカウントの種類\)** が "Accounts in this organizational directory only" (この組織のディレクトリ内のアカウントのみ) に設定されているものとして定義されます。 Graph API では、シングルテナント アプリケーションは signInAudience プロパティが "AzureADMyOrg" に設定されています。

## <a name="application-registration-subtypes-and-permissions"></a>アプリケーション登録のサブタイプとアクセス許可

サブタイプ、アクセス許可、プロパティ セットという一般的な用語の意味については、[カスタム ロールの概要](roles-custom-overview.md)に関するページの説明を参照してください。 次の情報は、アプリケーションの登録に固有のものです。

### <a name="subtypes"></a>サブタイプ

アプリの登録のサブタイプは applications.myOrganization の 1 つしかありません。 たとえば、microsoft.directory/applications.myOrganization/basic/update です。 このサブタイプは、特定のアプリの登録の **[認証]** ページで設定され、Graph API または PowerShell を使用して signInAudience プロパティを "AzureADMyOrg" に設定することに対応します。 このサブタイプは、組織内のアカウントのみがアクセス可能とマークされているアプリの登録にアクセス許可を制限します (シングルテナント アプリケーション)。

制限付きのアクセス許可を使用すれば、他の組織のアカウントがアクセスできるアプリケーションに読み取りまたは管理のアクセス許可を付与することなく、内部アプリケーションのみに読み取りまたは管理のアクセス許可を付与することができます。

削除のアクセス許可だけでなく、すべての読み取りおよび更新アクセス許可の applications.myOrganization バージョンがあります。 現時点では、作成の applications.myOrganization バージョンはありません。 (microsoft.directory/applications/basic/update などの) 標準のアクセス許可は、すべてのアプリ登録の種類に対して読み取りまたは管理のアクセス許可を付与します。

![シングルテナント アプリケーションまたはマルチテナント アプリケーションを宣言する](./media/roles-custom-available-permissions/supported-account-types.png)

カスタム ロールのプレビューに対する以下のアクセス許可の詳細については、[Azure Active Directory で利用可能なカスタム ロールのアクセス許可](roles-custom-available-permissions.md)に関するページの一覧を参照してください。

### <a name="create-and-delete"></a>作成と削除

アプリケーションの登録を作成する機能を許可するために使用できるアクセス許可は 2 つあり、それぞれ動作が異なります。

- **microsoft.directory/applications/createAsOwner**: このアクセス許可を割り当てると、作成者は、作成されたアプリの登録の最初の所有者として追加され、作成されたアプリの登録は、その作成者の 250 という作成オブジェクト クォータのカウント対象になります。
- **microsoft.directory/applicationPolicies/create**: このアクセス許可を割り当てると、作成者は、作成されたアプリの登録の最初の所有者としては追加されず、作成されたアプリの登録は、その作成者の 250 という作成オブジェクト クォータのカウント対象になりません。 担当者がディレクトリ レベルのクォータに達するまでアプリの登録を作成できないようにするものはないため、このアクセス許可は慎重に使用してください。 両方のアクセス許可が割り当てられている場合は、このアクセス許可が優先されます。

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

### <a name="read"></a>読み取り

組織内のすべてのメンバー ユーザーは、既定でアプリ登録情報を読み取ることができます。 一方、ゲスト ユーザーおよびアプリケーション サービス プリンシパルはできません。 ゲスト ユーザーまたはアプリケーションにロールを割り当てる予定の場合は、適切な読み取りアクセス許可を含める必要があります。

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft.directory/applications/allProperties/read

資格情報などの機微なプロパティを除き、シングルテナントおよびマルチテナントのアプリケーションのすべてのプロパティを読み取る権限。

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>microsoft.directory/applications.myOrganization/allProperties/read

microsoft.directory/applications/allProperties/read と同じアクセス許可を付与しますが、シングルテナント アプリケーションのみが対象となります。

#### <a name="microsoftdirectoryapplicationsstandardread-grants-access-to-all-fields-on-the-application-registration-branding-page"></a>microsoft.directory/applications/standard/read:アプリケーション登録のブランド化ページのすべてのフィールドへのアクセスを許可します

![このアクセス許可はアプリ登録のブランド化ページへのアクセスを許可します](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>microsoft.directory/applications.myOrganization/standard/read

microsoft.directory/applications/standard/read と同じアクセス許可を付与しますが、シングルテナント アプリケーションのみが対象となります。

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applications/owners/read

シングルテナントおよびマルチテナント アプリケーションの所有者プロパティを読み取る権限を付与します。 アプリケーション登録の所有者ページのすべてのフィールドへのアクセスを許可します。

![このアクセス許可はアプリ登録の所有者ページへのアクセスを許可します](./media/roles-custom-available-permissions/app-registration-owners.png)

アプリケーション エンティティの次のプロパティへのアクセスを許可します。

- AllowActAsForAllClients
- AllowPassthroughUsers
- AppAddress
- AppBrandingElements
- AppCategory
- AppCreatedDateTime
- AppData
- AppId
- AppInformationalUrl
- ApplicationTag
- AppLogoUrl
- AppMetadata
- AppOptions
- BinaryExtensionAttribute
- BooleanExtensionAttribute
- CountriesBlockedForMinors
- CreatedOnBehalfOf
- DateTimeExtensionAttribute
- DisplayName
- ExtensionAttributeDefinition
- IntegerExtensionAttribute
- KnownClientApplications
- LargeIntegerExtensionAttribute
- LegalAgeGroupRule
- LocalizedAppBrandingElements
- MainLogo
- MsaAppId
- ResourceApplicationSet
- ServiceDiscoveryEndpoint
- StringExtensionAttribute
- TrustedCertificateSubject
- WebApi
- WebApp
- WwwHomepage

### <a name="update"></a>アップデート

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft.directory/applications/allProperties/update

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft.directory/applications.myOrganization/allProperties/update

microsoft.directory/applications/allProperties/update と同じアクセス許可を付与しますが、シングルテナント アプリケーションのみが対象となります。

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

アプリケーション登録の認証ページのすべてのフィールドへのアクセスを許可します。

![このアクセス許可はアプリ登録の認証ページへのアクセスを許可します](./media/roles-custom-available-permissions/supported-account-types.png)

アプリケーション リソースの次のプロパティへのアクセスを許可します。

- AvailableToOtherTenants
- SignInAudience

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft.directory/applications.myOrganization/audience/update

microsoft.directory/applications/audience/update と同じアクセス許可を付与しますが、シングルテナント アプリケーションのみが対象となります。

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

シングルテナントおよびマルチテナント アプリケーションの応答 URL、サインアウト URL、暗黙的なフロー、発行元ドメインの各プロパティを更新する権限。 サポートされているアカウントの種類を除き、アプリケーション登録の認証ページのすべてのフィールドへのアクセスを許可します。

![サポートされているアカウントの種類を除き、アプリ登録認証へのアクセスを許可する](./media/roles-custom-available-permissions/supported-account-types.png)

 アプリケーション リソースの次のプロパティへのアクセスを許可します。

- AcceptMappedClaims
- AccessTokenAcceptedVersion
- AddIns
- GroupMembershipClaims
- IsDeviceOnlyAuthSupported
- OAuth2LegacyUrlPathMatching
- OauthOidcResponsePolicyBitmap
- OptionalClaims
- OrgRestrictions
- PublicClient
- UseCustomTokenSigningKey

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directory/applications.myOrganization/authentication/update

microsoft.directory/applications/authentication/update と同じアクセス許可を付与しますが、シングルテナント アプリケーションのみが対象となります。

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

シングルテナントおよびマルチテナント アプリケーションの、名前、ロゴ、ホーム ページ URL、サービス使用条件 URL、プライバシーに関する声明の URL の各プロパティを更新する権限。 アプリケーション登録のブランド化ページのすべてのフィールドへのアクセスを許可します。

![このアクセス許可はアプリ登録のブランド化ページへのアクセスを許可します](./media/roles-custom-available-permissions/app-registration-branding.png)

アプリケーション リソースの次のプロパティへのアクセスを許可します。

- AllowActAsForAllClients
- AllowPassthroughUsers
- AppAddress
- AppBrandingElements
- AppCategory
- AppData
- AppId
- AppInformationalUrl
- ApplicationTag
- AppLogoUrl
- AppMetadata
- AppOptions
- BinaryExtensionAttribute
- BooleanExtensionAttribute
- CountriesBlockedForMinors
- CreatedOnBehalfOf
- DateTimeExtensionAttribute
- DisplayName
- ExtensionAttributeDefinition
- IntegerExtensionAttribute
- KnownClientApplications
- LargeIntegerExtensionAttribute
- LegalAgeGroupRule
- LocalizedAppBrandingElements
- MainLogo
- MsaAppId
- ResourceApplicationSet
- ServiceDiscoveryEndpoint
- StringExtensionAttribute
- TrustedCertificateSubject
- WebApi
- WebApp
- WwwHomepage

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft.directory/applications.myOrganization/basic/update

microsoft.directory/applications/basic/update と同じアクセス許可を付与しますが、シングルテナント アプリケーションのみが対象となります。

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

シングルテナントおよびマルチテナント アプリケーションの、証明書とクライアント シークレットの各プロパティを更新する権限。 アプリケーション登録の証明書およびシークレット ページのすべてのフィールドへのアクセスを許可します。

![このアクセス許可はアプリ登録の証明書およびシークレット ページへのアクセスを許可します](./media/roles-custom-available-permissions/app-registration-secrets.png)

アプリケーション リソースの次のプロパティへのアクセスを許可します。
- AsymmetricKey
- EncryptedSecretKey
- KeyDescription
- SharedKeyReference
- TokenEncryptionKeyId

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft.directory/applications.myOrganization/credentials/update

microsoft.directory/applications/credentials/update と同じアクセス許可を付与しますが、シングルディレクトリ アプリケーションのみが対象となります。

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

シングルテナントおよびマルチテナントの所有者プロパティを更新する権限。 アプリケーション登録の所有者ページのすべてのフィールドへのアクセスを許可します。

![このアクセス許可はアプリ登録の所有者ページへのアクセスを許可します](./media/roles-custom-available-permissions/app-registration-owners.png)

アプリケーション リソースの次のプロパティへのアクセスを許可します。
- 所有者

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft.directory/applications.myOrganization/owners/update

microsoft.directory/applications/owners/update と同じアクセス許可を付与しますが、シングルテナント アプリケーションのみが対象となります。

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

シングルテナントおよびマルチテナント アプリケーションの、委任されたアクセス許可、アプリケーションのアクセス許可、承認されたクライアント アプリケーション、必要なアクセス許可、同意の付与の各プロパティを更新する権限。 同意を実行する権限を付与しません。 アプリケーション登録の [API のアクセス許可] および [API の公開] ページのすべてのフィールドへのアクセスを許可します。

![このアクセス許可はアプリ登録の [API のアクセス許可] ページへのアクセスを許可します](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![このアクセス許可はアプリ登録の [API の公開] ページへのアクセスを許可します](./media/roles-custom-available-permissions/app-registration-expose-api.png)

アプリケーション リソースの次のプロパティへのアクセスを許可します。

- AppIdentifierUri
- Entitlement
- PreAuthorizedApplications
- RecordConsentConditions
- RequiredResourceAccess

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directory/applications.myOrganization/permissions/update

microsoft.directory/applications/permissions/update と同じアクセス許可を付与しますが、シングルテナント アプリケーションのみが対象となります。

## <a name="required-license-plan"></a>必要とされるライセンス プラン

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>次の手順

- [Azure portal、Azure AD PowerShell、および Graph API](roles-create-custom.md) を使用してカスタム ロールを作成する
- [カスタム ロールの割り当てを表示する](roles-view-assignments.md)
