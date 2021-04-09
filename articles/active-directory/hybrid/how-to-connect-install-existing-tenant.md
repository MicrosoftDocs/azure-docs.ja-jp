---
title: Azure AD Connect:Azure AD が既にある場合 | Microsoft Docs
description: このトピックでは、既存の Azure AD テナントがある場合の Connect の使い方を説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68251270b6273f5a07391138e5c7210f1c46ba5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93420531"
---
# <a name="azure-ad-connect-when-you-have-an-existing-tenant"></a>Azure AD Connect:既存のテナントがある場合
Azure AD Connect の使い方に関するトピックではほとんどの場合、新しい Azure AD テナントで作業を開始すること、そしてユーザーまたはその他のオブジェクトがないことを想定しています。 しかし、ユーザーとその他のオブジェクトが存在する Azure AD テナントを既に使用していて Connect が必要になった場合は、このトピックを参照してください。

## <a name="the-basics"></a>基本
Azure AD 内のオブジェクトは、クラウド (Azure AD) とオンプレミスのいずれかで管理されます。 単一のオブジェクトでは、一部の属性をオンプレミスで管理し、他の属性を Azure AD で管理するということができません。 各オブジェクトには、オブジェクトが管理されている場所を示すフラグが付いています。

一部のユーザーをオンプレミスで管理して他のユーザーをクラウドで管理することができます。 この構成のよくあるシナリオとしては、経理ワーカーと営業ワーカーが両方存在する組織が挙げられます。 経理ワーカーにオンプレミス AD アカウントがありますが、営業ワーカーにはありません。営業ワーカーは Azure AD にアカウントがあります。 オンプレミスで管理するユーザーもいれば、Azure AD で管理するユーザーもいることになります。

オンプレミスの AD にも存在するユーザーを Azure AD で管理していて、後になって Connect が必要になった場合、いくつか追加の懸念事項を考慮する必要があります。

## <a name="sync-with-existing-users-in-azure-ad"></a>Azure AD の既存のユーザーとの同期
Azure AD Connect をインストールして同期を開始すると、(Azure AD の) Azure AD 同期サービスによってすべての新しいオブジェクトがチェックされ、一致する既存のオブジェクトが検索されます。 このプロセスでは、**userPrincipalName**、**proxyAddresses**、**sourceAnchor**/**immutableID** の 3 つの属性が使用されます。 **userPrincipalName** と **proxyAddresses** の一致は、**あいまい一致** と呼ばれます。 **sourceAnchor** の一致は、**完全一致** と呼ばれます。 **proxyAddresses** 属性では、**SMTP:** 付きの値 (つまり、プライマリ電子メール アドレス) のみが評価に使用されます。

一致は、Connect で生成された新しいオブジェクトについてのみ評価されます。 これらの属性のいずれかに一致するように既存のオブジェクトを変更すると、エラーが発生します。

Connect で生成されたオブジェクトと属性値が同じであるオブジェクトが Azure AD によって検出され、それが Azure AD に既にあることが判明すると、Azure AD のオブジェクトは Connect によって引き継がれます。 以前クラウドで管理されていたオブジェクトには、オンプレミスで管理されていることを示すフラグが付きます。 オンプレミスの AD の値が設定された Azure AD の属性はすべて、オンプレミスの値で上書きされます。

> [!WARNING]
> Azure AD のすべての属性はオンプレミスの値で上書きされるため、重要なデータは必ずオンプレミスで保持するようにしてください。 たとえば、電子メール アドレスを Microsoft 365 でしか管理しておらず、オンプレミスの AD DS で更新していなかった場合、AD DS に存在しない Azure AD/Microsoft 365 の値はすべて失われます。

> [!IMPORTANT]
> (簡易設定で常に使用される) パスワード同期を使用する場合、Azure AD のパスワードはオンプレミスの AD のパスワードで上書きされます。 ユーザーが異なるパスワードの管理に慣れている場合、Connect をインストールした後に、オンプレミスのパスワードを使用する必要があることをユーザーに伝える必要があります。

計画を立てる際には、前のセクションと警告の内容を考慮する必要があります。 オンプレミスの AD DS に反映していない変更が Azure AD に多数ある場合、Azure AD Connect でオブジェクトを同期する前に、更新された値を AD DS に設定する方法について計画しておく必要があります。

あいまい一致でオブジェクトを一致させた場合、**sourceAnchor** が Azure AD のオブジェクトに追加され、後で完全一致が使用できるようになります。

>[!IMPORTANT]
> Microsoft は、Azure Active Directory で既存の管理アカウントとオンプレミス アカウントを同期しないことを強くお勧めします。

### <a name="hard-match-vs-soft-match"></a>完全一致とあいまい一致
Connect を新しくインストールする場合、完全一致とあいまい一致にはほとんど違いはありません。 違いが生じるのは、ディザスター リカバリーの際です。 Azure AD Connect でサーバーを失った場合、データを失うことなく新しいインスタンスをもう一度インストールできます。 初期インストール中に、sourceAnchor があるオブジェクトは Connect に送信されます。 その後、一致はクライアント (Azure AD Connect) によって評価されます。これは Azure AD で実行される場合よりもはるかに高速です。 完全一致は Connect と Azure AD の両方によって評価されます。 あいまい一致は Azure AD によってのみ評価されます。

### <a name="other-objects-than-users"></a>ユーザー以外のオブジェクト
メールが有効なグループと連絡先については、proxyAddresses に基づいてあいまい一致を利用できます。 ユーザーについてのみ、(PowerShell を使用して) sourceAnchor/immutableID の更新のみを実行できます。このため、完全一致は適用されません。 メールが有効になっていないグループについては、あいまい一致も完全一致もサポートされていません。

### <a name="admin-role-considerations"></a>管理者ロールに関する考慮事項
信頼されていないオンプレミス ユーザーが任意の管理者ロールを持つクラウド ユーザーと一致しないようにするために Azure AD Connect では、オンプレミス ユーザー オブジェクトは管理者ロールを持つオブジェクトには一致しません。 これは既定です。 この振る舞いを回避するには、次の操作を行います。

1.  クラウドのみのユーザー オブジェクトからディレクトリ ロールを削除します。
2.  失敗したユーザー同期の試行がある場合は、クラウド内の検疫されたオブジェクトの物理的な削除を行います。
3.  同期をトリガーします。
4.  一致が発生した後は、クラウド内のユーザー オブジェクトにオプションでディレクトリ ロールを追加します。



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Azure AD のデータによる新しいオンプレミスの Active Directory の作成
お客様によっては、最初はクラウドのみのソリューションと Azure AD を使用しており、オンプレミスの AD はないものの、 後になってオンプレミスのリソースが必要になり、Azure AD のデータに基づいてオンプレミスの AD を構築しなければならなくなるケースもあります。 Azure AD Connect ではこのシナリオに対応できません。 オンプレミスでユーザーは作成されませんし、オンプレミスのパスワードを Azure AD と同じものに設定する機能はありません。

オンプレミスの AD の追加を計画する理由が LOB (基幹業務アプリ) への対応のみである場合は、[Azure AD Domain Services](../../active-directory-domain-services/index.yml) の使用を検討した方がよいと思われます。

## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
