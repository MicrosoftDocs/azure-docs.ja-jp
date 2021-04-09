---
title: Active Directory 認証 - Azure Database for MySQL
description: Azure Database for MySQL での認証に関する Azure Active Directory の概念について説明します
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 942789d5541e796247af2dbdf9371cce4701105e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "87171897"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>MySQL での認証に Azure Active Directory を使用する

Microsoft Azure Active Directory (Azure AD) 認証は、Azure AD で定義された ID を使用して Azure Database for MySQL に接続できるよう設計されています。
Azure AD 認証を使用すると、データベース ユーザーの ID や他の Microsoft サービスを一元管理でき、アクセス許可の管理が容易になります。

Azure AD を利用すると、以下のようなメリットがあります。

- Azure サービス全体でのユーザー認証の一元化
- パスワード ポリシーとパスワード ローテーションの一元管理
- Azure Active Directory による複数の認証形式のサポート (パスワードを格納する必要がなくなる)
- 顧客は外部の (Azure AD) グループを使用してデータベースのアクセス許可を管理できます。
- Azure AD 認証では、MySQL データベース ユーザーを使用して、データベース レベルで ID を認証します
- Azure Database for MySQL に接続するアプリケーションに対するトークンベースの認証のサポート

Azure Active Directory 認証を構成して使用するには、次の手順に従います。

1. 必要に応じてユーザー ID と共に Azure Active Directory を作成して設定します。
2. (省略可能) Active Directory を関連付けるか、現在 Azure サブスクリプションに関連付けられている Active Directory を変更します。
3. Azure Database for MySQL サーバーの Azure AD 管理者を作成します。
4. Azure AD の ID にマップされているデータベース ユーザーをデータベースに作成します。
5. Azure AD の ID 用のトークンを取得してログインし、自分のデータベースに接続します。

> [!NOTE]
> Azure AD を作成して設定し、Azure Database for MySQL で Azure AD を構成する方法については、[Azure Database for MySQL 向けの Azure AD での構成とサインイン](howto-configure-sign-in-azure-ad-authentication.md)に関するページを参照してください。

## <a name="architecture"></a>Architecture

次の概要図は、Azure Database for MySQL で Azure AD 認証を使用した場合の認証のしくみをまとめたものです。 矢印は通信経路を示します。

![認証フロー][1]

## <a name="administrator-structure"></a>管理者の構造

Azure AD 認証を使用すると、MySQL サーバーの管理者アカウントは、元の MySQL 管理者と Azure AD 管理者の 2 つになります。 ユーザー データベースに最初の Azure AD 包含データベース ユーザーを作成できるのは、Azure AD アカウントに基づく管理者のみです。 Azure AD の管理者ログインには、Azure AD ユーザーまたは Azure AD グループを使用できます。 管理者がグループ アカウントの場合は、MySQL サーバー用に複数の Azure AD 管理者を有効にすることで、すべてのグループ メンバーがそのアカウントを使用できます。 グループ アカウントを管理者として使用すると、MySQL サーバーでユーザーまたはアクセス許可を変更することなく Azure AD でグループ メンバーを一元的に追加および削除できるため、より管理しやすくなります。 いつでも構成できる Azure AD 管理者 (ユーザーまたはグループ) は 1 つだけです。

![admin structure][2]

## <a name="permissions"></a>アクセス許可

Azure AD を使用して認証できる新しいユーザーを作成するには、設計された Azure AD 管理者である必要があります。 このユーザーは、特定の Azure Database for MySQL サーバー用に Azure AD 管理者アカウントを構成することで割り当てられます。

新しい Azure AD データベース ユーザーを作成するには、Azure AD 管理者として接続する必要があります。 これについては、[Azure Database for MySQL 向けの Azure AD での構成とログイン](howto-configure-sign-in-azure-ad-authentication.md)に関するページで説明されています。

Azure AD 認証が可能なのは、Azure Database for MySQL 用に Azure AD 管理者が作成された場合のみです。 Azure Active Directory 管理者がサーバーから削除された場合、以前に作成された既存の Azure Active Directory ユーザーは、自分の Azure Active Directory 資格情報を使用してもデータベースに接続できなくなります。

## <a name="connecting-using-azure-ad-identities"></a>Azure AD の ID を使用した接続

Azure Active Directory 認証では、Azure AD の ID を使用してデータベースに接続する次の方法がサポートされています。

- Azure Active Directory パスワード
- Azure Active Directory 統合
- MFA による Azure Active Directory ユニバーサル
- Active Directory Application 証明書またはクライアント シークレットの使用
- [Managed Identity](howto-connect-with-managed-identity.md)

Active Directory に対して認証された後、トークンを取得します。 このトークンはログイン用のパスワードです。

新しいユーザーの追加などの管理操作は、現時点では Azure AD ユーザー ロールに対してのみサポートされることに注意してください。

> [!NOTE]
> Active Directory トークンを使用して接続する方法の詳細については、[Azure Database for MySQL 向けの Azure AD での構成とサインイン](howto-configure-sign-in-azure-ad-authentication.md)に関するページを参照してください。

## <a name="additional-considerations"></a>その他の注意点

- Azure Active Directory 認証は、MySQL 5.7 以降でのみ使用できます。
- Azure Database for MySQL サーバーには、常に 1 人の Azure AD 管理者のみを構成できます。
- Azure Active Directory アカウントを使用して Azure Database for MySQL に最初に接続できるのは、MySQL の Azure AD 管理者だけです。 Active Directory 管理者は、それ以降の Azure AD のデータベース ユーザーを構成できます。
- Azure AD からユーザーが削除されると、そのユーザーは Azure AD で認証されることができなくなります。したがって、そのユーザーのアクセス トークンを取得できなくなります。 この場合、一致するユーザーがデータベースに残るものの、そのユーザーを使用してサーバーに接続することはできなくなります。
> [!NOTE]
> トークンの有効期限 (トークンの発行から最大 60 分) が切れるまで、削除された Azure AD ユーザーはログインを実行できます。  Azure Database for MySQL からもユーザーを削除する場合は、そのアクセスはすぐに取り消されます。
- サーバーから Azure AD 管理者が削除されると、サーバーと Azure AD テナントの関連付けが解消されます。そのため、サーバーへのすべての Azure AD ログインが無効になります。 同じテナントから新しい Azure AD 管理者を追加すると、Azure AD ログインが再び有効になります。
- Azure Database for MySQL では、ユーザー名ではなくユーザーの一意な Azure AD ユーザー ID を使用して、アクセス トークンを Azure Database for MySQL ユーザーに一致させます。 つまり、Azure AD で Azure AD ユーザーが削除され、同じ名前で新しいユーザーが作成されると、それは Azure Database for MySQL によって別のユーザーとして見なされます。 そのため、Azure AD からユーザーを削除して同じ名前で新しいユーザーを追加すると、その新しいユーザーは既存のユーザーに接続できなくなります。

## <a name="next-steps"></a>次のステップ

- Azure AD を作成して設定し、Azure Database for MySQL で Azure AD を構成する方法については、[Azure Database for MySQL 向けの Azure AD での構成とサインイン](howto-configure-sign-in-azure-ad-authentication.md)に関するページを参照してください。
- Azure Database for MySQL のログインとデータベース ユーザーの概要については、[Azure Database for MySQL でのユーザーの作成](howto-create-users.md)に関する記事を参照してください。

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
