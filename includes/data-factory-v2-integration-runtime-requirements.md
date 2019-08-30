---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966378"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
データ ストアが次のいずれかの方法で構成されている場合、このデータ ストアに接続するには、[セルフホステッド統合ランタイム](../articles/data-factory/create-self-hosted-integration-runtime.md)を設定する必要があります。

- データ ストアは、オンプレミスのネットワーク内、Azure Virtual Network 内、または Amazon Virtual Private Cloud 内に配置されています。
- データ ストアは、ファイアウォール規則でホワイトリストに登録されている IP にアクセスが制限されるマネージド クラウド データ サービスです。
