---
title: Azure Firewall の インフラストラクチャ FQDN
description: Azure Firewall のインフラストラクチャ FQDN について説明します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 1369a82829b2c80768d746ba92daf2482c1fd7f8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994141"
---
# <a name="infrastructure-fqdns"></a>インフラストラクチャ FQDN

Azure Firewall には、既定で許可されるインフラストラクチャ FQDN 用の組み込みのルール コレクションが含まれています。 これらの FQDN はプラットフォームに固有であり、他の目的には使用できません。 

次のサービスは、組み込みのルール コレクションに含まれています。

- ストレージのプラットフォーム イメージ リポジトリ (PIR) への Compute アクセス
- マネージド ディスク状態ストレージ アクセス
- Azure Diagnostics とログ記録 (MDS)
- Azure Active Directory

## <a name="overriding"></a>オーバーライド 

この組み込みのインフラストラクチャ ルール コレクションをオーバーライドするには、最後に処理される "すべて拒否" アプリケーション ルール コレクションを作成します。 このコレクションは、常にインフラストラクチャ ルール コレクションより先に処理されます。 インフラストラクチャ ルール コレクションに含まれていないものは既定で拒否されます。

## <a name="next-steps"></a>次の手順

- [Azure Firewall のデプロイおよび構成](tutorial-firewall-deploy-portal.md)方法について説明します。