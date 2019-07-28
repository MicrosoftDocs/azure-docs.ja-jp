---
title: Porlicy のサンプル テンプレート | Microsoft Docs
description: 仮想ネットワーク用の Azure Policy のサンプル テンプレート。
services: virtual-network
documentationcenter: ''
author: kumudd
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 8228da778e6cf6a874868ef0206092913908352d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694289"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>仮想ネットワーク用の Azure Policy のサンプル テンプレート

次の表に、[Azure Policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) のサンプルへのリンクを示します。 サンプルは、[Azure Policy のサンプル リポジトリ](https://github.com/Azure/azure-policy)にあります。

| | |
|---|---|
|**ネットワーク**||
| [すべての NIC 上の NSG X](../governance/policy/samples/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 特定のネットワーク セキュリティ グループが、すべての仮想ネットワーク インターフェイスで使用される必要があります。 使用するネットワーク セキュリティ グループの ID を指定します。 |
| [すべてのサブネット上の NSG X](../governance/policy/samples/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 特定のネットワーク セキュリティ グループが、すべての仮想サブネットで使用される必要があります。 使用するネットワーク セキュリティ グループの ID を指定します。 |
| [ルート テーブルなし](../governance/policy/samples/no-user-defined-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |仮想ネットワークがルート テーブルを使ってデプロイされることを禁止します。 |
| [VM ネットワーク インターフェイスでの承認されたサブネットの使用](../governance/policy/samples/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 承認されたサブネットがネットワーク インターフェイスで使用される必要があります。 承認されたサブネットの ID を指定します。 |
| [VM ネットワーク インターフェイスでの承認された vNet の使用](../governance/policy/samples/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 承認された仮想ネットワークがネットワーク インターフェイスで使用される必要があります。 承認された仮想ネットワークの ID を指定します。 |
|**監視**||
| [診断設定の監査](../governance/policy/samples/audit-diagnostic-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 指定されたリソースの種類で診断設定が有効になっていない場合に監査を行います。 リソースの種類の配列を指定して、診断設定が有効になっているかどうかを確認します。 |
|**名前およびテキストの表記規則**||
| [複数の名前パターンを許可する](../governance/policy/samples/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 多数の名前パターンのいずれかをリソースに使うことを許可します。 |
| [類似パターンを要求する](../governance/policy/samples/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | リソース名がパターンの *like* 条件を満たすことを保証します。 |
| [一致パターンを要求する](../governance/policy/samples/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | リソース名が、指定された名前付けパターンと一致することを保証します。 |
| [タグ一致パターンを要求する](../governance/policy/samples/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | タグの値がテキストのパターンと一致することを保証します。 |
|**タグ**||
| [課金タグ ポリシーのイニシアティブ](../governance/policy/samples/billing-tags-policy-initiative.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | コスト センターと製品名に対して指定したタグの値が必要です。 組み込みのポリシーを使用して、必要なタグを強制的に適用します。 タグに対して必須の値を指定します。  |
| [リソース グループへのタグと値の強制](../governance/policy/samples/enforce-tag-on-resource-groups.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 1 つのリソース グループに 1 つのタグと値が必要です。 必要なタグ名と値を指定します。  |
| [タグとその値を強制](../governance/policy/samples/enforce-tag-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 指定したタグの名前と値が必要です。 強制的に適用するタグ名と値を指定します。  |
| [タグとその既定値の適用](../governance/policy/samples/apply-tag-default-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 指定されたタグがない場合、そのタグ名と値を付加します。 適用するタグ名と値を指定します。  |
|**全般**||
| [許可される場所](../governance/policy/samples/allowed-locations.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | すべてのリソースが承認済みの場所にデプロイされる必要があります。 承認された場所の配列を指定します。  |
| [許可されるリソースの種類](../governance/policy/samples/allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 必ず承認されたリソースの種類のみがデプロイされるようにします。 許可されているリソースの種類の配列を指定します。  |
| [許可されないリソースの種類](../governance/policy/samples/not-allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 指定されたリソースの種類のデプロイを禁止します。 ブロックするリソースの種類の配列を指定します。  |