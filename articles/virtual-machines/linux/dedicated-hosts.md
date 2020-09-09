---
title: 仮想マシン用 Azure 専用ホストの概要
description: Azure 専用ホストを使用して仮想マシンをデプロイする方法の詳細について説明します。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 07/28/2020
ms.author: cynthn
ms.openlocfilehash: 97e65528a7f049c213ec077c83da1b7672469d90
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388171"
---
# <a name="azure-dedicated-hosts-for-virtual-machines"></a>仮想マシン用 Azure 専用ホスト

Azure 専用ホストは、1 つの Azure サブスクリプションに対して専用の物理サーバーを提供するサービスであり、1 つ以上の仮想マシンをホストできます。 専用ホストは、データ センターで使用されるものと同じ物理サーバーであり、リソースとして提供されます。 リージョン、可用性ゾーン、障害ドメイン内に専用ホストをプロビジョニングできます。 次に、プロビジョニングされたホストに VM を直接配置し、ニーズに最も合った構成にすることができます。



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>次のステップ

- [Azure CLI](dedicated-hosts-cli.md)、[ポータル](dedicated-hosts-portal.md)、[PowerShell](../windows/dedicated-hosts-powershell.md) を使用して専用ホストをデプロイできます。

- 詳細については、[専用ホスト](dedicated-hosts.md)の概要に関するページを参照してください。

- [こちら](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)には、リージョン内の回復性を最大にするためにゾーンと障害ドメインの両方を使用するサンプル テンプレートがあります。

- また、[Azure Dedicated Host](../prepay-dedicated-hosts-reserved-instances.md)の予約インスタンスを使用してコストを節約することもできます。
