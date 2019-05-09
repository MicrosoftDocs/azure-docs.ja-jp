---
title: 体験版とは | Microsoft Docs
description: Marketplace 体験版機能の説明
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 0bdff77a4be6a28e2799d4f481492d2c7ba6bda0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57457427"
---
<a name="what-is-test-drive"></a>体験版とは
===================

体験版は、\'購入前に試用する\'オプションを与えることで潜在顧客へのオファーを披露し、その結果、会話が増加し、見込みの高いリードが生成される優れた方法です。

顧客は、連絡先を入力すると、構築済みの体験版を使用できるようになります。製品の主な機能とメリットを、実際の実装シナリオを通じて自ら試用することができます。

体験版は、製品を実際に使ってみることができるようにし、そのプロセスで見込みの高いリードを生み出すことができます。

<a name="how-does-a-test-drive-work"></a>体験版のしくみ
---------------------------

潜在顧客は、Marketplace でアプリケーションを見つけ、サインインして、利用規約に同意します。 この時点で顧客は、あらかじめ構成された環境を受け取り、決められた時間数だけ試すことができます。一方、発行元はフォローアップすべき見込みの高いリードを獲得します。

![手順 1。 Marketplace のオファーが表示されます](./media/what-is-test-drive/step1.png)

![手順 2。 Marketplace のオファーのサインイン画面](./media/what-is-test-drive/step1andahalf.png)

![手順 3。 Marketplace オファーの発行元契約画面](./media/what-is-test-drive/step2.png)

![手順 4。 体験版のセットアップ画面](./media/what-is-test-drive/step3.png)

デプロイする時間を必要とする場合にオファーがどのように表示されるかの例を下に示します。

![デプロイされている Marketplace オファー](./media/what-is-test-drive/step4.png)

![Marketplace 体験版の準備完了画面](./media/what-is-test-drive/step5.png)

![Marketplace 体験版の完了画面](./media/what-is-test-drive/step6.png)

アプリケーションがどれだけ複雑であるかにかかわらず、Microsoft 体験版によって顧客は製品を実際に使ってみることができます。 現在、使用している製品、シナリオ、マーケットプレースの種類に基づいて、3 つの異なる種類の体験版を提供しています。

- **[Azure Resource Manager](./azure-resource-manager-test-drive.md)**: Azure Resource Manager 体験版は、発行元によって構築されるソリューションを構成しているすべての Azure リソースが含まれたデプロイ テンプレートです。 このシナリオに適合する製品は、Azure リソースのみを使用する製品です。
- **[ロジック アプリ](./logic-app-test-drive.md)**: ロジック アプリ体験版は、あらゆる複雑なソリューション アーキテクチャに対応するように意図されたデプロイ テンプレートです。 すべての Dynamics アプリケーションやカスタム製品には、この種類の体験版を使用する必要があります。
- **Power BI**: Power BI 体験版は、単なる、カスタム ビルドされたダッシュボードへの埋め込みリンクです。 インタラクティブな Power BI の視覚化のデモンストレーションだけを行う製品には、この種類の体験版を使用する必要があります。
    ここで必要なことは、埋め込み Power BI の URL をアップロードすることだけです。

<a name="what-goes-on-in-the-background"></a>バックグラウンドでの処理
-------------------------------

体験版は、お客様の手作業を一切必要とすることなく、継続的にお客様の顧客をサポートしサービスを提供するように構築されています。 発行元として、Microsoft Coud パートナー ポータルから得られた体験版の設定を管理および構成することであり、その後、その設定は直接顧客が利用できます。

これは、体験版用に構成を設定した後、各体験版は、顧客の要求に応じてオンデマンドでデプロイされるマネージド インスタンスになるからです。 体験版インスタンスが割り当てられると、体験版は、設定された分量の時間を使用でき、その後、別の顧客のための余地を作成するために削除されます。

<a name="next-steps"></a>次の手順
----------

体験版についてすべて理解したので、発行する特定の種類の体験版にアクセスし、必要なフィールドに関するすべてを学習します。

- **[Azure Resource Manager](./azure-resource-manager-test-drive.md)**
- **[ロジック アプリ](./logic-app-test-drive.md)**

さらに質問がある場合やトラブルシューティングに関するアドバイスが必要な場合、あるいは体験版をより成功させたい場合は、「[FAQ, Troubleshooting, & Best Practices (FAQ、トラブルシューティング、およびベスト プラクティス)](./marketing-and-best-practices.md)」をご覧ください。
