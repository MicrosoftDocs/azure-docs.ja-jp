---
title: Azure Active Directory のカスタム ロールに対するアプリのアクセス許可 | Microsoft Docs
description: Azure portal、PowerShell、Graph API におけるカスタム Azure AD ロールに対するエンタープライズ アプリのアクセス許可について見ていきます。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: d2159540c688a63082efb792fd4d261062ef65d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466795"
---
# <a name="enterprise-application-permissions-for-custom-roles-in-azure-active-directory"></a>Azure Active Directory のカスタム ロールに対するエンタープライズ アプリケーションのアクセス許可

この記事では、Azure Active Directory (Azure AD) のカスタム ロール定義に対して現在利用可能なエンタープライズ アプリケーションのアクセス許可について説明します。 この記事には、いくつかの一般的なシナリオで使用されるアクセス許可の一覧のほか、エンタープライズ アプリのアクセス許可を網羅した一覧が記載されています。 現在このリリースでは、アプリケーション プロキシのアクセス許可はロールアウトされていません。

## <a name="required-license-plan"></a>必要とされるライセンス プラン

この機能を使用するためには、Azure AD 組織に Azure AD Premium P1 ライセンスが必要です。 要件に対する適切なライセンスを確認するには、「[Free、Basic、および Premium エディションの一般公開されている機能の比較](https://azure.microsoft.com/pricing/details/active-directory/)」をご覧ください。

## <a name="enterprise-application-permissions"></a>エンタープライズ アプリケーションのアクセス許可

これらのアクセス許可を使用する方法について詳しくは、[カスタム ロールを割り当ててエンタープライズ アプリを管理する](custom-enterprise-apps.md)方法に関するページを参照してください。

### <a name="assigning-users-or-groups-to-an-application"></a>アプリケーションにユーザーまたはグループを割り当てる

SAML ベースのシングル サインオン アプリケーションにアクセスできるユーザーとグループの割り当てを委任します。 必要なアクセス許可

- microsoft.directory/servicePrincipals/appRoleAssignedTo/update

### <a name="creating-gallery-applications"></a>ギャラリー アプリケーションを作成する

ServiceNow、F5、Salesforce など、Azure AD ギャラリー アプリケーションの作成を委任します。 必要なアクセス許可:

- microsoft.directory/applicationTemplates/instantiate

### <a name="configuring-basic-saml-urls"></a>基本的な SAML URL を構成する

SAML ベースのシングル サインオン アプリケーションに使用される基本的な SAML 構成の更新と読み取りを委任します。 必要なアクセス許可:

- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/applications.myOrganization/authentication/update

### <a name="rolling-over-or-creating-signing-certs"></a>署名証明書をロール オーバーまたは作成する

SAML ベースのシングル サインオン アプリケーションに使用される署名証明書の管理を委任します。 必要なアクセス許可:

microsoft.directory/applications/credentials/update

### <a name="update-expiring-sign-in-cert-notification-email-address"></a>期限切れが迫っているサインイン証明書の通知メール アドレスを更新する

SAML ベースのシングル サインオン アプリケーションについて、期限切れが迫っているサインイン証明書の通知メール アドレスの更新を委任します。 必要なアクセス許可:

- microsoft.directory/applications.myOrganization/authentication/update
- microsoft.directory/applications.myOrganization/permissions/update
- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/servicePrincipals/basic/update

### <a name="manage-saml-token-signature-and-sign-in-algorithm"></a>SAML トークン署名とサインイン アルゴリズムを管理する

SAML ベースのシングル サインオン アプリケーションに使用される SAML トークン署名とサインイン アルゴリズムの更新を委任します。 必要なアクセス許可:

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

### <a name="manage-user-attributes-and-claims"></a>ユーザー属性と要求を管理する

SAML ベースのシングル サインオン アプリケーションに使用されるユーザー属性と要求の作成、削除、更新を委任します。 必要なアクセス許可:

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

## <a name="app-provisioning-permissions"></a>アプリのプロビジョニングのアクセス許可

ジョブ、スキーマ、資格情報の管理など、UI を使用した書き込み操作を実行するためには、プロビジョニング ページを表示するための読み取りアクセス許可も必要となります。

現在、"すべてのユーザーとグループ" または "割り当てられたユーザーとグループ" にスコープを設定するためには、synchronizationJob と synchronizationCredentials の両方のアクセス許可が必要となります。

### <a name="turn-on-or-restart-provisioning-jobs"></a>プロビジョニング ジョブを有効化または再開する

プロビジョニング ジョブを有効化、無効化、再開する権限を委任します。 必要なアクセス許可:

- microsoft.directory/servicePrincipals/synchronizationJobs/manage  

### <a name="configure-the-provisioning-schema"></a>プロビジョニング スキーマを構成する  

属性マッピングに対する更新を委任します。 必要なアクセス許可:

- microsoft.directory/servicePrincipals/synchronizationSchema/manage  

### <a name="read-provisioning-settings-associated-with-the-application-object"></a>アプリケーション オブジェクトに関連付けられているプロビジョニング設定を読み取る

オブジェクトに関連付けられているプロビジョニング設定を読み取る権限を委任します。 必要なアクセス許可:

- microsoft.directory/applications/synchronization/standard/read

### <a name="read-provisioning-settings-associated-with-your-service-principal"></a>サービス プリンシパルに関連付けられているプロビジョニング設定を読み取る

サービス プリンシパルに関連付けられているプロビジョニング設定を読み取る権限を委任します。 必要なアクセス許可:

- microsoft.directory/servicePrincipals/synchronization/standard/read

### <a name="authorize-application-access-for-provisioning"></a>プロビジョニングのためのアプリケーション アクセスを承認する  

プロビジョニングのためのアプリケーション アクセスを承認する権限を委任します (例: OAuth ベアラー トークンの入力など)。 必要なアクセス許可:

- microsoft.directory/servicePrincipals/synchronizationCredentials/manage

## <a name="full-list-of-permissions"></a>アクセス許可の全一覧

権限 | 説明
---------- | -----------
microsoft.directory/applicationPolicies/allProperties/read | アプリケーション ポリシーのすべてのプロパティを読み取ります。
microsoft.directory/applicationPolicies/allProperties/update | アプリケーション ポリシーのすべてのプロパティを更新します。
microsoft.directory/applicationPolicies/basic/update | アプリケーション ポリシーの標準プロパティを更新します。
microsoft.directory/applicationPolicies/create | アプリケーション ポリシーを作成します。
microsoft.directory/applicationPolicies/createAsOwner | アプリケーション ポリシーを作成します。 作成者は最初の所有者として追加されます。
microsoft.directory/applicationPolicies/delete | アプリケーション ポリシーを削除します。
microsoft.directory/applicationPolicies/owners/read | アプリケーション ポリシーの所有者を読み取ります。
microsoft.directory/applicationPolicies/owners/update | アプリケーション ポリシーの所有者プロパティを更新します。
microsoft.directory/applicationPolicies/policyAppliedTo/read | オブジェクト リストに適用されているアプリケーション ポリシーを読み取ります。
microsoft.directory/applicationPolicies/standard/read | アプリケーション ポリシーの標準プロパティを読み取ります。
microsoft.directory/servicePrincipals/allProperties/allTasks | Azure Active Directory での servicePrincipals の作成と削除、およびすべてのプロパティの読み取りと更新。
microsoft.directory/servicePrincipals/allProperties/read | servicePrincipals のすべてのプロパティを読み取ります。
microsoft.directory/servicePrincipals/allProperties/update | servicePrincipals のすべてのプロパティを更新します。
microsoft.directory/servicePrincipals/appRoleAssignedTo/read | サービス プリンシパルのロールの割り当てを読み取ります。
microsoft.directory/servicePrincipals/appRoleAssignedTo/update | サービス プリンシパルのロールの割り当てを更新します。
microsoft.directory/servicePrincipals/appRoleAssignments/read | サービス プリンシパルに割り当てられたロールの割り当てを読み取ります。
microsoft.directory/servicePrincipals/audience/update | サービス プリンシパルの対象ユーザー プロパティを更新します。
microsoft.directory/servicePrincipals/authentication/update | サービス プリンシパルの認証プロパティを更新します。
microsoft.directory/servicePrincipals/basic/update | サービス プリンシパルで基本プロパティを更新します。
microsoft.directory/servicePrincipals/create | サービス プリンシパルを作成します。
microsoft.directory/servicePrincipals/createAsOwner | サービス プリンシパルを作成します。 作成者は最初の所有者として追加されます。
microsoft.directory/servicePrincipals/credentials/update | サービス プリンシパルの資格情報プロパティを更新します。
microsoft.directory/servicePrincipals/delete | サービス プリンシパルを削除します。
microsoft.directory/servicePrincipals/disable | サービス プリンシパルを無効にします。
microsoft.directory/servicePrincipals/enable | サービス プリンシパルを有効にします。
microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | サービス プリンシパルのパスワード シングル サインオン資格情報を読み取ります。
microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | サービス プリンシパルのパスワード シングル サインオン資格情報を管理します。
microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | サービス プリンシパルの委任されたアクセス許可付与を読み取ります。
microsoft.directory/servicePrincipals/owners/read | サービス プリンシパルの所有者を読み取ります。
microsoft.directory/servicePrincipals/owners/update | サービス プリンシパルの所有者を更新します。
microsoft.directory/servicePrincipals/permissions/update |  
microsoft.directory/servicePrincipals/policies/read | サービス プリンシパルのポリシーを読み取ります。
microsoft.directory/servicePrincipals/policies/update | サービス プリンシパルのポリシーを更新します。
microsoft.directory/servicePrincipals/standard/read | サービス プリンシパルの標準プロパティを読み取ります。
microsoft.directory/servicePrincipals/synchronization/standard/read | サービス プリンシパルに関連付けられているプロビジョニング設定を読み取ります。
microsoft.directory/servicePrincipals/tag/update | サービス プリンシパルのタグ プロパティを更新します。
microsoft.directory/applicationTemplates/instantiate | アプリケーション テンプレートからギャラリー アプリケーションのインスタンス化。
microsoft.directory/auditLogs/allProperties/read | 監査ログを読み取ります。
microsoft.directory/signInReports/allProperties/read | サインイン レポートを読み取ります。
microsoft.directory/applications/synchronization/standard/read | アプリケーション オブジェクトに関連付けられているプロビジョニング設定を読み取ります。
microsoft.directory/servicePrincipals/synchronizationJobs/manage | サービス プリンシパル リソースのジョブ同期の側面をすべて管理します。
microsoft.directory/servicePrincipals/synchronization/standard/read | サービス プリンシパルに関連付けられているプロビジョニング設定を読み取ります。
microsoft.directory/servicePrincipals/synchronizationSchema/manage | サービス プリンシパル リソースのスキーマ同期の側面をすべて管理します。
microsoft.directory/provisioningLogs/allProperties/read | プロビジョニング ログのすべてのプロパティを読み取ります。

## <a name="next-steps"></a>次のステップ

- [Azure portal、Azure AD PowerShell、および Graph API を使用してカスタム ロールを作成する](custom-create.md)
- [ロールの割り当てを一覧表示する](view-assignments.md)
