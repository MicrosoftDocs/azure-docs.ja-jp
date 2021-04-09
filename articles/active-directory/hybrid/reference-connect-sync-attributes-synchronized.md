---
title: Azure AD Connect によって同期される属性 | Microsoft Docs
description: Azure Active Directory に同期される属性の一覧を示します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ec05c4160c6502904644bf7035bda0bed66cc33
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94413192"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Azure AD Connect 同期: Azure Active Directory に同期される属性
このトピックでは、Azure AD Connect Sync によって同期される属性の一覧を示します。  
属性は、関連する Azure AD アプリによってグループ化されます。

## <a name="attributes-to-synchronize"></a>同期する属性
よく寄せられる質問に、"*同期させる最低限の属性のリストは何か*" というものがあります。 既定の推奨されるアプローチは、クラウドに完全な GAL (グローバル アドレス一覧) を構築できるように既定の属性を保持し、さらに、Microsoft 365 ワークロードにすべての機能を取得することです。 場合によっては、次の例のように、属性に機密性の高い個人データが含まれるために、組織がクラウドとの属性の同期を望まないことがあります。  
![使用しない方がよい属性](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

この場合、このトピックの属性の一覧をまず確認し、個人データが含まれる同期できない属性を特定します。 次に、インストール時に、[Azure AD アプリと属性フィルター](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering)を使用して、それらの属性の選択を解除します。

> [!WARNING]
> 属性の選択を解除するときは、注意が必要であり、絶対に同期できない属性だけを選択解除する必要があります。 その他の属性の選択を解除すると、機能に悪影響を及ぼす可能性があります。
>
>

## <a name="microsoft-365-apps-for-enterprise"></a>Microsoft 365 Apps for enterprise
| 属性名 | User | 解説 |
| --- |:---:| --- |
| accountEnabled |X |アカウントが有効な場合に定義します。 |
| cn |X | |
| displayName |X | |
| objectSID |X |機械的なプロパティ。 Azure AD と AD 間で同期を維持するために使用される AD ユーザー識別子です。 |
| pwdLastSet |X |機械的なプロパティ。 既に発行されているトークンを無効にする時期を確認するために使用されます。 パスワード ハッシュ同期、パススルー認証、およびフェデレーションのすべてで使用されます。 |
|samAccountName|X| |
| sourceAnchor |X |機械的なプロパティ。 ADDS と Azure AD 間の関係を維持する変更不可の識別子です。 |
| usageLocation |X |機械的なプロパティ。 ユーザーの国/リージョン。 ライセンスの割り当てに使用されます。 |
| userPrincipalName |X |UPN は、ユーザーのログイン ID です。 多くの場合、[mail] 値と同じです。 |

## <a name="exchange-online"></a>Exchange Online
| 属性名 | User | Contact | グループ | 解説 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |アカウントが有効な場合に定義します。 |
| assistant |X |X | | |
| altRecipient |X | | |Azure AD Connect ビルド 1.1.552.0 以降が必要です。 |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| department |X |X | | |
| description | | |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| homePhone |X |X | | |
| info |X |X |X |この属性は現在、グループには使用されていません。 |
| Initials |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| mobile |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Azure AD Connect バージョン 1.1.524.0 で利用可能 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |この属性は現在、Exchange Online では使用されていません。 |
| msExchExtensionCustomAttribute2 |X |X |X |この属性は現在、Exchange Online では使用されていません。 |
| msExchExtensionCustomAttribute3 |X |X |X |この属性は現在、Exchange Online では使用されていません。 |
| msExchExtensionCustomAttribute4 |X |X |X |この属性は現在、Exchange Online では使用されていません。 |
| msExchExtensionCustomAttribute5 |X |X |X |この属性は現在、Exchange Online では使用されていません。 |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg-IsOrganizational | | |X | |
| objectSID |X | |X |機械的なプロパティ。 Azure AD と AD 間で同期を維持するために使用される AD ユーザー識別子です。 |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |機械的なプロパティ。 既に発行されているトークンを無効にする時期を確認するために使用されます。 パスワード同期とフェデレーションの両方で使用されます。 |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |機械的なプロパティ。 ADDS と Azure AD 間の関係を維持する変更不可の識別子です。 |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |Exchange Online がこの属性の権限のあるソースになり、それ以降の変更をオンプレミスから同期できない場合、Azure AD から Exchange Online に 1 回だけ同期されます。 詳細については、([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)) を参照してください。|
| title |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |機械的なプロパティ。 ユーザーの国/リージョン。 ライセンスの割り当てに使用されます。 |
| userCertificate |X |X | | |
| userPrincipalName |X | | |UPN は、ユーザーのログイン ID です。 多くの場合、[mail] 値と同じです。 |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| 属性名 | User | Contact | グループ | 解説 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |アカウントが有効な場合に定義します。 |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |X | |
| homePhone |X |X | | |
| info |X |X |X | |
| Initials |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| middleName |X |X | | |
| mobile |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSID |X | |X |機械的なプロパティ。 Azure AD と AD 間で同期を維持するために使用される AD ユーザー識別子です。 |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| postOfficeBox |X |X | |この属性は現在、SharePoint Online では使用されていません。 |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |機械的なプロパティ。 既に発行されているトークンを無効にする時期を確認するために使用されます。 パスワード ハッシュ同期、パススルー認証、およびフェデレーションのすべてで使用されます。 |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |機械的なプロパティ。 ADDS と Azure AD 間の関係を維持する変更不可の識別子です。 |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |Exchange Online がこの属性の権限のあるソースになり、それ以降の変更をオンプレミスから同期できない場合、Azure AD から Exchange Online に 1 回だけ同期されます。 詳細については、([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)) を参照してください。|
| title |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |機械的なプロパティ。 ユーザーの国/リージョン
. ライセンスの割り当てに使用されます。 |
| userPrincipalName |X | | |UPN は、ユーザーのログイン ID です。 多くの場合、[mail] 値と同じです。 |
| wWWHomePage |X |X | | |

## <a name="teams-and-skype-for-business-online"></a>Teams と Skype for Business Online
| 属性名 | User | Contact | グループ | 解説 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |アカウントが有効な場合に定義します。 |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| homePhone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| mobile |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| objectSID |X | |X |機械的なプロパティ。 Azure AD と AD 間で同期を維持するために使用される AD ユーザー識別子です。 |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |機械的なプロパティ。 既に発行されているトークンを無効にする時期を確認するために使用されます。 パスワード ハッシュ同期、パススルー認証、およびフェデレーションのすべてで使用されます。 |
| sn |X |X | | |
| sourceAnchor |X |X |X |機械的なプロパティ。 ADDS と Azure AD 間の関係を維持する変更不可の識別子です。 |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |Exchange Online がこの属性の権限のあるソースになり、それ以降の変更をオンプレミスから同期できない場合、Azure AD から Exchange Online に 1 回だけ同期されます。 詳細については、([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)) を参照してください。|
| title |X |X | | |
| usageLocation |X | | |機械的なプロパティ。 ユーザーの国/リージョン。 ライセンスの割り当てに使用されます。 |
| userPrincipalName |X | | |UPN は、ユーザーのログイン ID です。 多くの場合、[mail] 値と同じです。 |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| 属性名 | User | Contact | グループ | 解説 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |アカウントが有効な場合に定義します。 |
| cn |X | |X |共通名または別名です。 多くの場合、[mail] 値のプレフィックスです。 |
| displayName |X |X |X |多くの場合フレンドリ名 (姓名) として示される名前を表す文字列。 |
| mail |X |X |X |完全な電子メール アドレス。 |
| member | | |X | |
| objectSID |X | |X |機械的なプロパティ。 Azure AD と AD 間で同期を維持するために使用される AD ユーザー識別子です。 |
| proxyAddresses |X |X |X |機械的なプロパティ。 Azure AD によって使用されます。 ユーザー向けのすべてのセカンダリの電子メール アドレスが含まれています。 |
| pwdLastSet |X | | |機械的なプロパティ。 既に発行されているトークンを無効にする時期を確認するために使用されます。 |
| sourceAnchor |X |X |X |機械的なプロパティ。 ADDS と Azure AD 間の関係を維持する変更不可の識別子です。 |
| usageLocation |X | | |機械的なプロパティ。 ユーザーの国/リージョン。 ライセンスの割り当てに使用されます。 |
| userPrincipalName |X | | |この UPN は、ユーザーのログイン ID です。 多くの場合、[mail] 値と同じです。 |

## <a name="intune"></a>Intune
| 属性名 | User | Contact | グループ | 解説 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |アカウントが有効な場合に定義します。 |
| c |X |X | | |
| cn |X | |X | |
| description |X |X |X | |
| displayName |X |X |X | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| member | | |X | |
| objectSID |X | |X |機械的なプロパティ。 Azure AD と AD 間で同期を維持するために使用される AD ユーザー識別子です。 |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |機械的なプロパティ。 既に発行されているトークンを無効にする時期を確認するために使用されます。 パスワード ハッシュ同期、パススルー認証、およびフェデレーションのすべてで使用されます。 |
| sourceAnchor |X |X |X |機械的なプロパティ。 ADDS と Azure AD 間の関係を維持する変更不可の識別子です。 |
| usageLocation |X | | |機械的なプロパティ。 ユーザーの国/リージョン。 ライセンスの割り当てに使用されます。 |
| userPrincipalName |X | | |UPN は、ユーザーのログイン ID です。 多くの場合、[mail] 値と同じです。 |

## <a name="dynamics-crm"></a>Dynamics CRM
| 属性名 | User | Contact | グループ | 解説 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |アカウントが有効な場合に定義します。 |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| mobile |X |X | | |
| objectSID |X | |X |機械的なプロパティ。 Azure AD と AD 間で同期を維持するために使用される AD ユーザー識別子です。 |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |機械的なプロパティ。 既に発行されているトークンを無効にする時期を確認するために使用されます。 パスワード ハッシュ同期、パススルー認証、およびフェデレーションのすべてで使用されます。 |
| sn |X |X | | |
| sourceAnchor |X |X |X |機械的なプロパティ。 ADDS と Azure AD 間の関係を維持する変更不可の識別子です。 |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| title |X |X | | |
| usageLocation |X | | |機械的なプロパティ。 ユーザーの国/リージョン。 ライセンスの割り当てに使用されます。 |
| userPrincipalName |X | | |UPN は、ユーザーのログイン ID です。 多くの場合、[mail] 値と同じです。 |

## <a name="3rd-party-applications"></a>サード パーティ製アプリケーション
このグループは、一般的なワークロードまたはアプリケーションで最低限必要な属性として使用される属性セットです。 別のセクションに示されていないワークロードまたは Microsoft 以外のアプリで使用できます。 これは、次の場合に明示的に使用されます。

* Yammer (User のみを使用)
* [SharePoint のようなリソースによって提供されるハイブリッド企業間取引 (B2B) の組織間コラボレーションのシナリオ](/sharepoint/create-b2b-extranet)

このグループは、Microsoft 365、Dynamics、または Intune のサポートに Azure AD ディレクトリを利用しない場合に使用できる属性のセットです。 この中には、少数のコア属性が含まれます。 一部のサードパーティ アプリケーションへのシングル サインオンまたはプロビジョニングでは、ここで説明する属性に加えて、属性の同期を構成する必要があることに注意してください。 [SaaS アプリのチュートリアル](../saas-apps/tutorial-list.md)に、各アプリケーションの要件が説明されています。

| 属性名 | User | Contact | グループ | 解説 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |アカウントが有効な場合に定義します。 |
| cn |X | |X | |
| displayName |X |X |X | |
| employeeID |X |  |  | |
| givenName |X |X | | |
| mail |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| member | | |X | |
| objectSID |X | | |機械的なプロパティ。 Azure AD と AD 間で同期を維持するために使用される AD ユーザー識別子です。 |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |機械的なプロパティ。 既に発行されているトークンを無効にする時期を確認するために使用されます。 パスワード ハッシュ同期、パススルー認証、およびフェデレーションのすべてで使用されます。 |
| sn |X |X | | |
| sourceAnchor |X |X |X |機械的なプロパティ。 ADDS と Azure AD 間の関係を維持する変更不可の識別子です。 |
| usageLocation |X | | |機械的なプロパティ。 ユーザーの国/リージョン。 ライセンスの割り当てに使用されます。 |
| userPrincipalName |X | | |UPN は、ユーザーのログイン ID です。 多くの場合、[mail] 値と同じです。 |

## <a name="windows-10"></a>Windows 10
Windows 10 のドメイン参加コンピューター (デバイス) は、一部の属性を Azure AD と同期します。 シナリオの詳細については、「 [Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する](../devices/hybrid-azuread-join-plan.md)」をご覧ください。 これらの属性は常に同期され、Windows 10 は選択解除できるアプリとして表示されません。 Windows 10 のドメイン参加コンピューターは、属性 userCertificate が設定されていることで識別されます。

| 属性名 | Device | 解説 |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |ドメイン参加コンピューターのハードコーディングされた値です。 |
| displayName |X | |
| ms-DS-CreatorSID |X |registeredOwnerReference とも呼ばれます。 |
| objectGUID |X |deviceID とも呼ばれます。 |
| objectSID |X |onPremisesSecurityIdentifier とも呼ばれます。 |
| operatingSystem |X |deviceOSType とも呼ばれます。 |
| operatingSystemVersion |X |deviceOSVersion とも呼ばれます。 |
| userCertificate |X | |

選択したアプリに加え、 **ユーザー** には以下の属性があります。  

| 属性名 | User | 解説 |
| --- |:---:| --- |
| domainFQDN |X |dnsDomainName とも呼ばれます。 たとえば、contoso.com です。 |
| domainNetBios |X |netBiosName とも呼ばれます。 たとえば、CONTOSO です。 |
| msDS-KeyCredentialLink |X |ユーザーが Windows Hello for Business に登録された後。 | 

## <a name="exchange-hybrid-writeback"></a>Exchange ハイブリッドの書き戻し
次の属性は、 **Exchange ハイブリッド** を有効にした場合に Azure AD からオンプレミスの Active Directory に書き戻されます。 Exchange のバージョンに応じて、同期される属性が少なくなる場合があります。

| 属性名 (オンプレミスの AD) | 属性名 (Connect の UI) | User | Contact | グループ | 解説 |
| --- |:---:|:---:|:---:| --- |---|
| msDS ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Azure AD の cloudAnchor から派生します。 この属性は、Exchange 2016 と Windows Server 2016 AD で導入されました。 |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |X | | |オンライン アーカイブ: 顧客によるメールのアーカイブを有効にします。 |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |フィルター:オンプレミスのフィルター処理、オンラインの安全性、ブロックされた送信者データをクライアントから書き戻します。 |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |フィルター:オンプレミスのフィルター処理、オンラインの安全性、ブロックされた送信者データをクライアントから書き戻します。 |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |フィルター:オンプレミスのフィルター処理、オンラインの安全性、ブロックされた送信者データをクライアントから書き戻します。 |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |ユニファイド メッセージング (UM) の有効化 - オンラインのボイス メール: Microsoft Lync Server の統合で使用され、オンプレミスの Lync Server に対して、ユーザーがオンライン サービスでボイス メールを使用していることを示します。 |
| msExchUserHoldPolicies| ms-Exch-UserHoldPolicies |X | | |訴訟ホールド: クラウド サービスが訴訟ホールド状態にあるユーザーを特定できるようにします。 |
| proxyAddresses| proxyAddresses |X |X |X |Exchange Online の x500 アドレスのみが挿入されます。 |
| publicDelegates| ms-Exch-Public-Delegates  |X | | |オンプレミスの Exchange メールボックスを持つユーザーに送信するための SendOnBehalfTo 権限を、Exchange Online メールボックスに付与できます。 Azure AD Connect ビルド 1.1.552.0 以降が必要です。 |

## <a name="exchange-mail-public-folder"></a>Exchange メールのパブリック フォルダー
次の属性は、**Exchange メールのパブリック フォルダー** を有効にすると、オンプレミス Active Directory から Azure AD に同期されます。

| 属性名 | PublicFolder | 解説 |
| --- | :---:| --- |
| displayName | X |  |
| mail | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>デバイスの書き戻し
デバイス オブジェクトは、Active Directory に作成されます。 これらのオブジェクトは、Azure AD に参加しているデバイス、またはドメインに参加している Windows 10 コンピューターです。

| 属性名 | Device | 解説 |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| dn |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Windows Server 2016 AD スキーマでのみ |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Notes
* 代替 ID を使用する場合、オンプレミスの userPrincipalName 属性は Azure AD の onPremisesUserPrincipalName 属性と同期されます。 mail などの代替 ID 属性は、Azure AD の userPrincipalName 属性と同期されます。
* 前の一覧で、オブジェクトの種類 **User** は、オブジェクトの種類 **iNetOrgPerson** にも適用されます。

## <a name="next-steps"></a>次のステップ
[Azure AD Connect Sync](how-to-connect-sync-whatis.md) の構成に関するページをご覧ください。

「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。