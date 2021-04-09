---
title: Azure SQL の Azure Active Directory のディレクトリ閲覧者ロール
description: Azure SQL の Azure AD でのディレクトリ閲覧者ロールについて説明します。
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: 5764a8df862610fc076ce2810fcc0d4bf8dbda3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99094558"
---
# <a name="directory-readers-role-in-azure-active-directory-for-azure-sql"></a>Azure SQL の Azure Active Directory のディレクトリ閲覧者ロール

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> この記事にある機能は **パブリック プレビュー** 段階です。

Azure Active Directory (Azure AD) に、[クラウド グループを使用して Azure Active Directory でロールの割り当てを管理する (プレビュー)](../../active-directory/roles/groups-concept.md) 方法が導入されました。 これにより、Azure AD ロールをグループに割り当てることができます。

Azure SQL Database、Azure SQL Managed Instance、または Azure Synapse Analytics に対して [マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) を有効にする場合、[Azure AD Graph API](../../active-directory/develop/active-directory-graph-api.md) に対する読み取りアクセスを許可するには Azure AD [**ディレクトリ閲覧者**](../../active-directory/roles/permissions-reference.md#directory-readers)ロールをその ID に割り当てる必要があります。 SQL Database と Azure Synapse におけるマネージド ID は、サーバー ID と呼ばれます。 SQL Managed Instance のマネージド ID はマネージド インスタンス ID と呼ばれ、これはインスタンスの作成時に自動的に割り当てられます。 SQL Database または Azure Synapse にサーバー ID を割り当てる方法の詳細については、「[サービス プリンシパルが Azure AD ユーザーを作成できるようにする](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users)」を参照してください。

**ディレクトリ閲覧者** ロールは、次を行うために必要です。

- SQL Managed Instance に対する Azure AD ログインを作成する
- Azure SQL で Azure AD ユーザーを偽装する
- Windows 認証を使用している SQL Server ユーザーを Azure AD 認証を使用して SQL Managed Instance に移行する ([ALTER USER (Transact-SQL)](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-mi-current#d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration) コマンドを使用)
- SQL Managed Instance の Azure AD 管理者を変更する
- [サービス プリンシパル (アプリケーション)](authentication-aad-service-principal.md) を使用した Azure SQL の Azure AD ユーザーの作成を許可する

## <a name="assigning-the-directory-readers-role"></a>ディレクトリ閲覧者ロールの割り当て

[**ディレクトリ閲覧者**](../../active-directory/roles/permissions-reference.md#directory-readers)ロールを ID に割り当てるには、[全体管理者](../../active-directory/roles/permissions-reference.md#global-administrator)または [特権ロール管理者](../../active-directory/roles/permissions-reference.md#privileged-role-administrator)アクセス許可を持つユーザーが必要です。 SQL Database、SQL Managed Instance、または Azure Synapse を頻繁に管理またはデプロイするユーザーは、これらの高い特権を持つロールにアクセスできない可能性があります。 これは、計画外のAzure SQL リソースを作成したり、大規模な組織では連絡が付きにくいことが多い、高度な権限を持つロール メンバーの助けを必要とするユーザーにとっては、しばしば複雑な問題を引き起こす可能性があります。

SQL Managed Instance では、[マネージド インスタンスの Azure AD 管理者を設定する](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)前に、**ディレクトリ閲覧者** ロールをマネージド インスタンス ID に割り当てる必要があります。 

論理サーバーの Azure AD 管理者を設定する場合、SQL Database または Azure Synapse では **ディレクトリ閲覧者** ロールをサーバー ID に割り当てる必要はありません。 ただし、Azure AD アプリケーションの代わりに SQL Database または Azure Synapse で Azure AD オブジェクトを作成できるようにするには、**ディレクトリ閲覧者** ロールが必要になります。 このロールが SQL 論理サーバー ID に割り当てられていない場合、Azure SQL で Azure AD ユーザーを作成することはできません。 詳細については、「[Azure SQL での Azure Active Directory のサービス プリンシパル](authentication-aad-service-principal.md)」を参照してください。

## <a name="granting-the-directory-readers-role-to-an-azure-ad-group"></a>Azure AD グループへのディレクトリ閲覧者ロールの付与

現在は **パブリック プレビュー** 段階にありますが、[グローバル管理者](../../active-directory/roles/permissions-reference.md#global-administrator)または [特権ロール管理者](../../active-directory/roles/permissions-reference.md#privileged-role-administrator)が Azure AD グループを作成し、[**ディレクトリ閲覧者**](../../active-directory/roles/permissions-reference.md#directory-readers)権限をそのグループに割り当てることができるようになりました。 これにより、このグループのメンバーは Azure AD Graph API にアクセスできるようになります。 さらに、このグループの所有者である Azure AD ユーザーは、このグループに新しいメンバーを割り当てることができます (Azure SQL 論理サーバーの ID を含む)。

このソリューションでは、グループを作成してユーザーを 1 回限りのアクティビティとして割り当てるには、これまでと変わらず高い特権を持つユーザー (全体管理者または特権ロール管理者) が必要になりますが、今後は Azure AD グループの所有者が、追加のメンバーを割り当てることができるようになります。 これにより、将来的には、Azure ADテナント内のすべての SQL Database、SQL Managed Instance、または Azure Synapse サーバーを構成するために、高い特権を持つユーザーに関与してもらう必要がなくなります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:Azure AD グループにディレクトリ閲覧者ロールを割り当て、ロールの割り当てを管理する](authentication-aad-directory-readers-role-tutorial.md)