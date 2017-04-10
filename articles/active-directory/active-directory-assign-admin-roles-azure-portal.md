---
title: "Azure Active Directory の管理者ロールの割り当て | Microsoft Docs"
description: "Azure Active Directory で管理者ロールが使用できる機能と、管理者ロールを割り当てる方法について説明します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7fc27e8e-b55f-4194-9b8f-2e95705fb731
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: e8d3eb7e336383eb639e55656b26d881e8c2adb0
ms.lasthandoff: 03/29/2017


---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Azure Active Directory での管理者ロールの割り当て
> [!div class="op_single_selector"]
> * [Azure ポータル](active-directory-assign-admin-roles-azure-portal.md)
> * [Azure クラシック ポータル](active-directory-assign-admin-roles.md)
>
>

Azure Active Directory (Azure AD) を使用すると、各種役割ごとに別々の管理者を指定することができます。 これらの管理者は、Azure ポータルまたは Azure クラシック ポータルでさまざまな機能にアクセスでき、ロールに応じて、ユーザーの作成または編集、他のユーザーへの管理者ロールの割り当て、ユーザー パスワードのリセット、ユーザー ライセンスの管理、ドメインの管理などを行うことができます。 Office 365 ポータルと Azure クラシック ポータルのどちらで割り当てたのか、あるいは Windows PowerShell 用 Azure AD モジュールを使用して割り当てたのかに関係なく、管理者ロールが割り当てられたユーザーは、組織がサブスクライブしているすべてのクラウド サービスで同じ権限を持つことになります。

次の管理者ロールを使用できます。

* **課金管理者**: 購入、サブスクリプションの管理、サポート チケットの管理、サービス正常性の監視を行います。

* **コンプライアンス管理者**: このロールが割り当てられたユーザーは、Office 365 セキュリティ/コンプライアンス センターと Exchange 管理センター内で管理アクセス許可を持ちます。 詳しくは、「[Office 365 の管理者ロールについて](https://microsoft.sharepoint.com/teams/adiamteam/_layouts/15/WopiFrame.aspx?sourcedoc={dae8d6f3-5990-46a2-b12b-4c0e561bc7cc}&action=view&wdAccPdf=1)」をご覧ください。

* **CRM サービス管理者**: このロールが割り当てられたユーザーは、Microsoft CRM Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

* **デバイス管理者**: このロールが割り当てられたユーザーは、Azure Active Directory に参加しているすべての Windows 10 デバイスのローカル コンピューター管理者になります。 Azure Active Directory 内のデバイス オブジェクトを管理することはできません。

* **ディレクトリ リーダー**: これは、[同意フレームワーク](active-directory-integrating-applications.md)をサポートしていないアプリケーションに割り当てられる従来のロールです。 このロールをユーザーに割り当てることはできません。

* **ディレクトリ同期アカウント**: 使用しないでください。 このロールは、自動的に Azure AD Connect サービスに割り当てられます。他の用途に使用するためのものではなく、他の用途ではサポートされていません。

* **ディレクトリ ライター**: これは、[同意フレームワーク](active-directory-integrating-applications.md)をサポートしていないアプリケーションに割り当てられる従来のロールです。 このロールをユーザーに割り当てることはできません。

* **Exchange サービス管理者**: このロールが割り当てられたユーザーは、Microsoft Exchange Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

* **グローバル管理者/会社の管理者**: このロールが割り当てられたユーザーは、Azure Active Directory のすべての管理機能と、Azure Active Directory とフェデレーションするサービス (Exchange Online、SharePoint Online、Skype for Business Online など) にアクセスできます。 Azure Active Directory テナントにサインアップしたユーザーがグローバル管理者になります。 他の管理者ロールを割り当てることができるのはグローバル管理者だけです。 会社に複数のグローバル管理者が存在してかまいません。 グローバル管理者は、すべてのユーザーと他のすべての管理者のパスワードをリセットできます。

  > [!NOTE]
  > Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell では、このロールは "会社の管理者" として識別されます。 [Azure Portal](https://portal.azure.com) では、"全体管理者" になります。
  >
  >

* **ゲスト招待元**: このロールが割り当てられたユーザーは、"メンバーは招待ができる" ユーザー設定が "いいえ" に設定されている場合に、Azure Active Directory B2B ゲスト ユーザーの招待を管理できます。 B2B コラボレーションの詳細については、「[Azure AD B2B コラボレーション プレビューについて](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)」をご覧ください。 その他の権限は含まれません。

* **Intune サービス管理者**: このロールが割り当てられたユーザーは、Microsoft Intune Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。 さらに、このロールはポリシーを関連付けるためにユーザーとデバイスを管理することができ、グループを作成および管理することもできます。

* **メールボックス管理者**: このロールは、RIM Blackberry デバイスの Exchange Online 電子メール サポートの一部としてのみ使用されます。 組織が RIM Blackberry デバイスの Exchange Online 電子メールを使用していない場合、このロールは使用しません。

* **Partner Tier 1 サポート**: 使用しないでください。 このロールは廃止されており、将来的に Azure AD から削除されます。 このロールは少数の Microsoft 再販パートナーを対象としており、一般的な使用を目的としたものではありません。

* **Partner Tier 2 サポート**: 使用しないでください。 このロールは廃止されており、将来的に Azure AD から削除されます。 このロールは少数の Microsoft 再販パートナーを対象としており、一般的な使用を目的としたものではありません。

* **パスワード管理者/ヘルプデスク管理者**: このロールが割り当てられたユーザーは、パスワードのリセット、サービス要求の管理、サービス正常性の監視を行うことができます。 パスワード管理者がリセットできるのは、ユーザーと他のパスワード管理者のパスワードだけです。

  > [!NOTE]
  > Microsoft Graph API、Azure AD Graph API、および Azure AD PowerShell では、このロールは "ヘルプデスクの管理者" として識別されます。 [Azure Portal](https://portal.azure.com/) では、"パスワード管理者" になります。
  >
  >
  
* **Power BI サービス管理者**: このロールが割り当てられたユーザーは、Microsoft Power BI 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/en-us/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-001&ad=US)」をご覧ください。

* **特権ロール管理者**: このロールが割り当てられたユーザーは、Azure Active Directory と Azure AD Privileged Identity Management 内でロールの割り当てを管理できます。 また、このロールは Privileged Identity Management の全側面を管理できます。

* **セキュリティ管理者**: このロールが割り当てられたユーザーは、セキュリティ閲覧者ロールのすべての読み取り専用アクセス許可を持ち、セキュリティ関連サービス (Azure Active Directory Identity Protection、Privileged Identity Management、Office 365 セキュリティ/コンプライアンス センター) の構成を管理することもできます。 Office 365 のアクセス許可の詳細については、「[Office 365 セキュリティ/コンプライアンス センターでのアクセス許可](https://support.office.com/en-us/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)」をご覧ください。

* **セキュリティ閲覧者**: このロールが割り当てられたユーザーは、Azure Active Directory、Identity Protection、Privileged Identity Management 内のすべての情報を含め、グローバルな読み取り専用アクセス権を持ち、Azure Active Directory のサインイン レポートと監査ログを閲覧できます。 また、このロールには、Office 365 セキュリティ/コンプライアンス センターでの読み取り専用アクセス許可が付与されます。 Office 365 のアクセス許可の詳細については、「[Office 365 セキュリティ/コンプライアンス センターでのアクセス許可](https://support.office.com/en-us/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)」をご覧ください。

* **サービス サポート管理者**: このロールが割り当てられたユーザーは、Azure サービスと Office 365 サービスについて Microsoft へのサポート要求を開くことができます。また、Azure Portal と Office 365 管理ポータルで、サービス ダッシュボードとメッセージ センターを表示できます。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

* **SharePoint サービス管理者**: このロールが割り当てられたユーザーは、Microsoft SharePoint Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

* **Skype for Business/Lync サービス管理者**: このロールが割り当てられたユーザーは、Microsoft Skype for Business 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、Azure Active Directory で Skype 固有のユーザー属性を管理します。 さらに、このロールはサポート チケットを管理し、サービス正常性を監視できます。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

  > [!NOTE]
  > Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell では、このロールは "Lync サービス管理者" として識別されます。 [Azure Portal](https://portal.azure.com/) では、"Skype for Business サービス管理者" になります。
  >
  >

* **ユーザー アカウント管理者**: このロールが割り当てられたユーザーは、ユーザーとグループを作成し、全側面を管理できます。 さらに、このロールはサポート チケットを管理し、サービス正常性を監視できます。 ただし、いくつかの制限が適用されます。 たとえば、このロールはグローバル管理者を削除することはできません。また、管理者以外のパスワードは変更できますが、グローバル管理者または他の特権管理者のパスワードを変更することはできません。

## <a name="administrator-permissions"></a>管理者の権限

### <a name="billing-administrator"></a>課金管理者

| できること | できないこと |
| --- | --- |
|<p>会社情報とユーザー情報の表示</p><p>Office サポート チケットの管理</p><p>Office 製品の課金および購入操作の実行</p> |<p>ユーザー パスワードのリセット</p><p>ユーザー ビューの作成と管理</p><p>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理</p><p>ドメインの管理</p><p>会社情報の管理</p><p>他のユーザーへの管理者ロールの委任</p><p>ディレクトリ同期の使用</p><p>監査ログの表示</p>|

### <a name="global-administrator"></a>グローバル管理者
| できること | できないこと |
| --- | --- |
| <p>会社情報とユーザー情報の表示</p><p>Office サポート チケットの管理</p><p>Office 製品の課金および購入操作の実行</p><p>ユーザー パスワードのリセット</p>
<p>他の管理者のパスワードのリセット</p> <p>ユーザー ビューの作成と管理</p><p>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理</p><p>ドメインの管理</p><p>会社情報の管理</p><p>他のユーザーへの管理者ロールの委任</p><p>ディレクトリ同期の使用</p><p>Multi-Factor Authentication の有効化または無効化</p><p>監査ログの表示</p> |該当なし |

### <a name="password-administrator"></a>パスワード管理者
| できること | できないこと |
| --- | --- |
| <p>会社情報とユーザー情報の表示</p><p>Office サポート チケットの管理</p><p>ユーザー パスワードのリセット</p> <p>他の管理者のパスワードのリセット</p>|<p>Office 製品の課金操作と購入操作の実行</p><p>ユーザー ビューの作成と管理</p><p>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理</p><p>ドメインの管理</p><p>会社情報の管理</p><p>他のユーザーへの管理者ロールの委任</p><p>ディレクトリ同期の使用</p><p>レポートを表示する</p>|

### <a name="service-administrator"></a>サービス管理者
| できること | できないこと |
| --- | --- |
| <p>会社情報とユーザー情報の表示</p><p>Office サポート チケットの管理</p> |<p>ユーザー パスワードのリセット</p><p>Office 製品の課金操作と購入操作の実行</p><p>ユーザー ビューの作成と管理</p><p>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理</p><p>ドメインの管理</p><p>会社情報の管理</p><p>他のユーザーへの管理者ロールの委任</p><p>ディレクトリ同期の使用</p><p>監査ログの表示</p> |

### <a name="user-administrator"></a>ユーザー管理者
| できること | できないこと |
| --- | --- |
| <p>会社情報とユーザー情報の表示</p><p>Office サポート チケットの管理</p><p>ユーザー パスワードのリセット (制限付き)。</p><p>他の管理者のパスワードのリセット</p><p>他のユーザーのパスワードのリセット</p><p>ユーザー ビューの作成と管理</p><p>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理 (制限付き)。 グローバル管理者を削除することも、他の管理者を作成することもできません。</p> |<p>Office 製品の課金操作と購入操作の実行</p><p>ドメインの管理</p><p>会社情報の管理</p><p>他のユーザーへの管理者ロールの委任</p><p>ディレクトリ同期の使用</p><p>Multi-Factor Authentication の有効化または無効化</p><p>監査ログの表示</p> |

### <a name="security-reader"></a>セキュリティ リーダー
| イン | できること |
| --- | --- |
| Identity Protection Center |各セキュリティ機能の全セキュリティ レポートと設定情報の閲覧<ul><li>スパム対策<li>暗号化<li>データ損失の防止<li>マルウェア対策<li>高度な脅威保護<li>フィッシング詐欺対策<li>メールフロー ルール |
| Privileged Identity Management |<p>Azure AD PIM に表示される、Azure AD ロール割り当てに関するポリシーとレポート、セキュリティ レビューのすべての情報に対する読み取り専用アクセス権を持ちます。また、将来的には、Azure AD ロール割り当て以外のシナリオのポリシー データとレポートに対する読み取りアクセス権も付与される予定です。<p>Azure AD PIM へのサインアップおよび Azure AD PIM の変更を行うことは**できません**。 このロールのユーザーは、追加のロール (グローバル管理者や特権ロール管理者など) の資格を持っている場合、PIM のポータルまたは PowerShell からそのロールを有効化することができます。 |
| <p>Office 365 サービス正常性の監視</p><p>Office 365 セキュリティ/コンプライアンス センター</p> |<ul><li>アラートの閲覧と管理<li>セキュリティ ポリシーの閲覧<li>検索調査における脅威インテリジェンス情報、Cloud App Discovery、および検査の閲覧<li>全レポートの閲覧 |

### <a name="security-administrator"></a>セキュリティ管理者
| イン | できること |
| --- | --- |
| Identity Protection Center |<ul><li>セキュリティ リーダー ロールのすべての権限を持ちます。<li>さらに、パスワードのリセットを除く IPC のすべての操作を行うことができます。 |
| Privileged Identity Management |<ul><li>セキュリティ リーダー ロールのすべての権限を持ちます。<li>Azure AD ロールのメンバーシップまたは設定を管理することは**できません**。 |
| <p>Office 365 サービス正常性の監視</p><p>Office 365 セキュリティ/コンプライアンス センター |<ul><li>セキュリティ リーダー ロールのすべての権限を持ちます。<li>Advanced Threat Protection 機能 (マルウェアおよびウイルス防止、悪意のある URL の構成、URL 追跡など) のすべての設定を構成することができます。 |

## <a name="details-about-the-global-administrator-role"></a>グローバル管理者ロールの詳細
グローバル管理者は、すべての管理機能にアクセスできます。 既定では、Azure サブスクリプションにサインアップしたユーザーには、ディレクトリのグローバル管理者ロールが割り当てられます。 他の管理者ロールを割り当てることができるのはグローバル管理者だけです。

## <a name="assign-or-remove-administrator-roles"></a>管理者ロールの割り当てまたは削除
Azure Active Directory のユーザーに管理者ロールを割り当てる方法については、「[Azure Active Directory プレビューでユーザーを管理者ロールに割り当てる](active-directory-users-assign-role-azure-portal.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* Azure サブスクリプションの管理者を変更する方法の詳細については、「 [Azure 管理者ロールを追加または変更する方法](../billing-add-change-azure-subscription-administrator.md)
* Microsoft Azure でリソース アクセスを制御する方法の詳細については、「 [Azure でのリソース アクセスについて](active-directory-understanding-resource-access.md)
* Azure Active Directory と Azure サブスクリプションの関係の詳細については、「 [Azure サブスクリプションを Azure Active Directory に関連付ける方法](active-directory-how-subscriptions-associated-directory.md)
* [ユーザーの管理](active-directory-create-users.md)
* [パスワードの管理](active-directory-manage-passwords.md)
* [グループの管理](active-directory-manage-groups.md)

