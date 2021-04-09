---
title: Azure Active Directory を使用したアクセスを承認する
description: この記事では、Azure Active Directory を使用して Azure SignalR Service リソースへのアクセスを承認する方法について説明します。
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 37c2e41b5c81f941245b895ecd144baee3b9db6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97797414"
---
# <a name="authorize-access-to-azure-signalr-service-resources-using-azure-active-directory"></a>Azure Active Directory を使用して Azure SignalR Service リソースへのアクセスを承認する
Azure SignalR Service では、Azure Active Directory (Azure AD) を使用して Azure SignalR Service リソースへの要求を承認することがサポートされています。 Azure AD では、ロールベースのアクセス制御 (RBAC) を使用して、サービス プリンシパル (ユーザーまたはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与します。 ロールとロールの割り当ての詳細については、[各種ロールの理解](../role-based-access-control/overview.md)に関するページを参照してください。

## <a name="overview"></a>概要
セキュリティ プリンシパル (アプリケーション) によって Azure SignalR Service リソースへのアクセスが試みられたら、その要求を承認する必要があります。 Azure AD では、リソースへのアクセスは 2 段階のプロセスです。 

 1. まず、セキュリティ プリンシパルの ID が認証され、OAuth 2.0 トークンが返されます。 トークンを要求するリソース名は `https://signalr.azure.com/` です。
 2. 次に、指定したリソースにアクセスする承認を得るため、Azure SignalR Service への要求の一部としてトークンを渡します。

認証の手順により、実行時にアプリケーション要求に OAuth 2.0 アクセス トークンが含まれる必要があります。 ハブ サーバーが Azure VM、仮想マシン スケール セット、Azure 関数アプリなどの Azure エンティティ内で実行されている場合は、マネージド ID を使用してリソースにアクセスできます。 マネージド ID によって Azure SignalR Service に対して行われる要求を認証する方法については、[Azure Active Directory と Azure リソースのマネージド ID を使用して Azure SignalR Service リソースへのアクセスを承認する](authenticate-managed-identity.md)方法に関するページを参照してください。 

承認の手順では、セキュリティ プリンシパルに 1 つ以上の RBAC ロールを割り当てる必要があります。 Azure SignalR Service には、Azure SignalR リソースへの一連のアクセス許可を含む RBAC ロールが用意されています。 セキュリティ プリンシパルに割り当てられたロールによって、そのプリンシパルが持つアクセス許可が決定されます。 RBAC ロールの詳細については、「[Azure SignalR Service 用の Azure 組み込みロール](#azure-built-in-roles-for-azure-signalr-service)」を参照してください。 

Azure エンティティ内で実行されていない SignalR ハブ サーバーも、Azure AD で承認できます。 アクセス トークンを要求し、それを使用して Azure SignalR Service リソースへの要求を承認する方法については、[Azure AD を使用してアプリケーションから Azure SignalR Service へのアクセスを認証する](authenticate-application.md)方法に関する記事を参照してください。 

## <a name="azure-built-in-roles-for-azure-signalr-service"></a>Azure SignalR Service 用の Azure 組み込みロール

- [SignalR アプリ サーバー]
- [SignalR Service 閲覧者]
- [SignalR Service 所有者]

## <a name="assign-rbac-roles-for-access-rights"></a>アクセス権に RBAC ロールを割り当てる
Azure Active Directory (Azure AD) では、[ロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 Azure SignalR Service で定義されている Azure 組み込みロールのセットには、Azure SignalR Service へのアクセスに使用されるアクセス許可の一般的なセットが含まれています。また、リソースにアクセスするためのカスタム ロールを定義することもできます。

RBAC ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 サブスクリプション、リソース グループ、または Azure SignalR Service リソースのレベルに、アクセスのスコープを設定できます。 Azure AD セキュリティ プリンシパルは、ユーザー、アプリケーション、または [Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) の場合があります。

## <a name="built-in-roles-for-azure-signalr-service"></a>Azure SignalR Service 用の組み込みロール
Azure には、Azure AD と OAuth を使用して Azure SignalR Service リソースへのアクセスを承認するために、次の Azure 組み込みロールが用意されています。

### <a name="signalr-app-server"></a>SignalR アプリ サーバー

このロールは、クライアント トークンへの署名に使用される一時的なアクセス キーを取得するためのアクセス権を付与するために使用します。

### <a name="signalr-serverless-contributor"></a>SignalR サーバーレス共同作成者

このロールは、Azure SignalR Service からクライアント トークンを直接取得するためのアクセス権を付与するために使用します。

## <a name="next-steps"></a>次のステップ

次の関連記事を参照してください。

- [Azure SignalR Service にアクセスするために Azure AD でアプリケーションを認証する](authenticate-application.md)
- [Azure SignalR Service にアクセスするために Azure AD を使用してマネージド ID を認証する](authenticate-managed-identity.md)