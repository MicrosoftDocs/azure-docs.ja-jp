---
title: 仮想ネットワークの作成
titleSuffix: Azure SQL Managed Instance
description: この記事では、Azure SQL Managed Instance のデプロイをサポートするように構成された仮想ネットワークの作成方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 09/12/2019
ms.openlocfilehash: 2a23fc0b769727cab5a28d3d313a7791bcfa6eee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91617708"
---
# <a name="create-a-virtual-network-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance 用の仮想ネットワークを作成する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、Azure SQL Managed Instance のデプロイ先にできる有効な仮想ネットワークとサブネットの作成方法について説明します。

Azure SQL Managed Instance は、Azure [仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)内にデプロイする必要があります。 このデプロイにより、次のシナリオが可能になります。

- セキュリティで保護されたプライベート IP アドレス
- オンプレミス ネットワークから SQL Managed Instance への直接接続
- リンク サーバーまたは別のオンプレミス データ ストアへの SQL Managed Instance の接続
- Azure リソースへの SQL Managed Instance の接続  

> [!NOTE]
> 最初のインスタンスをデプロイする前に、[SQL Managed Instance のために使用するサブネットのサイズを決める](vnet-subnet-determine-size.md)必要があります。 サブネットは、いったんリソースを配置するとサイズを変更できなくなります。
>
> 既存の仮想ネットワークを使用する予定の場合は、SQL Managed Instance に合うようにそのネットワーク構成を変更する必要があります。 詳細については、[SQL Managed Instance 用の既存の仮想ネットワークの変更](vnet-existing-add-subnet.md)を参照してください。
>
> マネージド インスタンスが作成された後は、マネージド インスタンスまたは仮想ネットワークを別のリソース グループまたはサブスクリプションに移動することは、サポートされていません。  また、マネージド インスタンスの別のサブネットへの移動もサポートされていません。
>

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

仮想ネットワークの作成と構成を行う最も簡単な方法は、Azure Resource Manager のデプロイ テンプレートを使用することです。

1. Azure portal にサインインします。

2. **[Deploy to Azure]\(Azure にデプロイ\)** を選択します。

   [![[Deploy to Azure]\(Azure にデプロイ\) というラベルが付けられたボタンが表示されている画像。](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json)

   このボタンから、SQL Managed Instance をデプロイできるネットワーク環境を構成するために使用できるフォームが開かれます。

   > [!Note]
   > この Azure Resource Manager テンプレートでは、2 つのサブネットを持つ仮想ネットワークをデプロイします。 1 つは **ManagedInstances** と呼ばれるサブネットであり、SQL Managed Instance 用に予約され、ルート テーブルが事前に構成されています。 もう 1 つのサブネットは **Default** と呼ばれ、SQL Managed Instance にアクセスする必要がある他のリソース (Azure Virtual Machines など) に使用されます。

3. ネットワーク環境を構成します。 次のフォームで、ネットワーク環境のパラメーターを構成できます。

   ![Azure ネットワークを構成するための Resource Manager テンプレート](./media/virtual-network-subnet-create-arm-template/create-mi-network-arm.png)

   仮想ネットワークとサブネットの名前を変更することや、ネットワーク リソースに関連付けられた IP 範囲を調整することができます。 **[購入]** ボタンを選択すると、このフォームにより環境が作成および構成されます。 2 つのサブネットを必要としない場合は、既定のサブネットを削除できます。

## <a name="next-steps"></a>次のステップ

- 概要については、[SQL Managed Instance](sql-managed-instance-paas-overview.md) に関するページを参照してください。
- [SQL Managed Instance の接続アーキテクチャ](connectivity-architecture-overview.md)について確認します。
- [SQL Managed Instance 用に既存の仮想ネットワークを変更する](vnet-existing-add-subnet.md)方法を確認します。
- 仮想ネットワークを作成し、マネージド インスタンスを作成して、データベース バックアップからデータベースを復元する方法を示すチュートリアルについては、[マネージド インスタンスの作成](instance-create-quickstart.md)に関するページを参照してください。
- DNS の問題については、[カスタム DNS の構成](custom-dns-configure.md)に関するページを参照してください。
