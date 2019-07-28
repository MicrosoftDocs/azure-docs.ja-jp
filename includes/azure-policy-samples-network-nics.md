---
title: インクルード ファイル
description: インクルード ファイル
services: azure-policy
author: dcthegeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: f189843e865863d9b4088363e691ebc42ad9f2a9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181321"
---
### <a name="network-interfaces"></a>ネットワーク インターフェイス

|  |  |
|---------|---------|
| [すべての NIC 上の NSG X](../articles/governance/policy/samples/nsg-on-nic.md) | 特定のネットワーク セキュリティ グループが、すべての仮想ネットワーク インターフェイスで使用される必要があります。 使用するネットワーク セキュリティ グループの ID を指定します。 |
| [VM ネットワーク インターフェイスでの承認されたサブネットの使用](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | 承認されたサブネットがネットワーク インターフェイスで使用される必要があります。 承認されたサブネットの ID を指定します。 |
| [VM ネットワーク インターフェイスでの承認された vNet の使用](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | 承認された仮想ネットワークがネットワーク インターフェイスで使用される必要があります。 承認された仮想ネットワークの ID を指定します。 |