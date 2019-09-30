---
title: Azure Active Directory を使用したアクセスを承認する
description: この記事では、Azure Active Directory を使用して Event Hubs リソースへのアクセスを承認する方法について説明します。
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 0d8d1b37e7f5ebb6eef1c76e4324041c48ab8986
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995737"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Azure Active Directory を使用して Event Hubs リソースへのアクセスを承認する
Azure Event Hubs は、Azure Active Directory (Azure AD) を使用して Event Hubs リソースへの要求を承認することをサポートしています。 Azure AD では、ロールベースのアクセス制御 (RBAC) を使用して、サービス プリンシパル (ユーザーまたはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与します。 ロールとロールの割り当ての詳細については、[各種ロールの理解](../role-based-access-control/overview.md)に関するページを参照してください。

## <a name="overview"></a>概要
セキュリティ プリンシパル (ユーザーまたはアプリケーション) が Event Hubs リソースにアクセスしようとした場合、要求は承認される必要があります。 Azure AD では、リソースへのアクセスは 2 段階のプロセスです。 

 1. まず、セキュリティ プリンシパルの ID が認証され、OAuth 2.0 トークンが返されます。 トークンを要求するリソース名は `https://eventhubs.azure.net/` です。
 1. 次に、指定したリソースへのアクセスを承認するために、トークンが要求の一部として Event Hubs サービスに渡されます。

認証の手順では、実行時にアプリケーション要求に OAuth 2.0 アクセス トークンが含まれている必要があります。 アプリケーションが Azure VM、仮想マシン スケール セット、または Azure 関数アプリなどの Azure エンティティ内から実行されている場合、マネージド ID を使用してリソースにアクセスできます。 マネージド ID によって Event Hubs サービスに対して行われる要求を認証する方法については、[Azure リソースに Azure Active Directory とマネージド ID を使用して Azure Event Hubs リソースへのアクセスを認証する](authenticate-managed-identity.md)方法に関する記事を参照してください。 

承認の手順では、セキュリティ プリンシパルに 1 つ以上の RBAC ロールを割り当てる必要があります。 Azure Event Hubs には、Event Hubs リソースの一連のアクセス許可を含む RBAC ロールが用意されています。 セキュリティ プリンシパルに割り当てられたロールによって、そのプリンシパルが持つアクセス許可が決定されます。 RBAC ロールの詳細については、「[Azure Event Hubs の組み込みの RBAC ロール](#built-in-rbac-roles-for-azure-event-hubs)」を参照してください。 

Event Hubs に対して要求を行うネイティブ アプリケーションと Web アプリケーションは、Azure AD を使用して承認することもできます。 アクセス トークンを要求し、それを使用して Event Hubs リソースへの要求を承認する方法については、[アプリケーションから Azure AD を使用して Azure Event Hubs へのアクセスを認証する](authenticate-application.md)方法に関する記事を参照してください。 

## <a name="assign-rbac-roles-for-access-rights"></a>アクセス権に RBAC ロールを割り当てる
Azure Active Directory (Azure AD) では、[ロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 Azure Event Hubs には、イベント ハブ データへのアクセスに使用される一般的なアクセス許可のセットを含む一連の組み込みの RBAC ロールが定義されています。また、データにアクセスするためのカスタム ロールを定義することもできます。

RBAC ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 アクセスでは、サブスクリプションのレベル、リソース グループ、Event Hubs 名前空間、またはそれ以下の任意のリソースにスコープを設定することができます。 Azure AD セキュリティ プリンシパルは、Azure リソースのユーザー、またはアプリケーション サービス プリンシパル、または[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) の場合があります。

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Azure Event Hubs の組み込みの RBAC ロール
Azure には、Event Hubs データへの Azure AD と OAuth を使ったアクセスを承認するために、次の組み込み RBAC ロールが用意されています。

- [Azure Event Hubs のデータ所有者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner):Event Hubs リソースへの完全なアクセス権を付与するには、このロールを使用します。
- [Azure Event Hubs のデータ送信者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver):Event Hubs リソースへの送信アクセス権を付与するには、このロールを使用します。
- [Azure Event Hubs のデータ受信者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender):Event Hubs リソースへの使用/受信アクセス権を付与するには、このロールを使用します。

## <a name="resource-scope"></a>リソースのスコープ 
セキュリティ プリンシパルに RBAC ロールを割り当てる前に、セキュリティ プリンシパルに必要なアクセスのスコープを決定します。 ベスト プラクティスとしては、常にできるだけ狭いスコープのみを付与するのが最善の方法です。

次の一覧で、Event Hubs リソースへのアクセスのスコープとして指定できるレベルを、最も狭いスコープから順に示します。

- **コンシューマー グループ**:このスコープでは、ロールの割り当てはこのエンティティにのみ適用されます。 現時点で、Azure portal は、このレベルのセキュリティ プリンシパルに RBAC ロールを割り当てることをサポートしていません。 
- **[イベント ハブ]** : ロールの割り当ては、イベント ハブ エンティティとそれ以下のコンシューマー グループに適用されます。
- **名前空間**:ロールの割り当ては、名前空間以下とそれに関連付けられているコンシューマー グループに対する Event Hubs のトポロジ全体にわたります。
- **[リソース グループ]** :ロールの割り当ては、リソース グループのすべての Event Hubs リソースに適用されます。
- **サブスクリプション**:ロールの割り当ては、サブスクリプションのすべてのリソース グループ内のすべての Event Hubs リソースに適用されます。

> [!NOTE]
> RBAC ロールの割り当ての反映には最大で 5 分かかる場合があることに留意してください。 

組み込みのロールの定義方法の詳細については、[ロール定義](../role-based-access-control/role-definitions.md#management-and-data-operations)に関するページを参照してください。 カスタム RBAC ロールの作成の詳細については、[Azure のロールベースのアクセス制御のためにカスタム ロールを作成する方法](../role-based-access-control/custom-roles.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順
- 組み込みの RBAC ロールをセキュリティ プリンシパルに割り当てる方法については、[Azure Active Directory を使用して Event Hubs リソースへのアクセスを認証する](authenticate-application.md)方法に関する記事を参照してください。
- [RBAC を使用してカスタム ロールを作成する方法](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)について学習します。
- [EH と共に Azure Active Directory を使用する方法](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)について学習します。

次の関連記事を参照してください。

- [Azure Active Directory を使用してアプリケーションから Azure Event Hubs への要求を認証する](authenticate-application.md)
- [Azure Active Directory を使用して Event Hubs リソースにアクセスするためのマネージド ID を認証する](authenticate-managed-identity.md)
- [Shared Access Signature を使用して Azure Event Hubs に対する要求を認証する](authenticate-shared-access-signature.md)
- [Shared Access Signature を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-shared-access-signature.md)