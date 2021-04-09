---
title: Azure Active Directory を使用したアクセスを承認する
description: この記事では、Azure Active Directory を使用して Event Hubs リソースへのアクセスを承認する方法について説明します。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: d794b03fdbb5429983788c74cbb05a7c13bf2d76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92910799"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Azure Active Directory を使用して Event Hubs リソースへのアクセスを承認する
Azure Event Hubs は、Azure Active Directory (Azure AD) を使用して Event Hubs リソースへの要求を承認することをサポートしています。 Azure AD では、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、セキュリティ プリンシパル (ユーザーまたはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与できます。 ロールとロールの割り当ての詳細については、[各種ロールの理解](../role-based-access-control/overview.md)に関するページを参照してください。

## <a name="overview"></a>概要
セキュリティ プリンシパル (ユーザーまたはアプリケーション) が Event Hubs リソースにアクセスしようとした場合、要求は承認される必要があります。 Azure AD では、リソースへのアクセスは 2 段階のプロセスです。 

 1. まず、セキュリティ プリンシパルの ID が認証され、OAuth 2.0 トークンが返されます。 トークンを要求するリソース名は `https://eventhubs.azure.net/` であり、すべてのクラウド/テナントで同じです。 Kafka クライアントの場合、トークンを要求するリソースは `https://<namespace>.servicebus.windows.net` です。
 1. 次に、指定したリソースへのアクセスを承認するために、トークンが要求の一部として Event Hubs サービスに渡されます。

認証の手順により、実行時にアプリケーション要求に OAuth 2.0 アクセス トークンが含まれる必要があります。 アプリケーションが Azure VM、仮想マシン スケール セット、または Azure 関数アプリなどの Azure エンティティ内から実行されている場合、マネージド ID を使用してリソースにアクセスできます。 マネージド ID によって Event Hubs サービスに対して行われる要求を認証する方法については、[Azure リソースに Azure Active Directory とマネージド ID を使用して Azure Event Hubs リソースへのアクセスを認証する](authenticate-managed-identity.md)方法に関する記事を参照してください。 

承認の手順では、セキュリティ プリンシパルに 1 つまたは複数の Azure ロールを割り当てる必要があります。 Azure Event Hubs には、Event Hubs リソースの一連のアクセス許可を含む Azure ロールが用意されています。 セキュリティ プリンシパルに割り当てられたロールによって、そのプリンシパルが持つアクセス許可が決定されます。 Azure ロールの詳細については、「[Azure Event Hubs の Azure の組み込みロール](#azure-built-in-roles-for-azure-event-hubs)」を参照してください。 

Event Hubs に対して要求を行うネイティブ アプリケーションと Web アプリケーションは、Azure AD を使用して承認することもできます。 アクセス トークンを要求し、それを使用して Event Hubs リソースへの要求を承認する方法については、[アプリケーションから Azure AD を使用して Azure Event Hubs へのアクセスを認証する](authenticate-application.md)方法に関する記事を参照してください。 

## <a name="assign-azure-roles-for-access-rights"></a>アクセス権の Azure ロールを割り当てる
Azure Active Directory (Azure AD) では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 Azure Event Hubs には、イベント ハブ データへのアクセスに使用される一般的なアクセス許可のセットを含む一連の Azure の組み込みロールが定義されています。また、データにアクセスするためのカスタム ロールを定義することもできます。

Azure ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 アクセスでは、サブスクリプションのレベル、リソース グループ、Event Hubs 名前空間、またはそれ以下の任意のリソースにスコープを設定することができます。 Azure AD セキュリティ プリンシパルは、Azure リソースのユーザー、またはアプリケーション サービス プリンシパル、または[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) の場合があります。

## <a name="azure-built-in-roles-for-azure-event-hubs"></a>Azure Event Hubs の Azure の組み込みロール
Azure には、Event Hubs データへの Azure AD と OAuth を使ったアクセスを承認するために、次の Azure の組み込みロールが用意されています。

| Role | 説明 | 
| ---- | ----------- | 
| [Azure Event Hubs データ所有者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) | Event Hubs リソースへの完全なアクセス権を付与するには、このロールを使用します。 |
| [Azure Event Hubs データ送信者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) | Event Hubs リソースへの送信アクセス権を付与するには、このロールを使用します。 |
| [Azure Event Hubs データ受信者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) | Event Hubs リソースへの使用/受信アクセス権を付与するには、このロールを使用します。 |

スキーマ レジストリの組み込みロールについては、[スキーマ レジストリのロール](schema-registry-overview.md#azure-role-based-access-control)に関する記事を参照してください。

## <a name="resource-scope"></a>リソースのスコープ 
セキュリティ プリンシパルに Azure ロールを割り当てる前に、セキュリティ プリンシパルに必要なアクセスのスコープを決定します。 ベスト プラクティスとしては、常にできるだけ狭いスコープのみを付与するのが最善の方法です。

次の一覧で、Event Hubs リソースへのアクセスのスコープとして指定できるレベルを、最も狭いスコープから順に示します。

- **コンシューマー グループ**:このスコープでは、ロールの割り当てはこのエンティティにのみ適用されます。 現時点で、Azure portal は、このレベルのセキュリティ プリンシパルに Azure ロールを割り当てることをサポートしていません。 
- **[イベント ハブ]** : ロールの割り当ては、イベント ハブ エンティティとそれ以下のコンシューマー グループに適用されます。
- **名前空間**:ロールの割り当ては、名前空間以下とそれに関連付けられているコンシューマー グループに対する Event Hubs のトポロジ全体にわたります。
- **[リソース グループ]** :ロールの割り当ては、リソース グループのすべての Event Hubs リソースに適用されます。
- **サブスクリプション**:ロールの割り当ては、サブスクリプションのすべてのリソース グループ内のすべての Event Hubs リソースに適用されます。

> [!NOTE]
> - Azure ロールの割り当ての反映には最大で 5 分かかる場合があることに注意してください。 
> - このコンテンツは、Event Hubs と Apache Kafka 用 Event Hubs の両方を対象としています。 Kafka 用 Event Hubs のサポートの詳細については、[Kafka 用 Event Hubs のセキュリティと認証](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication)に関するセクションを参照してください。


組み込みのロールの定義方法の詳細については、[ロール定義](../role-based-access-control/role-definitions.md#management-and-data-operations)に関するページを参照してください。 Azure カスタム ロールの作成については、「[Azure カスタム ロール](../role-based-access-control/custom-roles.md)」を参照してください。



## <a name="samples"></a>サンプル
- [Microsoft.Azure.EventHubs サンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
    
    これらのサンプルでは、古い **Microsoft.Azure.EventHubs** ライブラリが使用されていますが、最新の **Azure.Messaging.EventHubs** ライブラリを使用するように簡単に更新できます。 古いライブラリから新しいライブラリを使用するようにサンプルを移行する方法については、[Microsoft.Azure.EventHubs から Azure.Messaging.EventHubs への移行のガイド](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)に関する記事を参照してください。
- [ Azure.Messaging.EventHubs サンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    このサンプルは、最新の **Azure.Messaging.EventHubs** ライブラリを使用するように更新されています。
- [Kafka 用 Event Hubs - OAuth サンプル](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)。 


## <a name="next-steps"></a>次のステップ
- Azure 組み込みロールをセキュリティ プリンシパルに割り当てる方法については、[Azure Active Directory を使用して Event Hubs リソースへのアクセスを認証する](authenticate-application.md)方法に関する記事を参照してください。
- [Azure RBAC を使用してカスタム ロールを作成する方法](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)について学習します。
- [EH と共に Azure Active Directory を使用する方法](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)について学習します。

次の関連記事を参照してください。

- [Azure Active Directory を使用してアプリケーションから Azure Event Hubs への要求を認証する](authenticate-application.md)
- [Azure Active Directory を使用して Event Hubs リソースにアクセスするためのマネージド ID を認証する](authenticate-managed-identity.md)
- [Shared Access Signature を使用して Azure Event Hubs に対する要求を認証する](authenticate-shared-access-signature.md)
- [Shared Access Signature を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-shared-access-signature.md)
