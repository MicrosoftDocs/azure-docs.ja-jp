---
title: インクルード ファイル
description: インクルード ファイル
documentationcenter: partner-center-commercial-marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 08/24/2020
ms.custom: include file
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 7709f26b30d41b932569227b8b6f05b5725dc98f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88815668"
---
パートナー センターを通じてオファーをコマーシャル マーケットプレースに公開する場合、オファーを CRM システムに接続する必要があります。 この方法により、顧客が関心を持ったり、製品をデプロイしたりしたときに、すぐに顧客の連絡先情報を受け取ることができます。

1. 顧客リードの送信先となるリードのターゲットを選択します。 次の CRM システムがサポートされています。

    * [Dynamics 365](../commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    * [Marketo](../commercial-marketplace-lead-management-instructions-marketo.md)
    * [Salesforce](../commercial-marketplace-lead-management-instructions-salesforce.md)

    ご使用の CRM システムがこの一覧で明示的にサポートされていない場合は、次のオプションを使用して顧客のリード データを格納できます。 その後、このデータを CRM システムにエクスポートまたはインポートできます。

    * [Azure テーブル](../commercial-marketplace-lead-management-instructions-azure-table.md)
    * [HTTPS エンドポイント](../commercial-marketplace-lead-management-instructions-https.md)

1. 上記の該当するリンク先のドキュメントを参照して、リードの送信先を設定し、コマーシャル マーケットプレースのオファーからリードを受信する方法を確認してください。
1. オファーをリードの送信先に接続したら、パートナー センターでオファーに対して **[発行]** を選択します。 接続を検証し、テスト リードを送信します。 公開前にオファーを確認したときに、プレビュー環境でオファーを自分で入手してみることで、リードの接続をテストすることもできます。
1. リードが失われないように、リード ターゲットへの接続が最新の状態に保たれていることを確認します。 何かが変更されたときは、必ずこれらの接続を更新してください。
