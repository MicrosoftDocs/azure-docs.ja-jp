---
title: Azure SQL Database Managed Instance の仮想ネットワークを作成する | Microsoft Docs
description: この記事では、Azure SQL Database Managed Instance のデプロイ先にできる仮想ネットワークの作成方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: 2fd4055f1a2ec7b5b29d1a303f1c9ac3260f4cca
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70958469"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance の仮想ネットワークを作成する

この記事では、Azure SQL Database Managed Instance のデプロイ先にできる有効な仮想ネットワークおよびサブネットの作成方法について説明します。

Azure SQL Database Managed Instance は、Azure [仮想ネットワーク](../virtual-network/virtual-networks-overview.md)内にデプロイする必要があります。 このデプロイにより、次のシナリオが可能になります。

- セキュリティで保護されたプライベート IP アドレス
- オンプレミス ネットワークからマネージド インスタンスへの直接接続
- リンク サーバーまたは別のオンプレミス データ ストアへのマネージド インスタンスの接続
- Azure リソースへのマネージド インスタンスの接続  

> [!Note]
> 最初のインスタンスをデプロイする前に、[Managed Instance に使用するサブネットのサイズを決める](sql-database-managed-instance-determine-size-vnet-subnet.md)必要があります。 サブネットは、いったんリソースを配置するとサイズを変更できなくなります。
>
> 既存の仮想ネットワークを使用する予定の場合は、お使いのマネージド インスタンスに合わせて、そのネットワーク構成を変更する必要があります。 詳細については、「[マネージド インスタンスの既存の仮想ネットワークを変更する](sql-database-managed-instance-configure-vnet-subnet.md)」を参照してください。
>
> マネージド インスタンスを作成した後は、マネージド インスタンスまたは VNet を別のリソース グループまたはサブスクリプションに移動することはできません。


## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

仮想ネットワークの作成と構成を行う最も簡単な方法は、Azure Resource Manager のデプロイ テンプレートを使用することです。

1. Azure ポータルにサインインします。

2. **[Deploy to Azure]\(Azure にデプロイ\)** を選択します。

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   このボタンにより、Managed Instance をデプロイできるネットワーク環境を構成するために使用できるフォームが開きます。

   > [!Note]
   > この Azure Resource Manager テンプレートでは、2 つのサブネットを持つ仮想ネットワークをデプロイします。 1 つは **ManagedInstances** と呼ばれるサブネットであり、マネージド インスタンス用に予約され、ルート テーブルが事前に構成されています。 もう 1 つのサブネットは **Default** と呼ばれ、Managed Instance にアクセスする必要がある他のリソース (Azure Virtual Machines など) に使用されます。

3. ネットワーク環境を構成します。 次のフォームで、ネットワーク環境のパラメーターを構成できます。

   ![Azure ネットワークを構成するための Resource Manager テンプレート](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   仮想ネットワークとサブネットの名前を変更することや、ネットワーク リソースに関連付けられた IP 範囲を調整することができます。 **[購入]** ボタンを選択すると、このフォームにより環境が作成および構成されます。 2 つのサブネットを必要としない場合は、既定のサブネットを削除できます。

## <a name="next-steps"></a>次の手順

- 概要については、[マネージド インスタンス](sql-database-managed-instance.md)に関するページを参照してください。
- [Managed Instance の接続アーキテクチャ](sql-database-managed-instance-connectivity-architecture.md)について確認します。
- [Managed Instance の既存の仮想ネットワークを変更する](sql-database-managed-instance-configure-vnet-subnet.md)方法を確認します。
- 仮想ネットワークを作成し、マネージド インスタンスを作成して、データベース バックアップからデータベースを復元する方法を示すチュートリアルについては、[Azure SQL Database マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)に関するページを参照してください。
- DNS の問題については、[カスタム DNS の構成](sql-database-managed-instance-custom-dns.md)に関するページを参照してください。
