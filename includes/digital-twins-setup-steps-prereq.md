---
author: baanders
description: Azure Digital Twins 設定の手順の概要とアクセス許可の前提条件のためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: b87d45a8be68a77df7b06ebd6e80562ccbe0e444
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009660"
---
>[!NOTE]
>これらの操作は、Azure サブスクリプションでリソースとユーザー アクセスの両方を管理する権限を持つユーザーが実行するよう意図されています。 一部の手順は下位のアクセス許可でも実行できますが、使用可能なインスタンスを完全に設定するためには、これらのアクセス許可を持つユーザーの協力が必要です。 詳細については、下の「[*前提条件:必要なアクセス許可*](#prerequisites-permission-requirements)」セクションを参照してください。

新しい Azure Digital Twins インスタンスのための設定全体は、3 つの部分で構成されます。
1. **インスタンスの作成**
2. **ユーザーのアクセス許可の設定**:Azure ユーザーが Azure Digital Twins インスタンスとそのデータを管理するには、"*Azure Digital Twins 所有者 (プレビュー)* " ロールが必要です。 この手順では、Azure サブスクリプションの所有者/管理者が、Azure Digital Twins インスタンスを管理することになる人にこのロールを割り当てます。 これは、自分自身のこともあれば、組織内の他の人であることもあります。
3. **クライアント アプリケーションのアクセス許可の設定**:インスタンスとやり取りするために使用するクライアント アプリケーションを作成するのは一般的です。 そのクライアント アプリが Azure Digital Twins にアクセスできるようにするには、そのクライアント アプリケーションがインスタンスに対して認証するために使用する "*アプリ登録*" を [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) で設定する必要があります。

続行するには、Azure サブスクリプションが必要です。 [こちら](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)から無料で設定できます。

## <a name="prerequisites-permission-requirements"></a>前提条件:権限の要件

この記事のすべての手順を実行するには、次のアクセス許可を持つ[サブスクリプションのロール](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)が必要です。
* Azure リソースのデプロイおよび管理
* Azure リソースへのユーザー アクセスの管理 (アクセス許可の付与と委任を含む)

この要件を満たす一般的なロールは、*所有者*、*アカウント管理者*、*ユーザー アクセス管理者*と*共同作成者*の組み合わせです。 他のロールが含まれているアクセス許可など、ロールとアクセス許可の詳細については、Azure RBAC のドキュメントの「[*従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロール*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)」をご覧ください。

ご自分のサブスクリプションのロールを確認するには、Azure portal 上の[サブスクリプション ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にアクセスします (このリンクを使用するか、ポータルの検索バーを使って "*サブスクリプション*" を検索できます)。 使用しているサブスクリプションの名前を検索し、それに対応するロールを *[自分の役割]* 列で確認します。

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="ユーザーが所有者として表示されている Azure portal 上のサブスクリプション ページのビュー" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

その値が*共同作成者*、または前述した必要なアクセス許可を持たない別のロールである場合は、こうしたアクセス許可を*持つ*サブスクリプションのユーザー (サブスクリプションの所有者やアカウント管理者など) に問い合わせて、次のいずれかの方法で続行することができます。
* ご自分に代わってこの記事の手順を実行するように依頼する
* ご自身で続行するためのアクセス許可が付与されるように、サブスクリプションのロールを昇格するように依頼する。 これが適切かどうかは、ユーザーの組織やその中でのロールによって異なります。