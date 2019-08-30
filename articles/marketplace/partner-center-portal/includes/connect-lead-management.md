---
title: インクルード ファイル
description: インクルード ファイル
services: commercial marketplace
documentationcenter: partner-center-commercial-marketplace
author: qianw211
manager: evansma
editor: ''
ms.service: marketplace
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/16/2019
ms.author: v-qiwe
ms.custom: include file
ms.openlocfilehash: 43b551639fface699b1c0ec95500de8ea2089b26
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900089"
---
パートナー センターを介してマーケットプレースにオファーを発行する一方で、オファーを顧客関係管理 (CRM) システムに接続する必要があります。これにより、顧客があなたの製品に興味を示した直後、またはそれをデプロイした直後に、顧客の連絡先情報を受け取ることができます。

1. **潜在顧客の送信先となるリードのターゲットを選択します**。 次の CRM システムがサポートされています。

    * [Dynamics 365](../commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    * [Marketo](../commercial-marketplace-lead-management-instructions-marketo.md)
    * [Salesforce](../commercial-marketplace-lead-management-instructions-salesforce.md)

    上記の一覧で CRM システムが明示的にサポートされていない場合は、次のオプションを使用して潜在顧客データを格納し、それらのデータを CRM システムにエクスポートまたはインポートすることができます。

    * [Azure テーブル](../commercial-marketplace-lead-management-instructions-azure-table.md)
    * [Https エンドポイント](../commercial-marketplace-lead-management-instructions-https.md)

2. 上のリンクにあるドキュメントを読み、マーケットプレース オファーからリードを受け取るようにリードのターゲットを設定する方法を確認してください。 
3. パートナー センター内でオファーをマーケットプレースに公開するときに、オファーをリードのターゲットに接続します。 これを行う方法については、上記のリンク先のドキュメントをご覧ください。
4. リードのターゲットへの接続が適切に設定されていることを確認します。 リードのターゲットを適切に構成し、パートナー センター内でオファーに対して [発行] をクリックすると、接続が検証され、テスト リードが送信されます。 公開前にプランを確認したときに、プレビュー環境でプランを自分で入手してみることで、リードの接続をテストすることもできます。 
5. リードが失われないように、リードのターゲットへの接続を最新の状態に保つことが重要です。そのため、パートナー側で変更を加えるたびに、これらの接続を必ず更新してください。
