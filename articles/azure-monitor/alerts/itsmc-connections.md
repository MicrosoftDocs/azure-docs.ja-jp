---
title: Azure Monitor の IT Service Management Connector
description: この記事では、ITSM 製品/サービスを Azure Monitor の IT Service Management Connector (ITSMC) に接続して、ITSM 作業項目を一元的に監視して管理する方法に関する情報を提供します。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 40e737a1ec5fb34cd22a08925143a100d36cdc6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009319"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>ITSM 製品/サービスを IT Service Management Connector に追加する
この記事では、ITSM 製品/サービスと Log Analytics の IT Service Management Connector (ITSMC) の間の接続を構成して、作業項目を一元的に管理する方法に関する情報を提供します。 ITSMC の詳細については、[概要](./itsmc-overview.md)に関する記事をご覧ください。

次の ITSM 製品/サービスがサポートされています。 ITSMC に製品を接続する方法に関する詳細な情報を表示する製品を選択してください。

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> Cherwell や Provance のお客様には、統合の別の方法として、[Webhook アクション](./action-groups.md#webhook)を使用して Cherwell と Provance のエンドポイントに接続することを提案しています。

## <a name="ip-ranges-for-itsm-partners-connections"></a>ITSM パートナー接続の IP 範囲
パートナーの ITSM ツールからの ITSM 接続を許可するために ITSM IP アドレスを一覧表示するには、LogAnalytics ワークスペースが属している Azure リージョンのパブリック IP 範囲全体を一覧表示することをお勧めします。 [詳細はこちら](https://www.microsoft.com/en-us/download/details.aspx?id=56519) 米国東部、西ヨーロッパ、米国東部 2、米国西部 2、米国中南部のリージョンの場合、顧客は ActionGroup ネットワーク タグのみを一覧表示できます。

## <a name="next-steps"></a>次のステップ

* [ITSM Connector での問題のトラブルシューティング](./itsmc-resync-servicenow.md)
