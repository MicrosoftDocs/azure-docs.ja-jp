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
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 2ba5794ba647c28cde3b54a1afdfbd0201b23e8e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84706156"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance のカスタム DNS を構成する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance は、Azure [仮想ネットワーク (VNet)](../../virtual-network/virtual-networks-overview.md) 内にデプロイする必要があります。 プライベート ホスト名を SQL Managed Instance から解決する必要のあるシナリオ (データベース メール、クラウドまたはハイブリッド環境内の他の SQL Server インスタンスにリンクされたサーバーなど) がいくつか存在します。 この場合は、Azure の内部でカスタム DNS を構成する必要があります。 

SQL Managed Instance の内部の処理にも同じ DNS が使用されるため、パブリック ドメイン名を解決できるようにカスタム DNS サーバーを構成します。

> [!IMPORTANT]
> プライベート DNS ゾーン内にある場合でも、メール サーバー、SQL Server インスタンス、およびその他のサービスには、常に完全修飾ドメイン名 (FQDN) を使用します。 たとえば、`smtp` は正しく解決されないため、メール サーバーには `smtp.contoso.com` を使用します。 同じ仮想ネットワーク内の SQL Server VM を参照するリンク サーバーまたはレプリケーションを作成する場合も、FQDN と既定の DNS サフィックスが必要です。 たとえば、「 `SQLVM.internal.cloudapp.net` 」のように入力します。 詳細については、「[独自の DNS サーバーを使用する名前解決](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)」を参照してください。

> [!IMPORTANT]
> 仮想ネットワークの DNS サーバーを更新しても、すぐには SQL Managed Instance に影響しません。 SQL Managed Instance の DNS 構成が更新されるのは、DHCP リースの有効期限が切れた後、またはプラットフォームのアップグレード後のどちらか先に発生したときになります。 **ユーザーには、最初のマネージド インスタンスを作成する前に、仮想ネットワークの DNS 構成を設定しておくことをお勧めします。**

## <a name="next-steps"></a>次のステップ

- 概要については、「[Azure SQL Managed Instance とは](sql-managed-instance-paas-overview.md)」を参照してください。
- 新しいマネージド インスタンスの作成方法を紹介するチュートリアルが必要な場合、[マネージド インスタンスの作成](instance-create-quickstart.md)に関するページを参照してください。
- マネージド インスタンスの VNet を構成する方法については、[マネージド インスタンスの VNet 構成](connectivity-architecture-overview.md)に関するページを参照してください。
