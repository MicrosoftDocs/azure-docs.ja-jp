---
title: 'Bastion を使用したリモート作業: Azure Bastion'
description: このページでは、COVID-19 の世界的流行により、Azure Bastion を利用してリモートで作業できるようにする方法について説明します。
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 515a6f7175aee23c6f720258b7fdaefe76b3b5d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92077951"
---
# <a name="working-remotely-using-azure-bastion"></a>Azure Bastion を使用したリモート作業

Azure Bastion は、インターネット接続のあるユーザーが Azure 仮想マシンにアクセスできるようにすることで、リモート作業シナリオのサポートにおいて重要な役割を果たします。 特に、IT 管理者は、Azure で実行されているアプリケーションをいつでも、世界中のどこからでも管理できます。

>[!NOTE]
>この記事では、Azure Bastion、Azure、Microsoft ネットワーク、および Azure パートナー エコシステムを利用してリモートで作業し、COVID-19 の危機によって直面しているネットワークの問題を軽減する方法について説明します。
>

## <a name="securely-access-virtual-machines"></a>仮想マシンに安全にアクセス

具体的には、Azure Bastion を使用すると、Azure 仮想ネットワーク内の仮想マシンに、パブリック IP アドレスを使用せず Azure portal で直接、安全かつシームレスに RDP/SSH 接続できます。 Azure Bastion のアーキテクチャと主な機能の詳細については、「[Azure Bastion とは](bastion-overview.md)」を参照してください。

Azure Bastion は仮想ネットワークごとにデプロイされます。つまり、企業は 1 つの Azure Bastion を構成および管理して、Azure 仮想ネットワーク内の仮想マシンへのリモート ユーザー アクセスをすばやくサポートできます。 Azure Bastion を作成および管理する方法のガイダンスについては、[Bastion ホストの作成](./tutorial-create-host-portal.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure portal](./tutorial-create-host-portal.md)、[PowerShell](bastion-create-host-powershell.md)、または Azure CLI を使用して Azure Bastion を構成します。

* 詳細については、[Bastion に関する FAQ](bastion-faq.md) に関するページを参照してください。