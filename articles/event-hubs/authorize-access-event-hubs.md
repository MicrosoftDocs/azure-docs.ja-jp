---
title: Azure Event Hubs へのアクセスを承認する
description: この記事では、Azure Event Hubs リソースへのアクセスを承認するためのさまざまなオプションについて説明します。
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 5fc35387f4ac28ad4dd28bea0013bcdf1e1e9f02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92332384"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Azure Event Hubs へのアクセスを承認する
イベント ハブからイベント/データを発行または使用するたびに、クライアントでは Event Hubs リソースへのアクセスが試行されます。 データの発行または使用に必要なアクセス許可をクライアントが持っていることがサービスにより確保されるように、セキュリティで保護されたリソースに対するすべての要求が承認される必要があります。 

Azure Event Hubs には、セキュリティで保護されたリソースへのアクセスを承認するための次のオプションが用意されています。

- Azure Active Directory
- 共有アクセス署名

> [!NOTE]
> この記事は、Event Hubs と [Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) の両方のシナリオを対象としています。 

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory (Azure AD) の Event Hubs リソースの統合により、Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、クライアントのリソースへのアクセスをきめ細かく制御することができます。 Azure RBAC を使用して、セキュリティ プリンシパル (ユーザー、グループ、またはアプリケーションのサービス プリンシパルが可能です) にアクセス許可を付与できます。 セキュリティ プリンシパルは、Azure AD によって認証されて、OAuth 2.0 トークンを返します。 トークンは、Event Hubs リソースにアクセスする要求を承認するために使用できます。

Azure AD を使用した認証の詳細については、次の記事を参照してください。

- [Azure Active Directory を使用して Azure Event Hubs への要求を認証する](authenticate-application.md)
- [Azure Active Directory を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-azure-active-directory.md)。

## <a name="shared-access-signatures"></a>共有アクセス署名 
Event Hubs リソースの Shared Access Signature (SAS) では、Event Hubs リソースへの限定的な委任アクセスが提供されます。 署名が有効な期間または付与するアクセス許可に制約を追加すると、柔軟にリソースを管理できます。 詳細については、[Shared Access Signature (SAS) を使用した認証](authenticate-shared-access-signature.md)に関する記事を参照してください。 

Azure AD によって返された OAuth 2.0 トークンを使用するユーザーまたはアプリケーションの承認では、Shared Access Signatures (SAS) よりも優れたセキュリティが提供され、使いやすくなります。 Azure AD を使用すれば、アクセス トークンをコードに保存する必要がないため、潜在的なセキュリティ脆弱性のリスクを排除できます。 引き続き Shared Access Signature (SAS) を使用して Event Hubs リソースへのきめ細かいアクセス許可を付与することはできますが、Azure AD によって、SAS トークンを管理したり侵害された SAS の取り消しを心配したりする必要なく、同様の機能が提供されています。 

既定では、すべての Event Hubs リソースはセキュリティで保護されており、アカウント所有者のみが使用できます。 ただし、上記の承認戦略のいずれかを使用して、クライアントに Event Hubs リソースへのアクセスを許可することができます。 セキュリティと使いやすさを最大限にするために、可能な場合は Azure AD を使用することをお勧めします。

SAS を使用した承認の詳細については、「[Shared Access Signature を使用した Event Hubs リソースへのアクセスの承認](authorize-access-shared-access-signature.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
- GitHub リポジトリに公開されている [Azure RBAC のサンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)を参照してください。 
- 次の記事をご覧ください。
    - [Azure Active Directory を使用してアプリケーションから Azure Event Hubs への要求を認証する](authenticate-application.md)
    - [Azure Active Directory を使用して Event Hubs リソースにアクセスするためのマネージド ID を認証する](authenticate-managed-identity.md)
    - [Shared Access Signature を使用して Azure Event Hubs に対する要求を認証する](authenticate-shared-access-signature.md)
    - [Azure Active Directory を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-azure-active-directory.md)
    - [Shared Access Signature を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-shared-access-signature.md)

