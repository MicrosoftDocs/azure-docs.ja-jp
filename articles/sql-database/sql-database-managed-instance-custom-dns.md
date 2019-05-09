---
title: Azure SQL Database Managed Instance のカスタム DNS | Microsoft Docs
description: このトピックでは、Azure SQL Database マネージド インスタンスのカスタム DNS の構成オプションについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 12/13/2018
ms.openlocfilehash: bb5890b883b6062d834b928bff28a26a3664fb64
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787902"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance のカスタム DNS の構成

Azure SQL Database Managed Instance は、Azure [仮想ネットワーク (VNet)](../virtual-network/virtual-networks-overview.md) 内にデプロイする必要があります。 プライベート ホスト名をマネージド インスタンスから解決する必要のあるシナリオ (データベース メール、クラウドまたはハイブリッド環境内の他の SQL インスタンスにリンクされたサーバーなど) がいくつか存在します。 この場合は、Azure の内部でカスタム DNS を構成する必要があります。 マネージド インスタンスはその内側の作業に同じ DNS を使用するため、仮想ネットワークの DNS 構成をマネージド インスタンスと互換性があるようにする必要があります。

   > [!IMPORTANT]
   > プライベート DNS ゾーン内にある場合でも、メール サーバー、SQL サーバー、およびその他のサービスには必ず完全修飾ドメイン名 (FQDN) を使用します。 たとえば、単に `smtp` では正しく解決されないため、メール サーバーには `smtp.contoso.com` を使用します。

カスタム DNS 構成をマネージド インスタンスと互換性があるようにするには、以下が必要です。

- パブリック ドメイン名を解決できるようにカスタム DNS サーバーを構成する
- Azure 回帰リゾルバーの DNS IP アドレス 168.63.129.16 を、仮想ネットワーク DNS リストの末尾に配置する

## <a name="setting-up-custom-dns-servers-configuration"></a>カスタム DNS サーバー構成の設定

1. Azure Portal で、VNet のカスタム DNS オプションを見つけます。

   ![カスタム DNS オプション](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png)

2. [カスタム] に切り替え、カスタム DNS サーバーの IP アドレスや Azure の再帰リゾルバーの IP アドレス 168.63.129.16 を入力します。

   ![カスタム DNS オプション](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png)

   > [!IMPORTANT]
   > Azure の再帰リゾルバーを DNS リストに設定しないと、何らかの理由でカスタム DNS サーバーが使用不能になった場合に、マネージド インスタンスが障害状態になる可能性があります。 その状態から回復するには、準拠するネットワーク ポリシーを使用して VNet 内に新しいインスタンスを作成し、インスタンス レベル データを作成して、データベースを復元することが必要になる可能性があります。 Azure の再帰リゾルバーを DNS リストの最後のエントリとして設定すると、すべてのカスタム DNS サーバーが失敗した場合でも、パブリック名を解決できます。

## <a name="next-steps"></a>次の手順

- 概要については、[マネージド インスタンス](sql-database-managed-instance.md)に関するページを参照してください。
- 新しいマネージド インスタンスの作成方法を紹介するチュートリアルが必要な場合、[マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)に関するページを参照してください。
- マネージド インスタンスの VNet を構成する方法については、[マネージド インスタンスの VNet 構成](sql-database-managed-instance-connectivity-architecture.md)に関するページを参照してください。
