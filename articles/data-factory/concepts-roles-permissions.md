---
title: Azure Data Factory のロールとアクセス許可
description: データ ファクトリの作成および子リソースの操作に必要なロールとアクセス許可について説明します。
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 00d84a7f3b4a15c9278caac8c2715380dd330482
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930489"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Azure Data Factory のロールとアクセス許可

この記事では、Azure Data Factory リソースの作成と管理に必要なロール、およびそれらのロールによって付与されるアクセス許可について説明します。

## <a name="roles-and-requirements"></a>ロールと要件

Data Factory インスタンスを作成するには、Azure へのサインインに使用するユーザー アカウントが、"*共同作成者*" ロールまたは "*所有者*" ロールのメンバーであるか、Azure サブスクリプションの "*管理者*" である必要があります。 サブスクリプションで自分が持っているアクセス許可を表示するには、Azure portal で右上隅にあるユーザー名を選択し、 **[アクセス許可]** を選択します。 複数のサブスクリプションにアクセスできる場合は、適切なサブスクリプションを選択します。 

データセット、リンクされたサービス、パイプライン、トリガー、および統合ランタイムを含む Data Factory の子リソースを作成および管理するには、次の要件が適用されます。
- Azure portal で子リソースを作成および管理するには、リソース グループ レベル以上で **Data Factory 共同作成者**ロールに属している必要があります。
- PowerShell または SDK を使用して子リソースを作成および管理する場合は、リソース レベル以上での**共同作成者**ロールで十分です。

ロールにユーザーを追加する方法に関するサンプル手順については、[ロールの追加](../billing/billing-add-change-azure-subscription-administrator.md)に関する記事を参照してください。

## <a name="set-up-permissions"></a>アクセス許可の設定

データ ファクトリを作成した後、他のユーザーがデータ ファクトリで操作を行えるようにすることができます。 このアクセスを他のユーザーに付与するには、データ ファクトリを含むリソース グループの **Data Factory 共同作成者**組み込みロールにそのユーザーを追加する必要があります。

### <a name="scope-of-the-data-factory-contributor-role"></a>Data Factory 共同作成者ロールのスコープ

**Data Factory 共同作成者**ロールのメンバーシップによって、ユーザーは次のことを実行できます。
- データ ファクトリと子リソース (データ セット、リンクされたサービス、パイプライン、トリガー、統合ランタイムなど) を作成、編集、削除します。
- Resource Manager テンプレートをデプロイします。 Resource Manager のデプロイは、Azure portal の Data Factory で使用されるデプロイ方法です。
- データ ファクトリの App Insights アラートを管理します。
- サポート チケットを作成します。

このロールの詳細については、「[Data Factory 共同作成者ロール](../role-based-access-control/built-in-roles.md#data-factory-contributor)」を参照してください。

### <a name="resource-manager-template-deployment"></a>Resource Manager テンプレートの展開

リソース グループ レベル以上の **Data Factory 共同作成者**ロールを持つユーザーは、Resource Manager テンプレートをデプロイできます。 結果として、このロールのメンバーは Resource Manager テンプレートを使用して、データ ファクトリとその子リソース (データ セット、リンクされたサービス、パイプライン、トリガー、統合ランタイムなど) の両方をデプロイすることができます。 ただし、このロールのメンバーシップがあっても、ユーザーは他のリソースを作成することはできません。

Azure Repos や GitHub に対するアクセス許可は、Data Factory のアクセス許可から独立しています。 結果として、閲覧者ロールのメンバーにすぎない、リポジトリのアクセス許可を持つユーザーは、Data Factory の子リソースを編集して変更内容をリポジトリにコミットすることは可能ですが、それらの変更を発行することはできません。

> [!IMPORTANT]
> **Data Factory 共同作成者**ロールで Resource Manager テンプレートをデプロイする場合にアクセス許可が昇格されることはありません。 たとえば、Azure 仮想マシンを作成するテンプレートをデプロイするときに、仮想マシンを作成するアクセス許可がない場合、デプロイは認可エラーで失敗します。

### <a name="custom-scenarios-and-custom-roles"></a>カスタム シナリオとカスタム ロール

場合によっては、データ ファクトリ ユーザーごとに異なるアクセス レベルを付与しなければならない場合があります。 例:
- ユーザーが特定のデータ ファクトリに対するアクセス許可のみを持つグループが必要な場合があります。
- ユーザーはデータ ファクトリの監視しかできず、変更はできないグループが必要な場合もあります。

これらのカスタム シナリオは、カスタム ロールを作成してそのロールにユーザーを割り当てることで実現できます。 カスタム ロールの詳細については、「[Azure のカスタム ロール](..//role-based-access-control/custom-roles.md)」を参照してください。

カスタム ロールを使用して実行できる内容を示すいくつかの例を以下に示します。

- ユーザーが Azure portal からリソース グループ内の任意のデータ ファクトリを作成、編集、削除できるようにします。

  ユーザーのためにリソース グループ レベルで **Data Factory 共同作成者**組み込みロールを割り当てます。 サブスクリプション内のデータ ファクトリへのアクセスを許可する場合は、サブスクリプション レベルでそのロールを割り当てます。

- ユーザーによるデータ ファクトリの表示 (読み取り) と監視は許可しつつ、編集や変更は行えないようにします。

  ユーザーのデータ ファクトリ リソースに**閲覧者**組み込みロールを割り当てます。

- Azure portal でユーザーが単一のデータ ファクトリを編集できるようにします。

  このシナリオでは、2 種類のロールの割り当てが必要です。

  1. データ ファクトリ レベルで**共同作成者**組み込みロールを割り当てます。
  2. **Microsoft.Resources/deployments/** のアクセス許可を使用して、カスタム ロールを作成します。 このカスタム ロールをリソース グループ レベルのユーザーに割り当てます。

- ユーザーがリンクされたサービスでのみ接続をテストできるようにします。

    次のアクションのためのアクセス許可を持つカスタム ロールを作成します。**Microsoft.DataFactory/factories/getFeatureValue/read** および **Microsoft.DataFactory/factories/getDataPlaneAccess/read**。 このカスタム ロールを、ユーザーのデータ ファクトリ リソースに割り当てます。

- ユーザーが PowerShell または SDK からデータ ファクトリを更新できるようにし、Azure portal では更新を行えないようにします。

  ユーザーのためにデータ ファクトリ リソースの**共同作成者**組み込みロールを割り当てます。 このロールを持つユーザーは、Azure portal でリソースを閲覧することはできますが、 **[公開]** ボタンと **[すべてを公開]** ボタンにアクセスすることはできません。

## <a name="next-steps"></a>次の手順

- Azure でのロールの詳細 - [ロール定義について](../role-based-access-control/role-definitions.md)

- **Data Factory 共同作成者**ロールの詳細 - [Data Factory 共同作成者ロール](../role-based-access-control/built-in-roles.md#data-factory-contributor)
