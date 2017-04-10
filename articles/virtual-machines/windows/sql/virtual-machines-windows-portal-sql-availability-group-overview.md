---
title: "SQL Server 可用性グループ - Azure Virtual Machines - 概要 | Microsoft Docs"
description: "この記事では、Azure Virtual Machines での SQL Server 可用性グループについて説明します。"
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 3bf8cc49223e3e612a2343e8a3383bbbe02dcd82
ms.openlocfilehash: 3d18551823abbb9beca4212aa8095b6757a080cb


---

# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Azure Virtual Machines での SQL Server Always On 可用性グループの概要 #

この記事では、Azure Virtual Machines での SQL Server 可用性グループについて説明します。 

Azure Virtual Machines での Always On 可用性グループは、オンプレミスの Always On 可用性グループに似ています。 詳細については、「 [AlwaysOn 可用性グループ (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx)」をご覧ください。 

次の図は、Azure Virtual Machines での完全な SQL Server 可用性グループの一部を示したものです。

![可用性グループ](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Azure Virtual Machines での可用性グループの重要な違いは、Azure Virtual Machines には[ロード バランサー](../../../load-balancer/load-balancer-overview.md)が必要なことです。 ロード バランサーは、可用性グループ リスナーの IP アドレスを保持しています。 複数の可用性グループがある場合は、グループごとにリスナーが必要です。 1 つのロード バランサーで複数のリスナーをサポートできます。

Azure Virtual Machines に SQL Server 可用性グループを作成する準備ができたら、次のチュートリアルをご覧ください。

## <a name="automatically-create-an-availability-group-from-a-template"></a>テンプレートから可用性グループを自動的に作成する

[Azure VM での AlwaysOn 可用性グループの自動構成 - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>Azure Portal を使って手動で可用性グループを作成する

テンプレートを使わずに、自分で仮想マシンを作成することもできます。 最初に、前提条件を満たした後、可用性グループを作成します。 以下のトピックをご覧ください。 

- [Azure Virtual Machines で Always On 可用性グループを作成するための前提条件を満たす](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Always On 可用性グループを作成して可用性と障害復旧を向上させる](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>次のステップ

[異なるリージョンの Azure Virtual Machines に SQL Server Always On 可用性グループを構成します](virtual-machines-windows-portal-sql-availability-group-dr.md)。



<!--HONumber=Jan17_HO3-->


