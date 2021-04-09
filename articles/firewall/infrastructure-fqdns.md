---
title: Azure Firewall の インフラストラクチャ FQDN
description: Azure Firewall には、既定で許可されるインフラストラクチャ FQDN 用の組み込みのルール コレクションが含まれています。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74168978"
---
# <a name="infrastructure-fqdns"></a>インフラストラクチャ FQDN

Azure Firewall には、既定で許可されるインフラストラクチャ FQDN 用の組み込みのルール コレクションが含まれています。 これらの FQDN はプラットフォームに固有であり、他の目的には使用できません。 

次のサービスは、組み込みのルール コレクションに含まれています。

- ストレージのプラットフォーム イメージ リポジトリ (PIR) への Compute アクセス
- マネージド ディスク状態ストレージ アクセス
- Azure Diagnostics とログ記録 (MDS)

## <a name="overriding"></a>オーバーライド 

この組み込みのインフラストラクチャ ルール コレクションをオーバーライドするには、最後に処理される "すべて拒否" アプリケーション ルール コレクションを作成します。 このコレクションは、常にインフラストラクチャ ルール コレクションより先に処理されます。 インフラストラクチャ ルール コレクションに含まれていないものは既定で拒否されます。

## <a name="next-steps"></a>次のステップ

- [Azure Firewall のデプロイおよび構成](tutorial-firewall-deploy-portal.md)方法について説明します。