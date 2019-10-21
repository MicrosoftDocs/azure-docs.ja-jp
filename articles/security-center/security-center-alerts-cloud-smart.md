---
title: Azure Security Center でのクラウド スマート アラート関連付け (インシデント) | Microsoft Docs
description: このトピックでは、Azure Security Center でセキュリティ インシデントを生成するために Fusion でクラウド スマート アラート関連付けがどのように使われるかについて説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 4c2f084fe03271b29a12aa6906f5e36612d50674
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202695"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Azure Security Center でのクラウド スマート アラート関連付け (インシデント)

Azure Security Center では、高度な分析および脅威インテリジェンスを使用してハイブリッド クラウド ワークロードを継続的に分析し、悪意のあるアクティビティに関するアラートを受け取ることができます。

脅威の範囲が拡大しています。 ほんのわずかな侵害の兆候でも検出する必要性が重要であり、セキュリティ アナリストがさまざまなアラートをトリアージし、実際の攻撃を識別することは困難な場合があります。 Security Center は、アナリストがこのアラート疲れに対処するのに役立ちます。 さまざまなアラートと忠実度が低いシグナルがセキュリティ インシデントに関連付けられ、発生した攻撃を診断するのに役立ちます。

Fusion の分析は、Security Center のインシデントに力を与え、さまざまなアラートと状況に依存したシグナルを関連付けられるようにする、テクノロジと分析のバック エンドです。 Fusion では、サブスクリプションのリソースで報告されたさまざまなシグナルが調査されます。 Fusion では、攻撃が進行していることを示すパターン、または統一された対応手順を実施する必要があることを示す共有コンテキスト情報を含むシグナルが検出されます。

Fusion の分析では、セキュリティ ドメインに関する知識と AI の組み合わせでアラートが分析され、新しい攻撃パターンの発生が発見されます。 

Security Center では、MITRE 攻撃マトリックスを利用してアラートとそれらの認識された意図が関連付けられるので、セキュリティ ドメインに関する知識を整理するのに役立ちます。 さらに、攻撃の各ステップで収集された情報を使うことにより、Security Center では攻撃のステップのように見えても実際にはそうではないアクティビティを除外できます。  

攻撃は異なるテナントにまたがって発生することが多いので、Security Center では、AI アルゴリズムを組み合わせて、各サブスクリプションで報告された攻撃のシーケンスを分析できます。 この手法により、単にインシデントで相互に関連付けるのではなく、よくあるアラート パターンとして、攻撃のシーケンスが識別されます。

アナリストがインシデントの調査を行い、脅威の性質とそれを軽減する方法を判断するためには、追加のコンテキストが必要になることがよくあります。 たとえば、ネットワークの異常が検出されても、ネットワーク上で他に起きていたことや、対象のリソースに関して理解していないと、次に行うべきアクションを把握することは困難です。 そのため、セキュリティ インシデントには、成果物、関連するイベント、情報を含めることができます。 セキュリティ インシデントに利用できる追加情報は、検出された脅威の種類や環境内の構成によって異なります。 

![セキュリティ インシデント検出レポートのスクリーンショット](./media/security-center-alerts-cloud-smart/security-incident.png)

セキュリティ インシデントについての理解を深めるには、[Azure Security Center でのセキュリティ インシデントの処理方法](https://docs.microsoft.com/azure/security-center/security-center-incident)に関する記事をご覧ください。

