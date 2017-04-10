---
title: "OpenSUSE VM への MySQL のインストール | Microsoft Docs"
description: "Azure 上の OpenSUSE Linux 仮想マシンに MySQL をインストールする方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/19/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 01b798a25575b66f89057315ce80d6cc0cde53b5
ms.lasthandoff: 03/27/2017


---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Azure 上で OpenSUSE Linux を実行する仮想マシンへの MySQL のインストール
[MySQL][MySQL] は広く普及しているオープン ソースの SQL Database です。 このチュートリアルでは、OpenSUSE Linux を実行する仮想マシンを作成してから、MySQL をインストールする方法を説明します。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

<br>

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>OpenSUSE Linux を実行する仮想マシンの作成
[!INCLUDE [create-and-configure-opensuse-vm-in-portal](../../../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>仮想マシンへの MySQL のインストールと実行
[!INCLUDE [install-and-run-mysql-on-opensuse-vm](../../../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>次のステップ
MySQL の詳細については、[MySQL のドキュメント][MySQLDocs]を参照してください。

[MySQLDocs]:http://dev.mysql.com/doc/index-topic.html
[MySQL]:http://www.mysql.com


