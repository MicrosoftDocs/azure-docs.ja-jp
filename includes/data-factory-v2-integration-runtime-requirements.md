---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 6c348b3dd0005eeab154aa2d74abc617cbd1d0cb
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529392"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
データ ストアがオンプレ ミスネットワーク、Azure Virtual Network、または Amazon Virtual Private Cloud 内にある場合は、[セルフホステッド統合ランタイム](../articles/data-factory/create-self-hosted-integration-runtime.md)を設定して接続する必要があります。

データ ストアがマネージド クラウド データ サービスである場合は、Azure Integration Runtime を使用できます。 ファイアウォール規則でホワイトリストに登録されている IP にアクセスが制限される場合は、[Azure Integration Runtime の IP](../articles/data-factory/azure-integration-runtime-ip-addresses.md) を許可リストに追加することを選択できます。 

Data Factory によってサポートされるネットワーク セキュリティ メカニズムやオプションの詳細については、「[データ アクセス戦略](../articles/data-factory/data-access-strategies.md)」を参照してください。
