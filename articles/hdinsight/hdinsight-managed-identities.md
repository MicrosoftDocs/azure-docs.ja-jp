---
title: Azure HDInsight のマネージド ID
description: Azure HDInsight でのマネージド ID の実装の概要を説明します。
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: hrasheed
ms.openlocfilehash: 9386bcb8e455bff5ceed1fccdf55874caf7b6507
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176148"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Azure HDInsight のマネージド ID

マネージド ID とは、資格情報が Azure によって管理されている Azure Active Directory (Azure AD) に登録されている ID です。 マネージド ID を使用すると、Azure AD にサービス プリンシパルを登録したり、証明書などの資格情報を保持したりする必要はありません。

Azure HDInsight でマネージド ID を使用すると、クラスターから Azure AD Domain Services へのアクセス、Azure Key Vault へのアクセス、または Azure Data Lake Storage Gen2 内のファイルへのアクセスが可能になります。

マネージド ID には、ユーザー割り当てとシステム割り当ての 2 種類があります。 Azure HDInsight では、ユーザー割り当てマネージド ID が使用されます。 ユーザー割り当てマネージド ID はスタンドアロン Azure リソースとして作成され、1 つ以上の Azure サービス インスタンスに割り当てることができます。 これに対して、システム割り当てマネージド ID は Azure AD 内で作成され、自動的に特定の Azure サービス インスタンスで直接有効化されます。 システム割り当てマネージド ID の有効期間は、そのマネージド ID が有効になっているサービス インスタンスの有効期間に関連付けられます。

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight のマネージド ID 実装

Azure HDInsight では、マネージド ID はクラスターのノードごとにプロビジョニングされます。 ただし、これらの ID コンポーネントは HDInsight サービスのみが使用できます。 HDInsight クラスター ノードにインストールされているマネージド ID を使用してアクセス トークンを生成する方法は、現在サポートされていません。 一部の Azure サービスでは、マネージド ID は、ユーザーが他の Azure サービスと独自にやり取りするためのアクセス トークンの取得に使用できるエンドポイントで実装されます。

## <a name="create-a-managed-identity"></a>マネージド ID の作成

マネージド ID は、次のいずれかの方法で作成できます。

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

マネージド ID を構成するための残りの手順は、その ID が使用されるシナリオによって異なります。

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Azure HDInsight におけるマネージド ID のシナリオ

マネージド ID は、Azure HDInsight のさまざまなシナリオで使用されます。 セットアップと構成の詳細な手順については、次の関連ドキュメントをご覧ください。

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-managed-identity)
* [Enterprise セキュリティ パッケージ](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka Bring Your Own Key (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>次の手順

* [Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)