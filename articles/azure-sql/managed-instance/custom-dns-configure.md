---
title: '[カスタム DNS]'
titleSuffix: Azure SQL Managed Instance
description: このトピックでは、Azure SQL Managed Instance のカスタム DNS の構成オプションについて説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 07/17/2019
ms.openlocfilehash: a54907dd3f7b3fbc06033624f14b12de14d9afb9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831503"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance のカスタム DNS を構成する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance は、Azure [仮想ネットワーク (VNet)](../../virtual-network/virtual-networks-overview.md) 内にデプロイする必要があります。 プライベート ホスト名を SQL Managed Instance から解決する必要のあるシナリオ (データベース メール、クラウドまたはハイブリッド環境内の他の SQL Server インスタンスにリンクされたサーバーなど) がいくつか存在します。 この場合は、Azure の内部でカスタム DNS を構成する必要があります。 

SQL Managed Instance の内部の処理にも同じ DNS が使用されるため、パブリック ドメイン名を解決できるようにカスタム DNS サーバーを構成します。

> [!IMPORTANT]
> プライベート DNS ゾーン内にある場合でも、メール サーバー、SQL Server インスタンス、およびその他のサービスには、常に完全修飾ドメイン名 (FQDN) を使用します。 たとえば、`smtp` は正しく解決されないため、メール サーバーには `smtp.contoso.com` を使用します。 同じ仮想ネットワーク内の SQL Server VM を参照するリンク サーバーまたはレプリケーションを作成する場合も、FQDN と既定の DNS サフィックスが必要です。 たとえば、「 `SQLVM.internal.cloudapp.net` 」のように入力します。 詳細については、「[独自の DNS サーバーを使用する名前解決](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」を参照してください。

> [!IMPORTANT]
> 仮想ネットワークの DNS サーバーを更新しても、すぐには SQL Managed Instance に影響しません。 詳細については、[SQL Managed Instance 仮想クラスター上で仮想ネットワーク DNS サーバーの設定を同期する方法](synchronize-vnet-dns-servers-setting-on-virtual-cluster.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- 概要については、「[Azure SQL Managed Instance とは](sql-managed-instance-paas-overview.md)」を参照してください。
- 新しいマネージド インスタンスの作成方法を紹介するチュートリアルが必要な場合、[マネージド インスタンスの作成](instance-create-quickstart.md)に関するページを参照してください。
- マネージド インスタンスの VNet を構成する方法については、[マネージド インスタンスの VNet 構成](connectivity-architecture-overview.md)に関するページを参照してください。
