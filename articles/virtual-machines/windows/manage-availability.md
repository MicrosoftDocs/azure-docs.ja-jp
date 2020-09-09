---
title: Azure で Windows VM の可用性を管理する
description: 複数の仮想マシンを使って Azure の Windows アプリケーションの高い可用性を確保する方法について説明します。
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/27/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a3c37d42cc6cda644063b098cb53f05079455339
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86508527"
---
# <a name="manage-the-availability-of-windows-virtual-machines-in-azure"></a>Azure での Windows 仮想マシンの可用性の管理 

複数の仮想マシンを設定および管理して、Azure の Windows アプリケーションの高い可用性を確保する方法について説明します。 [Linux 仮想マシンの可用性を管理する](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)こともできます。

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>次のステップ
仮想マシンの負荷分散の詳細については、 [仮想マシンの負荷分散](tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。

IaaS の SQL Server で N 層アプリケーションを実行するための参照アーキテクチャを表示する

* [SQL Server を使用した Azure の Windows N 層アプリケーション](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [高可用性を得るために複数の Azure リージョンで N 層アプリケーションを実行する](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
