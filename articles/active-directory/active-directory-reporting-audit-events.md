---
title: "Azure Active Directory 監査レポートのイベント | Microsoft Docs"
description: "Azure Active Directory から表示およびダウンロードできる監査対象イベントについて説明します。"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: mbaldwin
editor: 
ms.assetid: 307eedf7-05bc-448d-a84d-bead5a4c5770
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/14/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 6950072b8970dfc1b80674efe0be6cb7496fd5ce
ms.openlocfilehash: 2dda60160bbcaa729bc8e5cec4f47efc10d120cf


---
# <a name="azure-active-directory-audit-report-events"></a>Azure Active Directory 監査レポートのイベント
*このドキュメントは、[Azure Active Directory レポート ガイド](active-directory-reporting-guide.md)の一部です。*

Azure Active Directory 監査レポートを利用すると、Azure Active Directory で発生した特権アクションを識別できます。 特権アクションとしては、昇格の変更 (例: ロールの作成、パスワードのリセット)、ポリシー構成の変更 (例: パスワード ポリシー)、ディレクトリ構成の変更 (例: ドメインのフェデレーション設定の変更) などがあります。 レポートでは、イベント名、アクションを実行したアクター、変更によって影響を受けた対象リソース、日時 (UTC) に関する監査レコードが提供されます。 [監査ログの表示](active-directory-reporting-azure-portal.md)に関するページに説明されているように、ユーザーは、[Azure Portal](https://portal.azure.com/) を使用して Azure Active Directory に対する監査イベントのリストを取得できます。

## <a name="list-of-audit-report-events"></a>監査レポートのイベントの一覧
<!--- audit event descriptions should be in the past tense --->

| イベント | イベントの説明 |
| --- | --- |
| **ユーザー イベント** | |
| ユーザーの追加 |ディレクトリにユーザーが追加されました。 |
| ユーザーの削除 |ディレクトリからユーザーが削除されました。 |
| ライセンスのプロパティの設定 |ディレクトリでユーザーのライセンス プロパティが設定されました。 |
| ユーザー パスワードのリセット |ディレクトリでユーザーのパスワードがリセットされました。 |
| ユーザー パスワードの変更 |ディレクトリでユーザーのパスワードが変更されました。 |
| ユーザー ライセンスの変更 |ディレクトリでユーザーに割り当てられたライセンスが変更されました。 更新されたライセンスを確認するには、次の " [ユーザーの更新](#update-user-attributes) " プロパティを調べます。 |
| ユーザーの更新 |ディレクトリでユーザーが更新されました。 [以下を参照](#update-user-attributes) してください。 |
| ユーザー パスワードの強制変更の設定 |ログイン時にパスワードの変更をユーザーに強制するプロパティが設定されました。 |
| ユーザーの資格情報の更新 |ユーザーによってパスワードが変更されました。 |
| **グループ イベント** | |
| グループの追加 |ディレクトリ内にグループが作成されました。 |
| グループの更新 |ディレクトリ内のグループが更新されました。 更新されたグループ プロパティを確認するには、後の [監査対象のグループ プロパティ](#update-group-attributes) に関する説明を参照してください。 |
| グループの削除 |ディレクトリからグループが削除されました。 |
| CreateGroupSettings |グループ設定が作成されました。 |
| UpdateGroupSettings |グループ設定が更新されました。 更新されたグループ設定を確認するには、後述のセクションにある [監査対象のグループ プロパティ](#update-group-attributes) に関する説明を参照してください。 |
| DeleteGroupSettings |グループ設定が削除されました。 |
| SetGroupLicense |グループ ライセンスが設定されました。 |
| SetGroupManagedBy |ユーザーが管理するグループが設定されました。 |
| AddGroupMember |グループにメンバーが追加されました。 |
| RemoveGroupMember |グループからのメンバーの削除 |
| AddGroupOwner |グループに所有者が追加されました。 |
| RemoveGroupOwner |グループから所有者が削除されました。 |
| **アプリケーション イベント** | |
| サービス プリンシパルの追加 |ディレクトリにサービス プリンシパルが追加されました。 |
| サービス プリンシパルの削除 |ディレクトリからサービス プリンシパルが削除されました。 |
| サービス プリンシパルの資格情報の追加 |サービス プリンシパルに資格情報が追加されました。 |
| サービス プリンシパルの資格情報の削除 |サービス プリンシパルから資格情報が削除されました。 |
| 委任エントリの追加 |ディレクトリに [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) が作成されました。 |
| 委任エントリの設定 |ディレクトリの [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) が更新されました。 |
| 委任エントリの削除 |ディレクトリの [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) が削除されました。 |
| **ロール イベント** | |
| ロールへのロール メンバーの追加 |ディレクトリのロールにユーザーが追加されました。 |
| ロールからのロール メンバーの削除 |ディレクトリのロールからユーザーが削除されました。 |
| AddRoleDefinition |ロール定義が追加されました。 |
| UpdateRoleDefinition |ロール定義が更新されました。 更新されたロール設定を確認するには、後述のセクションにある [監査対象のロール定義プロパティ](#update-role-definition-attributes) に関する説明を参照してください。 |
| DeleteRoleDefinition |ロール定義が削除されました。 |
| AddRoleAssignmentToRoleDefinition |ロール定義にロール割り当てが追加されました。 |
| RemoveRoleAssignmentFromRoleDefinition |ロール定義からロール割り当てが削除されました。 |
| AddRoleFromTemplate |テンプレートからロールが追加されました。 |
| UpdateRole |ロールが更新されました。 |
| AddRoleScopeMemberToRole |ロールにスコープを持つメンバーが追加されました。 |
| RemoveRoleScopedMemberFromRole |スコープを持つメンバーがロールから削除されました。 |
| **デバイス イベント (すべて新しいイベント)** | |
| AddDevice |デバイスが追加されました。 |
| UpdateDevice |デバイスが更新されました。 更新されたデバイス プロパティを確認するには、後述のセクションにある [監査対象のデバイス プロパティ](#update-device-attributes) に関する説明を参照してください。 |
| DeleteDevice |デバイスが削除されました。 |
| AddDeviceConfiguration |デバイス構成が追加されました。 |
| UpdateDeviceConfiguration |デバイス構成が更新されました。 更新されたデバイス構成プロパティを確認するには、後述のセクションにある [監査対象のデバイス構成プロパティ](#update-device-configuration-attributes) に関する説明を参照してください。 |
| DeleteDeviceConfiguration |デバイス構成が削除されました。 |
| AddRegisteredOwner |デバイスに登録済み所有者が追加されました。 |
| AddRegisteredUsers |デバイスに登録済みユーザーが追加されました。 |
| RemoveRegisteredOwner |デバイスから登録済み所有者を削除します。 |
| RemoveRegisteredUsers |デバイスから登録済みユーザーを削除します。 |
| RemoveDeviceCredentials |デバイスの資格情報を削除します。 |
| **B2B イベント** | |
| バッチ招待がアップロードされました。 |管理者が、パートナー ユーザーに送信する複数の招待を含むファイルをアップロードしました。 |
| バッチ招待が処理されました。 |パートナー ユーザーに対する招待を含むファイルが処理されました。 |
| 外部ユーザーを招待します。 |外部ユーザーがディレクトリに招待されました。 |
| 外部ユーザーの招待を利用します。 |外部ユーザーは、ディレクトリへの招待を利用しました。 |
| 外部ユーザーをグループに追加します。 |外部ユーザーには、ディレクトリ内のグループのメンバーシップが割り当てられました。 |
| 外部ユーザーをアプリケーションに割り当てます。 |外部ユーザーには、アプリケーションへの直接アクセスが割り当てられました。 |
| バイラル テナントの作成。 |招待の利用によって、Azure AD に新しいテナントが作成されました。 |
| バイラル ユーザーの作成。 |招待の利用によって、Azure AD の既存のテナントにユーザーが作成されました。 |
| **管理単位 (すべて新しいイベント)** | |
| AddAdministrativeUnit |管理単位を追加します。 |
| UpdateAdministrativeUnit |管理単位を更新します。 更新された管理単位を確認するには、後述のセクションにある [監査対象の管理単位プロパティ](#update-administrative-unit-attributes) に関する説明を参照してください。 |
| DeleteAdministrativeUnit |管理単位を削除します。 |
| AddMemberToAdministrativeUnit |管理単位にメンバーを追加します。 |
| RemoveMemberFromAdministrativeUnit |管理単位からメンバーを削除します。 |
| **ディレクトリ イベント** | |
| 会社へのパートナーの追加 |ディレクトリにパートナーが追加されました。 |
| 会社からのパートナーの削除 |ディレクトリからパートナーが削除されました。 |
| DemotePartner |パートナーを降格します。 |
| 会社へのドメインの追加 |ディレクトリにドメインが追加されました。 |
| 会社からのドメインの削除 |ディレクトリからドメインが削除されました。 |
| ドメインの更新 |ディレクトリでドメインが更新されました。 更新されたドメイン プロパティを確認するには、後述のセクションにある [監査対象のデバイス プロパティ](#update-domain-attributes) に関する説明を参照してください。 |
| ドメインの認証の設定 |会社の既定のドメイン設定が変更されました。 |
| 会社の連絡先情報の設定 |会社レベルで優先連絡先が設定されました。 これには、マーケティングに対する電子メール アドレスおよび Microsoft オンライン サービスに関する技術的通知が含まれます。 |
| ドメインのフェデレーションの設定 |ドメインのフェデレーション設定が更新されました。 |
| ドメインの検証 |ディレクトリのドメインが検証されました。 |
| 電子メール検証済みドメインの検証 |電子メールの検証を使用してディレクトリのドメインが検証されました。 |
| 会社への DirSyncEnabled フラグの設定 |ディレクトリの Azure AD Sync を有効にするプロパティが設定されました。 |
| パスワード ポリシーの設定 |ユーザーのパスワードの長さと文字の制約が設定されました。 |
| 会社情報の設定 |会社レベルの情報が更新されました。 詳細については、 [Get-MsolCompanyInformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) PowerShell コマンドレットを参照してください。 |
| SetCompanyAllowedDataLocation |会社で許可されるデータの場所が設定されました。 |
| SetCompanyDirSyncEnabled |DirSyncEnabled フラグが設定されました。 |
| SetCompanyDirSyncFeature |DirSync 機能が設定されました。 |
| SetCompanyInformation |会社情報が設定されました。 |
| SetCompanyMultiNationalEnabled |会社の多国対応機能が有効に設定されました。 |
| SetDirectoryFeatureOnTenant |テナントでディレクトリ機能が設定されました。 |
| SetTenantLicenseProperties |テナントのライセンス プロパティが設定されました。 |
| CreateCompanySettings |会社の設定を作成します。 |
| UpdateCompanySettings |会社の設定を更新します。 更新された会社プロパティを確認するには、後述のセクションにある [監査対象の会社プロパティ](#update-company-attributes) に関する説明を参照してください。 |
| DeleteCompanySettings |会社の設定を削除します。 |
| SetAccidentalDeletionThreshold |誤削除のしきい値を設定します。 |
| SetRightsManagementProperties |権限管理プロパティを設定します。 |
| PurgeRightsManagementProperties |権限管理プロパティを消去します。 |
| UpdateExternalSecrets |外部シークレットを更新します。 |
| **ポリシー イベント (すべて新しいイベント)** | |
| AddPolicy |ポリシーを追加します。 |
| UpdatePolicy |ポリシーを更新します。 |
| DeletePolicy |ポリシーを削除します。 |
| AddDefaultPolicyApplication |アプリケーションにポリシーを追加します。 |
| AddDefaultPolicyServicePrincipal |サービス プリンシパルにポリシーを追加します。 |
| RemoveDefaultPolicyApplication |アプリケーションからポリシーを削除します。 |
| RemoveDefaultPolicyServicePrincipal |サービス プリンシパルからポリシーを削除します。 |
| RemovePolicyCredentials |ポリシー資格情報を削除します。 |

## <a name="audit-report-retention"></a>監査レポートの保持
Azure AD の監査レポートのイベントは、180 日間保持されます。 レポートの保持の詳細については、「 [Azure Active Directory Report Retention Policies (Azure Active Directory レポートの保持ポリシー)](active-directory-reporting-retention.md)」をご覧ください。

監査イベントを長期間保存する目的のユーザーの場合は、Reporting API を使用して監査イベントを定期的に別のデータ ストアにプルできます。 詳細については、「 [Getting Started with the Reporting API (Reporting API の概要)](active-directory-reporting-api-getting-started.md) 」をご覧ください。

## <a name="properties-included-with-each-audit-event"></a>各監査イベントに含まれるプロパティ
| プロパティ | Description |
| --- | --- |
| 日付と時刻 |監査イベントが発生した日時です |
| アクター |アクションを実行したユーザーまたはサービス プリンシパルです |
| アクション |実行されたアクションです |
| ターゲット |アクションの実行対象になったユーザーまたはサービス プリンシパルです |

## <a name="update-user-attributes"></a>「ユーザーの更新」属性
「ユーザーの更新」監査イベントには、更新されたユーザー属性に関する追加情報が含まれます。 各属性について、更新前の値と新しい値の両方が含まれます。

| 属性 | Description |
| --- | --- |
| AccountEnabled |ユーザーはサインインできます。 |
| AssignedLicense |ユーザーに割り当てられているすべてのライセンスです。 |
| AssignedPlan |ユーザーに割り当てられたライセンスによるサービス プランです。 |
| LicenseAssignmentDetail |ユーザーに割り当てられているライセンスの詳細です。 たとえば、グループ ベースのライセンスが含まれていた場合、これにはライセンスを許可したグループが含まれます。 |
| モバイル |ユーザーの携帯電話の番号です。 |
| OtherMail |ユーザーの代替電子メール アドレスです。 |
| OtherMobile |ユーザーの代替携帯電話番号です。 |
| StrongAuthenticationMethod |ユーザーによって構成された Multi-Factor Authentication の検証方法のリストです。音声通話、SMS、モバイル アプリからの検証コードなど。 |
| StrongAuthenticationRequirement |このユーザーに対して、Multi-Factor Authentication が適用されている、有効になっている、無効になっているかどうかです。 |
| StrongAuthenticationUserDetails |Multi-Factor Authentication およびパスワード リセットの検証に使用されるユーザーの電話番号、代替電話番号、電子メール アドレスです。 |
| StrongAuthenticationPhoneAppDetail |2FA ログインを実行するために登録された電話アプリの詳細です。 |
| TelephoneNumber |ユーザーの電話番号です。 |
| AlternativeSecurityId |オブジェクトの代替セキュリティ ID です。 |
| CreationType |ユーザーの作成方法です (招待またはバイラル)。 |
| InviteTicket |ユーザーの招待チケットの一覧です。 |
| InviteReplyUrl |招待の承諾時に返信するための URL の一覧です。 |
| InviteResources |ユーザーが招待されたリソースの一覧です。 |
| LastDirSyncTime |優先する (顧客、オンプレミス) ディレクトリからの同期によってオブジェクトが最後に更新された時刻です。 |
| MSExchRemoteRecipientType |MSO の受信者の種類にマップされます。 受信者の種類については、[MSO の受信者の種類] https://msdn.microsoft.com/library/microsoft.office.interop.outlook.recipient.type.aspx を参照してください。 |
| PreferredDataLocation |ユーザー、グループ、連絡先、パブリック フォルダー、またはデバイスのデータの推奨される場所です。 |
| ProxyAddresses |Exchange Server 受信者オブジェクトが外部メール システムで認識されるアドレスです。 |
| StsRefreshTokensValidFrom |更新トークンの失効情報を保持します - この時間の前に発行されたすべての STS 更新トークンは期限切れと見なされます。 |
| UserPrincipalName |ユーザーのインターネット スタイルのログオン名である UPN です。 |
| UserState |ユーザーの状態 (PendingApproval、PendingAcceptance、Accepted、PendingVerification) です。 |
| UserStateChangedOn |UserState の最終変更のタイムスタンプです。 ライフサイクル ワークフローをトリガーするために使用されます。 |
| UserType |ユーザーの種類です。 メンバー (0)、ゲスト (1)、バイラル (2)。 |

## <a name="update-group-attributes"></a>"グループの更新" 属性
| 属性 | Description |
| --- | --- |
| 分類 |統合グループの分類です (HBI、MBI など)。 |
| Description |人間が判読できる、オブジェクトに関する説明的な記述です。 |
| DisplayName |オブジェクトの表示名です。 |
| DirSyncEnabled |優先する (顧客、オンプレミス) ディレクトリから同期が行われるかどうかを示します。 |
| GroupLicenseAssignment |グループのライセンスの割り当てです。 |
| GroupType |グループの種類。統合 (0) |
| IsMembershipRuleLocked |MembershipRule プロパティがセルフサービス グループ管理サービスによって設定され、ユーザーが変更できないことを示します。 GroupType に GroupType.DynamicMembership が含まれるグループのみに適用されます。 |
| IsPublic |グループがパブリックとプライベートのどちらであるかを示すフラグです。 |
| LastDirSyncTime |優先する (顧客、オンプレミス) ディレクトリからの同期の結果としてオブジェクトが最後に更新された時刻です。 |
| Mail |プライマリ電子メール アドレスです。 |
| MailEnabled |このグループにメール機能があるかどうかを示します。 |
| MailNickname |アドレス帳オブジェクトのモニカーです。通常は、電子メール名の ""@"" 記号の前の部分です。 |
| MembershipRule |このグループに属する必要があるメンバーを判定するためにセルフサービス グループ管理サービスによって使用される条件を表す文字列です。 IsMembershipRuleLocked も参照してください。 GroupType に GroupType.DynamicMembership が含まれるグループのみに適用されます。 |
| MembershipRuleProcessingState |セルフサービス グループ管理サービスによって定義された、このグループのメンバーシップ処理の状態を定義する enum 値。 GroupType に GroupType.DynamicMembership が含まれるグループのみに適用されます。 |
| ProxyAddresses |Exchange Server 受信者オブジェクトが外部メール システムで認識されるアドレスです。 |
| RenewedDateTime |グループが最後に更新された日時のタイムスタンプ レコードです。 |
| SecurityEnabled |このグループのメンバーシップが承認決定に影響を与える可能性があるかどうかを示します。 |
| WellKnownObject |事前に定義されたセットのいずれかとして、ディレクトリ オブジェクトにラベルを付けます。 |

## <a name="update-device-attributes"></a>"デバイスの更新" 属性
| 属性 | Description |
| --- | --- |
| AccountEnabled |セキュリティ プリンシパルを認証できるかどうかを示します。 |
| CloudAccountEnabled |セキュリティ プリンシパルを認証できるかどうかを示します。 デバイスがオンプレミスで管理されているときに InTune によって書き込まれます。 |
| CloudDeviceOSType |OS (Windows RT、iOS など) をベースとしたデバイスの種類です。 クラウド サービス (Intune など) によって設定されると、この属性はディレクトリで DeviceOSType に対して優先されます。 |
| CloudDeviceOSVersion |OS のバージョンです。 クラウド サービス (Intune など) によって設定されると、この属性はディレクトリで DeviceOSVersion に対して優先されます。 |
| CloudDisplayName |DisplayName LDAP 属性の値です。 クラウド サービス (Intune など) によって設定されると、この属性はディレクトリで displayName に対して優先されます。 |
| CloudCreated |オブジェクトがクラウド サービスによって作成されたかどうかを示します。 |
| CompliantUntil |デバイスがどの時点まで準拠デバイスと見なされるかを示します。 |
| DeviceMetadata |デバイスのカスタム メタデータです。 |
| DeviceObjectVersion |この属性は、デバイスのスキーマ バージョンを識別するために使用されます。 |
| DeviceOSType |OS (Windows RT、iOS など) をベースとしたデバイスの種類です。 登録サービスによって書き込まれ、MDM 管理サービスまたは STS 権限管理サービスによって更新されます。 |
| DeviceOSVersion |OS のバージョンです。 登録サービスによって書き込まれ、MDM 管理サービスまたは STS 権限管理サービスによって更新されます。 |
| DevicePhysicalIds |物理デバイスの識別子を格納するための複数値属性です。 この属性には、BIOS ID、TPM 拇印、ハードウェア固有の ID などが含まれます。 |
| DirSyncEnabled |優先する (顧客、オンプレミス) ディレクトリから行われたかどうかを示します。 |
| DisplayName |オブジェクトの表示名です。 |
| IsCompliant |この属性は、デバイスのモバイル デバイス管理状態を管理するために使用します。 |
| IsManaged |この属性は、デバイスがクラウド MDM によって管理されることを示すために使用します。 |
| LastDirSyncTime |オブジェクトが権限のある (顧客、オンプレミス) ディレクトリからの同期によって最後に更新された時刻です。 |

## <a name="update-device-configuration-attributes"></a>"デバイスの構成の更新" 属性
| 属性 | Description |
| --- | --- |
| MaximumRegistrationInactivityPeriod |デバイスが非アクティブ状態を継続できる最大日数です。この日数を経過すると、デバイスは削除されたと見なされます。 |
| RegistrationQuota |1 人のユーザーに許可するデバイス登録数を制限するためのポリシーです。 |

## <a name="update-service-principal-configuration-attributes"></a>"サービス プリンシパル構成の更新" 属性
| 属性 | Description |
| --- | --- |
| AccountEnabled |セキュリティ プリンシパルを認証できるかどうかを示します。 |
| AppPrincipalId |セキュリティ プリンシパルの外部のアプリケーション定義の ID です。 |
| DisplayName |オブジェクトの表示名です。 |
| ServicePrincipalName |"名前/権限" を含むサービス プリンシパル名です。"名前" は、アプリケーション クラス値を指定します。"権限" には、"ホスト名[:ポート]" か、またはサービス プリンシパルの識別子を指定する "名前" が格納されます。 |

## <a name="update-app-attributes"></a>"アプリケーションの更新" 属性
| 属性 | Description |
| --- | --- |
| AppAddress |サービス プリンシパルに割り当てられた (リダイレクト URL) のアドレスのセットです。 |
| AppId |アプリケーションのアプリケーション ID です。 |
| AppIdentifierUri |アプリケーションを識別するアプリケーション URI です。  通常は、アプリケーション アクセス URL です。 |
| AppLogoUrl |CDN に格納されているアプリケーションのロゴ画像の URL です。 |
| AvailableToOtherTenants |アプリケーションがマルチテナント アプリケーション (つまり、他のテナントで使用可能) の場合は true です。 |
| DisplayName |アプリケーション名の表示名です。 |
| Entitlement |アプリケーションの権限の一覧です。 |
| ExternalUserAccountDelegationsAllowed |リソース アプリケーションが信頼されているアプリケーションであり、外部ユーザー アカウントのための委任エントリを作成できるかどうかを示すフラグです。 |
| GroupMembershipClaims |グループ メンバーシップ要求ポリシーです。 |
| PublicClient |クライアントがシークレットを保持できない (つまり OAuth2.0 の非機密クライアント) 場合は true です。 |
| RecordConsentConditions |契約条項で規定された同意条件の種類です (なし (0)、SilentConsentForPartnerManagedApp (1))。 この値は Graph API スキーマで公開され、テナント管理者のみが設定または変更できます。 |
| RequiredResourceAccess |RequiredResourceAccess プロパティの値の XML コンテンツです。 |
| WebApp |true の場合、このアプリケーションが Web アプリであることを示します。 |
| WwwHomepage |プライマリ Web ページです。 |

## <a name="update-role-attributes"></a>"ロールの更新" 属性
| 属性 | Description |
| --- | --- |
| AppAddress |サービス プリンシパルに割り当てられた (リダイレクト URL) のアドレスのセットです。 |
| BelongsToFirstLoginObjectSet |true の場合、このオブジェクトが新しいテナントの最初の管理者のログインを有効にするために必要なオブジェクトのセットに属していることを示します。 |
| Builtin |オブジェクトの有効期間がシステムによって所有されているかどうかを示します。 |
| Description |人間が判読できる、オブジェクトに関する説明的な記述です。 |
| DisplayName |オブジェクトの表示名です。 |
| MailNickname |アドレス帳オブジェクトのモニカーです。通常は、電子メール名の ""@"" 記号の前の部分です。 |
| RoleDisabled |アクセス チェックの目的でロールを無視する必要があるかどうかを示します。 |
| RoleTemplateId |ロール テンプレートの ID です。 |
| ServiceInfo |サービスに固有のプロビジョニング情報です。この情報は、MOAC または (同じまたは異なるサービスの種類の) 他のサービス インスタンスによって使用される可能性があります。 |
| TaskSetScopeReference |ロールまたはロール テンプレートに関連付けられているタスク セットとスコープのセットを識別します。 |
| ValidationError |解決が必要な、プロパティまたはオブジェクト管理者によるアクションからのリンクに関する、一時的でないサービスに固有のエラーが記述された、フェデレーション サービスによって公開された情報です。 |
| WellKnownObject |事前に定義されたセットのいずれかとして、ディレクトリ オブジェクトにラベルを付けます。 |

## <a name="update-role-definition-attributes"></a>"ロール定義の更新" 属性
| 属性 | Description |
| --- | --- |
| AssignableScopes |この RoleDefinition をセキュリティ プリンシパルに割り当てるときに参照できる承認スコープのコレクションです。 |
| DisplayName |オブジェクトの表示名です。 |
| GrantedPermissions |この RoleDefinition によって付与された権限です。 |

## <a name="update-administrative-unit-attributes"></a>"管理単位の更新" 属性
| 属性 | Description |
| --- | --- |
| Description |このプロパティは、管理単位の説明を変更すると更新されます。 |
| DisplayName |このプロパティは、管理単位の名前を変更すると更新されます。 |

## <a name="update-company-attributes"></a>"会社の更新" 属性
| 属性 | Description |
| --- | --- |
| AllowedDataLocation |会社のユーザーをプロビジョニングできる場所です。 |
| AuthorizedServiceInstance |プランのデプロイ先となるサービス インスタンスの名前です。 |
| DirSyncEnabled |優先する (顧客、オンプレミス) ディレクトリから行われたかどうかを示します。 |
| DirSyncStatus |このテナントのコンテキストでアドレス帳オブジェクトの同期が権限のある (顧客、オンプレミス) ディレクトリから行われるかどうかを示します。Company オブジェクトの DirSyncEnabled プロパティの拡張です。 |
| DirSyncFeatures |テナントの有効および無効な DirSync 機能のセットを追跡するビット フラグです。 |
| DirectoryFeatures |有効および無効なディレクトリ機能です。 |
| DirSyncConfiguration |現在のテナントに固有の DirSync 構成すべてを含みます。 |
| DisplayName |オブジェクトの表示名です。 |
| IsMnc |ブール型のフラグです。会社の多国対応機能が有効な会社の場合は "true" に設定されます。 |
| ObjectSettings |オブジェクトのスコープに適用可能な設定のコレクションです。 |
| PartnerCommerceUrl |パートナーの商業サイトの URL です。 |
| PartnerHelpUrl |パートナーのヘルプ サイトの URL です。 |
| PartnerSupportEmail |パートナーのサポート電子メールの URL です。 |
| PartnerSupportTelephone |パートナーのサポート電話の URL です。 |
| PartnerSupportUrl |パートナーのサポート サイトの URL です。 |
| StrongAuthenticationDetails |StrongAuthentication に関連する詳細です。 |
| StrongAuthenticationPolicy |会社の強力な認証ポリシーです。 |
| TechnicalNotificationMail |会社に関連する技術的な問題を報告するための電子メール アドレスです。 |
| TelephoneNumber |ITU 勧告 E.123 に準拠した電話番号です。 |
| TenantType |テナントの種類です。 この値が指定されていない場合、テナントは Company です。 それ以外の場合、使用可能な値は MicrosoftSupport (0)、SyndicatePartner (1)、BreadthPartner (2)、BreadthPartnerDelegatedAdmin (3)、ResellerPartnerDelegatedAdmin (4)、または ValueAddedResellerPartnerDelegatedAdmin (5) です。 |
| VerifiedDomain |Company にバインドされた DNS ドメイン名のセットです。 |

## <a name="update-domain-attributes"></a>"ドメインの更新" 属性
| 属性 | Description |
| --- | --- |
| 機能 |ドメインの機能を記述するビット フラグです (存在する場合)。 |
| 既定値 |ドメインが既定値であるかどうかを示します (たとえば、管理者が MOAC に新しいユーザーを作成したときの既定の UserPrincipalName サフィックス)。 |
| Initial |ドメインが、OCP によって割り当てられた、会社の初期ドメインであるかどうかを示します。 初期ドメインは、Microsoft Online ドメインの一意のサブドメインです (たとえば、contoso3.microsoftonline.com)。 |
| LiveType |対応する Windows Live 名前空間の種類 (存在する場合) です。 |
| 名前 |エンドポイントの ID です。 |
| PasswordNotificationWindowDays |パスワードの有効期限が切れることを何日前からユーザーに通知するかを示す日数です。 |
| PasswordValidityPeriodDays |パスワードの有効日数です。この日数に達すると、パスワードを変更する必要があります。 |

監査レコードは、多くの法令遵守に必要な管理です。 Azure Active Directory 監査レポートを使用してコンプライアンス規制に対応するお客様の場合、お客様がエクスポートした監査レポートのコピーにこのヘルプ トピックのコピーを添えて提出し、レポートの詳細な説明に役立てることをお勧めします。 Azure が現在を満たしているコンプライアンス規制についての情報を監査担当者が必要としている場合は、監査担当者に Microsoft Azure セキュリティ センターの [コンプライアンス ページ](https://azure.microsoft.com/support/trust-center/compliance/) を紹介してください。




<!--HONumber=Feb17_HO3-->


