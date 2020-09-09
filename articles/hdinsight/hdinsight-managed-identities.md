---
title: Azure HDInsight のマネージド ID
description: Azure HDInsight でのマネージド ID の実装の概要を説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 07a8c26f7fc314680c51270ebafe03d4e3a84757
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88749859"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Azure HDInsight のマネージド ID

マネージド ID とは、資格情報が Azure によって管理されている Azure Active Directory (Azure AD) に登録されている ID です。 マネージド ID を使用すると、Azure AD 内でサービス プリンシパルを登録する必要はありません。 または、証明書などの資格情報を保持します。

Azure HDInsight では、必要に応じて、マネージド ID を使用して Azure AD ドメイン サービスにアクセスしたり、 Azure Data Lake Storage Gen2 のファイルにアクセスしたりします。

マネージド ID には、ユーザー割り当てとシステム割り当ての 2 種類があります。 Azure HDInsight では、ユーザー割り当てマネージド ID のみがサポートされます。 HDInsight では、システム割り当てマネージド ID はサポートされません。 ユーザー割り当てマネージド ID は、スタンドアロン Azure リソースとして作成されます。その後、これを 1 つ以上の Azure サービス インスタンスに割り当てることができます。 これに対して、システム割り当てマネージド ID は Azure AD 内で作成され、自動的に特定の Azure サービス インスタンスで直接有効化されます。 システム割り当てマネージド ID の有効期間は、そのマネージド ID が有効になっているサービス インスタンスの有効期間に関連付けられます。

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight のマネージド ID 実装

Azure HDInsight では、マネージド ID は内部コンポーネントの HDInsight サービスでのみ使用できます。 現在、外部サービスにアクセスする目的で、HDInsight クラスター ノードにインストールされているマネージド ID を使用してアクセス トークンを生成するための方法はサポートされていません。 コンピューティング VM など、一部の Azure サービスでは、マネージド ID は、アクセス トークンの取得に使用できるエンドポイントで実装されます。 このエンドポイントは現在、HDInsight ノードでは使用できません。

分析ジョブ (SCALA ジョブなど) にシークレットやパスワードを置かないようにアプリケーションをブートストラップする必要がある場合、スクリプト アクションを使用してクラスター ノードに独自の証明書を配布し、その後、その証明書を使用して (たとえば、Azure KeyVault にアクセスするための) アクセス トークンを取得できます。

## <a name="create-a-managed-identity"></a>マネージド ID の作成

マネージド ID は、次のいずれかの方法で作成できます。

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

マネージド ID を構成するための残りの手順は、その ID が使用されるシナリオによって異なります。

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Azure HDInsight におけるマネージド ID のシナリオ

マネージド ID は、Azure HDInsight のさまざまなシナリオで使用されます。 セットアップと構成の詳細な手順については、次の関連ドキュメントをご覧ください。

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Enterprise セキュリティ パッケージ](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [お客様が管理するキー ディスクの暗号化](disk-encryption.md)

## <a name="faq"></a>よく寄せられる質問

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>クラスターの作成後にマネージド ID を削除するとどうなりますか?

マネージド ID が必要になった時に、クラスターに問題が発生します。 現時点では、クラスターの作成後にマネージド ID を更新または変更する方法はありません。 そのため、クラスターの実行時にはマネージド ID が削除されないようにすることをお勧めします。 または、クラスターを再作成して新しいマネージド ID を割り当てることもできます。

## <a name="next-steps"></a>次のステップ

* [Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)
