---
title: Azure Firewall の インフラストラクチャ FQDN
description: Azure Firewall のインフラストラクチャ FQDN について説明します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: 5b134058e1924bae030338411226a9a6aff46557
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130199"
---
# <a name="infrastructure-fqdns"></a>インフラストラクチャ FQDN

Azure Firewall には、既定で許可されるインフラストラクチャ FQDN 用の組み込みのルール コレクションが含まれています。 これらの FQDN はプラットフォームに固有であり、他の目的には使用できません。 

次のサービスは、組み込みのルール コレクションに含まれています。

- ストレージのプラットフォーム イメージ リポジトリ (PIR) への Compute アクセス
- マネージド ディスク状態ストレージ アクセス
- Azure Diagnostics とログ記録 (MDS)

## <a name="overriding"></a>オーバーライド 

この組み込みのインフラストラクチャ ルール コレクションをオーバーライドするには、最後に処理される "すべて拒否" アプリケーション ルール コレクションを作成します。 このコレクションは、常にインフラストラクチャ ルール コレクションより先に処理されます。 インフラストラクチャ ルール コレクションに含まれていないものは既定で拒否されます。

## <a name="next-steps"></a>次の手順

- [Azure Firewall のデプロイおよび構成](tutorial-firewall-deploy-portal.md)方法について説明します。