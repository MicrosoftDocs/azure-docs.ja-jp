---
author: baanders
description: Azure Digital Twins の設定におけるアクセス許可の前提条件に関するインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 919cc934920d922a82ceb9e30630006627af44e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92205492"
---
## <a name="prerequisites-permission-requirements"></a>前提条件:権限の要件

この記事のすべての手順を実行するには、次のアクセス許可を持つ[サブスクリプションのロール](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)が必要です。
* Azure リソースのデプロイおよび管理
* Azure リソースへのユーザー アクセスの管理 (アクセス許可の付与と委任を含む)

この要件を満たす一般的なロールは、*所有者*、*アカウント管理者*、*ユーザー アクセス管理者* と *共同作成者* の組み合わせです。 他のロールが含まれているアクセス許可など、ロールとアクセス許可の詳細については、Azure RBAC のドキュメントの「[*従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロール*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)」をご覧ください。

ご自分のサブスクリプションのロールを確認するには、Azure portal 上の [サブスクリプション ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にアクセスします (このリンクを使用するか、ポータルの検索バーを使って "*サブスクリプション*" を検索できます)。 使用しているサブスクリプションの名前を検索し、それに対応するロールを *[自分の役割]* 列で確認します。

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="ユーザーが所有者として表示されている Azure portal 上のサブスクリプション ページのビュー" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

その値が *共同作成者*、または前述した必要なアクセス許可を持たない別のロールである場合は、こうしたアクセス許可を *持つ* サブスクリプションのユーザー (サブスクリプションの所有者やアカウント管理者など) に問い合わせて、次のいずれかの方法で続行することができます。
* ご自分に代わってこの記事の手順を実行するように依頼する
* ご自身で続行するためのアクセス許可が付与されるように、サブスクリプションのロールを昇格するように依頼する。 これが適切かどうかは、ユーザーの組織やその中でのロールによって異なります。