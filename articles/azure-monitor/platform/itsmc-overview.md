---
title: IT Service Management Connector 概要
description: この記事では、IT Service Management Connector (ITSMC) の概要を提供します。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: ed13837eabadc38cee50efac3b9340c973c8ea70
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492435"
---
# <a name="it-service-management-connector-overview"></a>IT Service Management Connector 概要

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

IT Service Management Connector (ITSMC) を使用すると、Azure を、サポートされている IT Service Management (ITSM) 製品またはサービスに接続できます。

Azure Log Analytics や Azure Monitor などの Azure サービスには、Azure および Azure 以外のリソースでの問題の検出、分析、トラブルシューティングを行うツールが用意されています。 しかし、問題に関連する作業項目は、一般に、ITSM の製品またはサービス内に存在します。 ITSMC では、より迅速な問題解決に役立つように、Azure と ITSM ツールの間に双方向接続が提供されます。

## <a name="configuration-steps"></a>構成の手順

ITSMC は、次の ITSM ツールとの接続をサポートしています。

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> Cherwell や Provance のお客様には、統合の別の方法として、[Webhook アクション](./action-groups.md#webhook)を使用して Cherwell と Provance のエンドポイントに接続することを提案しています。

ITSMC を使用すると、次のことができます。

-  お使いの Azure アラート (メトリック アラート、アクティビティ ログ アラート、Log Analytics アラート) に基づいて、ITSM ツールで作業項目を作成する。
-  オプションで、ITSM ツールのインシデントと変更要求データを Azure Log Analytics ワークスペースに同期する。

法律条項とプライバシー ポリシーについては、「[Microsoft プライバシー ステートメント](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)」を参照してください。

次の手順を完了すれば、ITSMC の使用を開始できます。

1. [Azure からのアラートを受け取るように ITSM 環境を設定します。](./itsmc-connections.md)
1. [Azure ITSM ソリューションを構成します](./itsmc-definition.md#add-it-service-management-connector)
1. [お使いの ITSM 環境の Azure ITSM コネクタを構成します。](./itsmc-definition.md#create-an-itsm-connection)
1. [ITSM コネクタを活用するようにアクション グループを構成します。](./itsmc-definition.md#define-a-template)

## <a name="next-steps"></a>次のステップ

* [ITSM Connector での問題のトラブルシューティング](./itsmc-resync-servicenow.md)