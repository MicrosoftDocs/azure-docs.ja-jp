---
title: 既定のユーザー アクセス許可 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で利用できるさまざまなユーザー アクセス許可について説明します。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18, contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb01a3e0fc5bc11a4d3de62b16aafb7dd308e34a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724273"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Azure Active Directory の既定のユーザー アクセス許可とは
Azure Active Directory (Azure AD) では、すべてのユーザーに既定のアクセス許可のセットが付与されます。 ユーザーのアクセスは、ユーザーの種類、ユーザーの[ロールの割り当て](active-directory-users-assign-role-azure-portal.md)、および個々のオブジェクトの所有権で構成されます。 この記事では、これらの既定のアクセス許可について説明し、メンバーとゲスト ユーザーの既定値を比較します。 既定のユーザー アクセス許可は、Azure AD のユーザー設定のみで変更できます。

## <a name="member-and-guest-users"></a>メンバーとゲスト ユーザー
受け取る既定のアクセス許可のセットは、ユーザーがテナントのネイティブ メンバー (メンバー ユーザー) かどうか、またはユーザーが B2B コラボレーション ゲスト (ゲスト ユーザー) としての別のディレクトリからのユーザーかどうかによって異なります。 ゲスト ユーザーの追加の詳細については、「[Azure AD B2B コラボレーションとは](../external-identities/what-is-b2b.md)」を参照してください。
* メンバー ユーザーは、アプリケーションの登録、自分のプロファイル写真と携帯電話番号の管理、自分のパスワードの変更、B2B ゲストの招待を行うことができます。 さらに、すべてのディレクトリ情報を読むことができます (いくつか例外があります)。 
* ゲスト ユーザーは、ディレクトリ アクセス許可を制限されています。 自分のプロファイルの管理や自分のパスワードの変更、他のユーザー、グループ、アプリに関する情報の取得を行うことができますが、すべてのディレクトリ情報を読み取ることはできません。 たとえば、ゲスト ユーザーは、ユーザー、グループ、およびその他のディレクトリ オブジェクトすべてが含まれる一覧を列挙できません。 ゲストを管理者ロールに追加することができ、追加すると、ロールに含まれる読み取りと書き込みのすべてのアクセス許可が付与されます。 また、ゲストは他のゲストを招待することもできます。

## <a name="compare-member-and-guest-default-permissions"></a>メンバーとゲストの既定のアクセス許可を比較する

**領域** | **メンバー ユーザーのアクセス許可** | **既定のゲスト ユーザーのアクセス許可** | **制限されたゲスト ユーザーのアクセス許可 (プレビュー)**
------------ | --------- | ---------- | ----------
ユーザーと連絡先 | <ul><li>すべてのユーザーと連絡先の一覧を列挙する<li>ユーザーと連絡先のすべてのパブリック プロパティを読み取る</li><li>ゲストを招待する<li>自分のパスワードを変更する<li>自分の携帯電話番号を管理する<li>自分の写真を管理する<li>自分の更新トークンを無効にする</li></ul> | <ul><li>自分のプロパティを読み取る<li>他のユーザーと連絡先の表示名、メール アドレス、サインイン名、写真、ユーザー プリンシパル名、ユーザーの種類の各プロパティを読み取る<li>自分のパスワードを変更する<li>別のユーザーを ObjectId で検索する (許可されている場合)<li>他のユーザーの上司と直属の部下の情報を読み取る</li></ul> | <ul><li>自分のプロパティを読み取る<li>自分のパスワードを変更する</li></ul>
グループ | <ul><li>セキュリティ グループを作成する<li>Microsoft 365 グループを作成する<li>すべてのグループの一覧を列挙する<li>グループのすべてのプロパティを読み取る<li>非表示でないグループのメンバーシップを読み取る<li>参加しているグループで非表示にされている Microsoft 365 グループのメンバーシップを読み取る<li>ユーザーが所有するグループのプロパティ、所有権、メンバーシップを管理する<li>所有するグループにゲストを追加する<li>動的メンバーシップの設定を管理する<li>所有するグループを削除する<li>所有する Microsoft 365 グループを復元する</li></ul> | <ul><li>非表示でないグループ (不参加のグループも含む) のプロパティ (メンバーシップや所有権など) を読み取る<li>参加しているグループで非表示にされている Microsoft 365 グループのメンバーシップを読み取る<li>表示名または ObjectId でグループを検索する (許可されている場合)</li></ul> | <ul><li>参加しているグループのオブジェクト ID を読み取る<li>一部の Microsoft 365 アプリで参加しているグループのメンバーシップと所有権を読み取る (許可されている場合)</li></ul>
アプリケーション | <ul><li>新しいアプリケーションを登録 (作成) する<li>すべてのアプリケーションの一覧を列挙する<li>登録済みアプリケーションとエンタープライズ アプリケーションのプロパティを読み取る<li>所有するアプリケーションのプロパティ、割り当て、資格情報を管理する<li>ユーザーのアプリケーション パスワードを作成または削除する<li>所有するアプリケーションを削除する<li>所有するアプリケーションを復元する</li></ul> | <ul><li>登録済みアプリケーションとエンタープライズ アプリケーションのプロパティを読み取る</li></ul> | <ul><li>登録済みアプリケーションとエンタープライズ アプリケーションのプロパティを読み取る
デバイス</li></ul> | <ul><li>すべてのデバイスの一覧を列挙する<li>デバイスのすべてのプロパティを読み取る<li>所有するデバイスのすべてのプロパティを管理する</li></ul> | アクセス許可なし | アクセス許可なし
ディレクトリ | <ul><li>会社のすべての情報を読み取る<li>すべてのドメインを読み取る<li>すべてのパートナー契約を読み取る</li></ul> | <ul><li>会社の表示名を読み取る<li>すべてのドメインを読み取る</li></ul> | <ul><li>会社の表示名を読み取る<li>すべてのドメインを読み取る</li></ul>
ロールとスコープ | <ul><li>すべての管理者ロールとメンバーシップを読み取る<li>管理単位のすべてのプロパティとメンバーシップを読み取る</li></ul> | アクセス許可なし | アクセス許可なし
サブスクリプション | <ul><li>すべてのサブスクリプションを読み取る<li>サービス プラン メンバーを有効にする</li></ul> | アクセス許可なし | アクセス許可なし
ポリシー | <ul><li>ポリシーのすべてのプロパティを読み取る<li>所有するポリシーのすべてのプロパティを管理する</li></ul> | アクセス許可なし | アクセス許可なし

## <a name="restrict-member-users-default-permissions"></a>メンバー ユーザーの既定のアクセス許可を制限する 

メンバー ユーザーの既定のアクセス許可は、次の方法で制限できます。

権限 | 設定の説明
---------- | ------------
ユーザーはアプリケーションを登録できる | このオプションを [いいえ] に設定すると、ユーザーはアプリケーション登録を作成できません。 この場合、特定のユーザーをアプリケーション開発者ロールに追加することで、そのユーザーにこの機能を付与できます。
ユーザーが LinkedIn で職場または学校アカウントに接続できるようにする | このオプションを [いいえ] に設定すると、ユーザーは、自身の LinkedIn アカウントで職場または学校のアカウントに接続できなくなります。 詳細については、「[LinkedIn アカウント接続のデータ共有と同意](../enterprise-users/linkedin-user-consent.md)」を参照してください。
セキュリティ グループを作成できる | このオプションを [いいえ] に設定すると、ユーザーはセキュリティ グループを作成できません。 その場合でも、全体管理者とユーザー管理者はセキュリティ グループを作成できます。 方法については、「[グループの設定を構成するための Azure Active Directory コマンドレット](../enterprise-users/groups-settings-cmdlets.md)」をご覧ください。
Microsoft 365 グループを作成する機能 | このオプションを [いいえ] に設定すると、ユーザーは Microsoft 365 グループを作成できません。 このオプションを [一部] に設定すると、選ばれたユーザーのセットは Microsoft 365 グループを作成できます。 その場合でも、全体管理者とユーザー管理者は Microsoft 365 グループを作成できます。 方法については、「[グループの設定を構成するための Azure Active Directory コマンドレット](../enterprise-users/groups-settings-cmdlets.md)」をご覧ください。
Azure AD 管理ポータルへのアクセスを制限する | このオプションを [いいえ] に設定すると、管理者以外の管理者が Azure AD 管理ポータルを使用して Azure AD リソースの読み取りと管理を行うことができます。 [はい] の場合、管理者以外はすべて、管理ポータルでの Azure AD データへのアクセスが制限されます。<p>**注**: この設定では、PowerShell または他のクライアント (Visual Studio など) を使用した Azure AD データへのアクセスは制限されません。[はい] に設定した場合、特定の非管理者ユーザーに Azure AD 管理ポータルを使用する権限を付与するには、ディレクトリ閲覧者ロールなどの管理ロールを割り当ててください。<p>このロールでは、メンバー ユーザーが既定で所有している (ゲストとサービス プリンシパルは所有していない) 基本的なディレクトリ情報を読み取ることができます。
他のユーザーを読み取ることができる | この設定は PowerShell のみでご利用いただけます。 このフラグを $false に設定すると、管理者以外のすべてのユーザーはディレクトリからユーザー情報を読み取ることができなくなります。 Exchange Online などの他の Microsoft サービスのユーザー情報の読み取りは妨げられません。 この設定は特殊な状況を想定しているため、このフラグを $false に設定することは推奨されません。

## <a name="restrict-guest-users-default-permissions"></a>ゲスト ユーザーの既定のアクセス許可を制限する

ゲスト ユーザーの既定のアクセス許可は、次の方法で制限できます。

>[!NOTE]
>[Guests user access restrictions]\(ゲスト ユーザーのアクセス制限\) 設定は、 **[ゲストのアクセス許可を制限する]** 設定に置き換わりました。 この機能の使用に関するガイダンスについては、「[Azure Active Directory でゲストのアクセス許可を制限する (プレビュー)](../enterprise-users/users-restrict-guest-permissions.md)」を参照してください。

権限 | 設定の説明
---------- | ------------
Guests user access restrictions (Preview) (ゲスト ユーザーのアクセス制限 (プレビュー)) | このオプションを **[Guest users have the same access as members]\(ゲスト ユーザーにメンバーと同じアクセス権を付与する\)** に設定すると、メンバー ユーザーのアクセス許可すべてがゲスト ユーザーに既定で付与されます。<p>このオプションを **[Guest user access is restricted to properties and memberships of their own directory objects]\(ゲスト ユーザーのアクセスを、自分のディレクトリ オブジェクトのプロパティとメンバーシップに制限する\)** に設定すると、ゲスト アクセスは既定で自分のユーザー プロファイルのみに制限されます。 ユーザー プリンシパル名、ObjectId、または表示名で検索する場合でも、他のユーザーへのアクセスは許可されなくなりました。 グループ メンバーシップを含むグループ情報へのアクセスも許可されなくなりました。<p>**注**:この設定では、Microsoft Teams など、一部の Microsoft 365 サービスの参加しているグループへのアクセスは禁止されません。 詳細については、[Microsoft Teams のゲストのアクセス](/MicrosoftTeams/guest-access)に関する記事を参照してください。<p>このアクセス許可の設定に関係なく、ゲスト ユーザーを管理者の役割に追加できます。
ゲストは招待ができる | このオプションを [はい] に設定すると、ゲストは他のゲストを招待できます。 詳しくは、[B2B コラボレーションの招待の委任](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings)に関する記事を参照してください。
メンバーは招待ができる | このオプションを [はい] に設定すると、自分のディレクトリの管理者以外のメンバーがゲストを招待できるようになります。 詳しくは、[B2B コラボレーションの招待の委任](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings)に関する記事を参照してください。
管理者とゲスト招待元ロールのユーザーは招待ができる | このオプションを [はい] に設定すると、管理者と "ゲスト招待元" ロールのユーザーがゲストを招待できます。 [はい] に設定すると、[メンバーは招待ができる] 設定に関係なく、ゲスト招待元ロールのユーザーはゲストを招待できます。 詳しくは、[B2B コラボレーションの招待の委任](../external-identities/delegate-invitations.md#assign-the-guest-inviter-role-to-a-user)に関する記事を参照してください。

## <a name="object-ownership"></a>オブジェクトの所有権

### <a name="application-registration-owner-permissions"></a>アプリケーション登録所有者のアクセス許可
ユーザーがアプリケーションを登録すると、そのユーザーはアプリケーションの所有者として自動的に追加されます。 所有者は、名前やアプリが要求するアクセス許可など、アプリケーションのメタデータを管理できます。 また、SSO の構成やユーザーの割り当てなど、アプリケーションのテナント固有の構成も管理できます。 所有者は、他の所有者を追加または削除することもできます。 全体管理者とは異なり、所有者は、自分が所有するアプリケーションのみを管理できます。

### <a name="enterprise-application-owner-permissions"></a>エンタープライズ アプリケーション所有者のアクセス許可
ユーザーがエンタープライズ アプリケーションを追加すると、そのユーザーは自動的に所有者として追加されます。 所有者は、SSO の構成、プロビジョニング、ユーザーの割り当てなど、アプリケーションのテナント固有の構成を管理できます。 所有者は、他の所有者を追加または削除することもできます。 全体管理者とは異なり、所有者は、自分が所有するアプリケーションのみを管理できます。

### <a name="group-owner-permissions"></a>グループ所有者のアクセス許可
グループを作成したユーザーは、そのグループの所有者として自動的に追加されます。 所有者は、名前などのグループのプロパティおよびグループ メンバーシップを管理できます。 所有者は、他の所有者を追加または削除することもできます。 全体管理者およびユーザー管理者とは異なり、所有者が管理できるのは自分が所有するグループだけです。 グループ所有者の割り当てについては、「[グループの所有者の管理](active-directory-accessmanagement-managing-group-owners.md)」をご覧ください。

### <a name="ownership-permissions"></a>所有者のアクセス許可
以降の表では、Azure Active Directory メンバー ユーザーの特定のアクセス許可には所有するオブジェクトを超えるオブジェクトが含まれることを説明します。 ユーザーは、自分が所有するオブジェクトに対してのみこれらのアクセス許可を持ちます。

#### <a name="owned-application-registrations"></a>所有しているアプリケーションの登録
ユーザーは、所有するアプリケーションの登録で次のアクションを実行できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/applications/audience/update | Azure Active Directory での applications.audience property プロパティの更新。 |
| microsoft.directory/applications/authentication/update | Azure Active Directory での applications.authentication プロパティの更新。 |
| microsoft.directory/applications/basic/update | Azure Active Directory での applications の基本プロパティの更新。 |
| microsoft.directory/applications/credentials/update | Azure Active Directory での applications.credentials プロパティの更新。 |
| microsoft.directory/applications/delete | Azure Active Directory での applications の削除。 |
| microsoft.directory/applications/owners/update | Azure Active Directory での applications.owners プロパティの更新。 |
| microsoft.directory/applications/permissions/update | Azure Active Directory での applications.permissions プロパティの更新。 |
| microsoft.directory/applications/policies/update | Azure Active Directory での applications.policies プロパティの更新。 |
| microsoft.directory/applications/restore | Azure Active Directory での applications の復元。 |

#### <a name="owned-enterprise-applications"></a>所有するエンタープライズ アプリケーション
ユーザーは、所有するエンタープライズ アプリケーションで次のアクションを実行できます。 エンタープライズ アプリケーションは、サービス プリンシパル、1 つまたは複数のアプリケーション ポリシー、および場合によってはサービス プリンシパルと同じテナント内のアプリケーション オブジェクトで構成されます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory での auditLogs 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.directory/policies/basic/update | Azure Active Directory での policies の基本プロパティの更新。 |
| microsoft.directory/policies/delete | Azure Active Directory での policies の削除。 |
| microsoft.directory/policies/owners/update | Azure Active Directory での policies.owners プロパティの更新。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの更新。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory での users.appRoleAssignments プロパティの更新。 |
| microsoft.directory/servicePrincipals/audience/update | Azure Active Directory での servicePrincipals.audience プロパティの更新。 |
| microsoft.directory/servicePrincipals/authentication/update | Azure Active Directory での servicePrincipals.authentication プロパティの更新。 |
| microsoft.directory/servicePrincipals/basic/update | Azure Active Directory での servicePrincipals の基本プロパティの更新。 |
| microsoft.directory/servicePrincipals/credentials/update | Azure Active Directory での servicePrincipals.credentials プロパティの更新。 |
| microsoft.directory/servicePrincipals/delete | Azure Active Directory での servicePrincipals の削除。 |
| microsoft.directory/servicePrincipals/owners/update | Azure Active Directory での servicePrincipals.owners プロパティの更新。 |
| microsoft.directory/servicePrincipals/permissions/update | Azure Active Directory での servicePrincipals.permissions プロパティの更新。 |
| microsoft.directory/servicePrincipals/policies/update | Azure Active Directory での servicePrincipals.policies プロパティの更新。 |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory での signInReports 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |

#### <a name="owned-devices"></a>所有するデバイス
ユーザーは、所有するデバイスで次のアクションを実行できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Azure Active Directory での devices.bitLockerRecoveryKeys プロパティの読み取り。 |
| microsoft.directory/devices/disable | Azure Active Directory での devices の無効化。 |

#### <a name="owned-groups"></a>所有するグループ
ユーザーは、所有するグループで次のアクションを実行できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Azure Active Directory での groups.appRoleAssignments プロパティの更新。 |
| microsoft.directory/groups/basic/update | Azure Active Directory での groups の基本プロパティの更新。 |
| microsoft.directory/groups/delete | Azure Active Directory での groups の削除。 |
| microsoft.directory/groups/dynamicMembershipRule/update | Azure Active Directory での groups.dynamicMembershipRule プロパティの更新。 |
| microsoft.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.directory/groups/owners/update | Azure Active Directory での groups.owners プロパティの更新。 |
| microsoft.directory/groups/restore | Azure Active Directory での groups の復元。 |
| microsoft.directory/groups/settings/update | Azure Active Directory での groups.settings プロパティの更新。 |

## <a name="next-steps"></a>次のステップ

* ゲスト ユーザーのアクセス制限の設定の詳細については、「[Azure Active Directory でゲストのアクセス許可を制限する (プレビュー)](../enterprise-users/users-restrict-guest-permissions.md)」を参照してください。
* Azure AD の管理者ロールを割り当てる方法の詳細については、「[Azure Active Directory でユーザーを管理者ロールに割り当てる](active-directory-users-assign-role-azure-portal.md)」を参照してください
* Microsoft Azure でリソース アクセスを制御する方法の詳細については、「 [Azure でのリソース アクセスについて](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Azure Active Directory と Azure サブスクリプションの関係の詳細については、「 [Azure サブスクリプションを Azure Active Directory に関連付ける方法](active-directory-how-subscriptions-associated-directory.md)
* [ユーザーの管理](add-users-azure-active-directory.md)