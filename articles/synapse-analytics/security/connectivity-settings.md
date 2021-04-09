---
title: Azure Synapse の接続設定
description: Azure Synapse Analytics で接続設定を構成する方法の説明記事
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 03/15/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: e0d8a8e3320b49b6fbe3e8ab66c0b4569fac9afd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587935"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Azure Synapse Analytics の接続設定

この記事では、Azure Synapse Analytics の接続設定と、必要に応じてそれらを構成する方法について説明します。


## <a name="connection-policy"></a>接続ポリシー
Azure Synapse Analytics での Synapse SQL の接続ポリシーは、"*既定値*" に設定されています。 Azure Synapse Analytics でこれを変更することはできません。 Azure Synapse Analytics での Synapse SQL への接続に対する影響については、[こちら](../../azure-sql/database/connectivity-architecture.md#connection-policy)を参照してください。 

## <a name="minimal-tls-version"></a>TLS の最小バージョン
Azure Synapse Analytics の Synapse SQL では、すべての TLS バージョンを使用した接続が許可されます。 Azure Synapse Analytics の Synapse SQL に対して最小 TLS バージョンを設定することはできません。

## <a name="next-steps"></a>次のステップ

[Azure Synapse ワークスペース](./synapse-workspace-ip-firewall.md)を作成する