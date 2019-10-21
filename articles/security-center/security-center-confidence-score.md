---
title: Azure Security Center の信頼度スコア | Microsoft Docs
description: " Azure Security Center の信頼度スコアを操作する方法について説明します。 "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: memildin
ms.openlocfilehash: 0f0380ca4a285d9cb4a8472a40c77130a8f63035
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202384"
---
# <a name="alert-confidence-score"></a>アラート信頼度スコア 

Azure Security Center は、Azure で実行するリソースに対する可視性を提供して、潜在的な問題を検出するとその旨を通知します。 アラートが大量になるとセキュリティ運用チームが個別に対応するのが困難になることがあるため、調査するアラートに優先度付けすることが必要になります。 アラートの調査は複雑で時間がかかることがあり、その結果、一部のアラートは無視されます。

Security Center の信頼度スコアは、チームがアラートをトリアージして優先度付けするのに役立ちます。 Security Center は、業界のベスト プラクティス、インテリジェント アルゴリズム、およびアナリストが使用するプロセスを自動的に適用して、脅威が本物かどうかを判断し、信頼度スコアの形で有意な分析情報を提供します。

## <a name="how-the-confidence-score-is-triggered"></a>信頼度スコアがトリガーされるしくみ

アラートは、仮想マシン上で疑わしいプロセスの実行が検出されたときに生成されます。 Security Center は、Azure で実行されている Windows 仮想マシンでこれらのアラートを確認および分析します。 Security Center は、組織全体の複数のエンティティとデータ ソース、およびすべての Azure リソースに対して高度なアルゴリズムを使用して自動チェックと関連付けを実行し、信頼度スコアを提示します。信頼度スコアは、アラートが本物であり、調査が必要だとする Security Center による判定の信頼性に関する尺度です。

## <a name="understanding-the-confidence-score"></a>信頼度スコアを理解する

信頼度スコアは 1 から 100 までの数字であり、アラートを調査する必要性を示す Security Center による判定の信頼度を表します。 スコアが高いほど、アラートが本物の悪意のあるアクティビティを示しているとする Security Center による判定の信頼度が高くなります。 信頼度スコアには、アラートにその信頼度スコアが与えられた主な理由が含まれています。 信頼度スコアを使用すると、セキュリティ アナリストは、アラートへの対応に容易に優先順位を付け、最も切迫している攻撃に最初に対応でき、最終的に攻撃やセキュリティ侵害に対応する時間を短縮することができます。

信頼度スコアを表示するには:
- Security Center ポータルで、[セキュリティ通知] ブレードを開きます。
-  アラートとインシデントは値が大きいものから小さいものの順に表示されます。つまり、脅威を示しているとする Security Center による判定の信頼度が高いアラートほど、ページ内で上に表示されます。 


 ![信頼度スコア][1]

アラートに対する Security Center の信頼度に貢献したデータを表示するには:
- [セキュリティ通知] ブレードの **[信頼度]** で、信頼度スコアに貢献した情報を表示して、アラートに関連する分析情報を取得します。 ここには、アラートの原因となったアクティビティの性質に関するより多くの分析情報が表示されます。

  ![疑わしい信頼度スコア][2]

Security Center の信頼度スコアを使用して、環境内のアラート トリアージの優先度付けを行います。 信頼度スコアは、業界のベスト プラクティスとインテリジェント アルゴリズムを適用して自動的にアラートを調査し、仮想アナリストとして機能して実際の脅威と注目すべき対象を判定することで、ユーザーの時間と労力を節約します。


## <a name="next-steps"></a>次の手順
この記事では、信頼度スコアを使用してアラート調査の優先度付けを行う方法について説明しました。 セキュリティ センターの詳細については、次を参照してください。

* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
* 「[Azure セキュリティ センターでのセキュリティ ヘルスの監視](security-center-monitoring.md)」-- Azure リソースの正常性を監視する方法について説明しています。



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
