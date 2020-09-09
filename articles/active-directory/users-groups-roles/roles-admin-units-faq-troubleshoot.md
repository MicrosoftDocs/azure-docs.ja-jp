---
title: 管理単位のトラブルシューティングと FAQ - Azure Active Directory | Microsoft Docs
description: Azure Active Directory において制限されたスコープでアクセス許可を付与する管理単位を調査します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b48bebe6aa5f9862d5f51fea257c4b7f3057639b
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794472"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD の管理単位:トラブルシューティングと FAQ

Azure Active Directory (Azure AD) では、きめ細かい管理制御を行うために、1 つ以上の管理単位 (AU) に制限されたスコープでユーザーを Azure AD ロールに割り当てることができます。 一般的なタスク用の PowerShell スクリプトのサンプルについては、「[管理単位の操作](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)」を参照してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q:管理単位を作成できないのはなぜですか?**

**A:** Azure AD で管理単位を作成できるのは、"*全体管理者*" と "*特権ロール管理者*" のみです。 管理単位を作成しようとしているユーザーに、"*全体管理者*" または "*特権ロール管理者*" のロールが割り当てられていることを確認してください。

**Q:管理単位にグループを追加しました。そこにグループ メンバーがまだ表示されないのはなぜですか?**

**A:** グループを管理単位に追加しても、そのグループのすべてのメンバーがそれに追加されるわけではありません。 ユーザーを管理単位に直接割り当てる必要があります。

**Q:管理単位のメンバーを先ほど追加 (または削除) しました。メンバーがユーザー インターフェイスに表示されない (または、まだ表示されている) のはなぜですか?**

**A:** 管理単位の 1 つまたは複数のメンバーの追加または削除の処理が **[管理単位]** ページに反映されるまでに数分かかる場合があります。 あるいは、関連するリソースのプロパティに直接アクセスして、アクションが完了したかどうかを確認することもできます。 AU のユーザーとグループについて詳しくは、[「ユーザーの管理単位を一覧表示する](roles-admin-units-add-manage-users.md)」および「[グループの管理単位を一覧表示する](roles-admin-units-add-manage-groups.md)」をご覧ください。

**Q:私は管理単位の委任されたパスワード管理者です。特定のユーザーのパスワードをリセットできないのはなぜですか?**

**A:** 管理単位の管理者は、自分の管理単位に割り当てられているユーザーのパスワードのみをリセットできます。 パスワードのリセットが失敗している対象のユーザーが、あなたが割り当てられている管理単位に属していることを確認してください。 ユーザーが同じ管理単位に属していても、そのユーザーのパスワードをリセットできない場合は、そのユーザーに割り当てられているロールを確認してください。 

特権の昇格を防ぐため、管理単位のスコープが設定された管理者は、組織全体のスコープでロールに割り当てられているユーザーのパスワードをリセットすることはできません。

**Q:管理単位が必要なのはなぜですか?スコープを定義する方法としてセキュリティ グループを使用することはできないのでしょうか?**

**A:** セキュリティ グループには、既存の目的と承認モデルがあります。 たとえば "*ユーザー管理者*" は、Azure AD 組織内のすべてのセキュリティ グループのメンバーシップを管理できます。 このロールは、Salesforce などのアプリケーションへのアクセスを管理するためにグループを使用する場合があります。 "*ユーザー管理者*" は委任モデル自体を管理できないようにする必要があります。これは、"リソースのグループ化" シナリオをサポートするためにセキュリティ グループが拡張された場合の結果です。 Windows Server Active Directory の組織単位などの管理単位は、さまざまなディレクトリ オブジェクトの管理スコープを設定する方法を提供するためのものです。 セキュリティ グループ自体は、リソース スコープのメンバーであっても構いません。 セキュリティ グループを使用して、管理者が管理できるセキュリティ グループのセットを定義すると、混乱を招く可能性があります。

**Q:グループを管理単位に追加するとはどういう意味ですか?**

**A:** グループを管理単位に追加すると、同様にその管理単位にスコープが制限されている "*ユーザー管理者*" の管理スコープにそのグループ自体が入れられます。 その管理単位のユーザー管理者は、グループ自体の名前とメンバーシップを管理できます。 その管理単位の "*ユーザー管理者*" には、グループのユーザーを管理する (たとえば、そのパスワードをリセットする) ためのアクセス許可は付与されません。 ユーザーを管理する権限を "*ユーザー管理者*" に付与するには、ユーザーがその管理単位の直接のメンバーになっている必要があります。

**Q:リソース (ユーザーまたはグループ) を複数の管理単位のメンバーにすることはできますか?**

**A:** はい。リソースは複数の管理単位のメンバーにすることができます。 リソースを管理できるのは、そのリソースに対するアクセス許可を持つすべての組織全体および管理単位でスコープ設定された管理者です。

**Q:B2C 組織では管理単位を使用できますか?**

**A:** いいえ。B2C 組織では管理単位を使用できません。

**Q:入れ子になった管理単位はサポートされていますか?**

**A:** いいえ、入れ子になった管理単位はサポートされていません。

**Q:PowerShell と Graph API では管理単位はサポートされていますか?**

**A:** はい。 管理単位のサポートについては、[PowerShell コマンドレットのドキュメント](/powershell/module/Azuread/?view=azureadps-2.0-preview)と[サンプル スクリプト](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview)を参照してください。 

Microsoft Graph で [administrativeUnit リソースの種類](/graph/api/resources/administrativeunit?view=graph-rest-beta)のサポートを検索してください。

## <a name="next-steps"></a>次のステップ

- [管理単位を使用してロールのスコープを制限する](directory-administrative-units.md)
- [管理単位を管理する](roles-admin-units-manage.md)