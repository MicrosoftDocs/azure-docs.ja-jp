---
title: Azure Security Center でのクラウド スマート アラート関連付け (インシデント) | Microsoft Docs
description: このトピックでは、Azure Security Center でセキュリティ インシデントを生成するために Fusion でクラウド スマート アラート関連付けがどのように使われるかについて説明します。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 7ba2cf14d9fac100f44a1ef23997b27ba062bee0
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295860"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Azure Security Center でのクラウド スマート アラート関連付け (インシデント)

Security Center では、高度な分析および脅威インテリジェンスを使用してハイブリッド クラウド ワークロードを継続的に分析し、悪意のあるアクティビティに関するアラートを受け取ることができます。

脅威の範囲は広くなり、ほんのわずかな侵害の兆候でも検出する必要性が増しているため、セキュリティ アナリストがさまざまなアラートをトリアージし、実際の攻撃を識別することは困難な場合があります。 Security Center では、さまざまなアラートと忠実度が低いシグナルがセキュリティ インシデントに関連付けられるため、アナリストがアラート疲れに対処して発生した攻撃を診断するのに役立ちます。

Fusion は、Security Center のインシデントに力を与え、さまざまなアラートと状況に依存したシグナルを関連付けられるようにする、テクノロジと分析のバックエンドです。 Fusion では、サブスクリプションに含まれるすべてのリソースで報告されたさまざまなシグナルが調査されて、攻撃が進行していることを示す一般的なパターン、または統一された対応手順を実施する必要があることを示す共有コンテキスト情報を含むシグナルが検出されます。

Fusion の分析では、セキュリティ ドメインに関する知識と AI の組み合わせでアラートが分析され、新しい攻撃パターンの発生が発見されます。 

Security Center では、MITRE 攻撃マトリックスを利用してアラートとそれらの認識された意図が関連付けられるので、セキュリティ ドメインに関する知識を整理するのに役立ちます。 さらに、攻撃の各ステップで収集された情報を使うことにより、Security Center では攻撃のステップのように見えてもそうではないアクティビティを除外できます。  

攻撃は異なるテナントにまたがって発生することが多いので、Security Center では、AI アルゴリズムを組み合わせて、各サブスクリプションで報告された攻撃のシーケンスを分析し、単にインシデントで相互に関連付けるのではなく、よくあるアラート パターンとしてそれらを識別できます。

アナリストがインシデントの調査を行い、脅威の性質とそれを軽減する方法を判断するためには、追加のコンテキストが必要になることがよくあります。 たとえば、ネットワークの異常が検出されても、ネットワーク上で他に起きていたことや、対象のリソースに関して理解していないと、次に行うべきアクションを把握することは困難です。 そのため、セキュリティ インシデントには、成果物、関連するイベント、情報を含めることができます。 セキュリティ インシデントに利用できる追加情報は、検出された脅威の種類や環境内の構成によって異なります。 

![セキュリティ インシデントの詳細](./media/security-center-alerts-cloud-smart/security-incident.png)

セキュリティ インシデントについての理解を深めるには、「[Azure Security Center でのセキュリティ インシデントの処理](https://docs.microsoft.com/azure/security-center/security-center-incident)」をご覧ください。

