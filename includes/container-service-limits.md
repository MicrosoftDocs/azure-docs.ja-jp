---
title: インクルード ファイル
description: インクルード ファイル
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 62eb75ef18d3ac81be65783e57c21c0aefd7a429
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554817"
---
| リソース | 既定の制限 |
| --- | :--- |
| サブスクリプションあたりの最大クラスター数 | 100 |
| クラスターあたりの最大ノード数 | 100 |
| ノードあたりの最大ポッド数:Kubenet を使用した[基本的なネットワーク][basic-networking] | 110 |
| ノードあたりの最大ポッド数:Azure Container Networking Interface を使った[高度なネットワーク][advanced-networking] | Azure CLI でのデプロイ: 30<sup>1</sup><br />Azure Resource Manager テンプレート:30<sup>1</sup><br />ポータルのデプロイ: 30 |

<sup>1</sup>Azure CLI または Resource Manager テンプレートを使用して Azure Kubernetes Service (AKS) クラスターをデプロイする場合、この値は最大でノードあたり 110 ポッドまで構成できます。 AKS クラスターを既にデプロイしている場合、または Azure portal を使用してクラスターをデプロイする場合は、ノードあたりの最大ポッド数を構成できません。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
