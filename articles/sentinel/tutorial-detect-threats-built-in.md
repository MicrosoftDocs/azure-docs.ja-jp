---
title: Azure Sentinel でアラートを調査する | Microsoft Docs
description: すぐに使用できる組み込み Azure 脅威検出テンプレートについて説明します。これを使用すると、何か疑わしいことが発生したときに通知が届きます。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: 5d73337c25c812363b7a542bf42372ca3baa10e8
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605416"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>チュートリアル:難しい設定なしで脅威を検出する


> [!IMPORTANT]
> 難しい設定の要らない脅威検出機能が現在、パブリック プレビューで提供されています。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Sentinel に[データ ソースを接続](quickstart-onboard.md) したら、不審な事態が起きたときに通知を受けるようにすることが推奨されます。 そのため Azure Sentinel には、脅威検出規則の作成に役立つ、すぐに使用できる組み込みのテンプレートが用意されています。 このテンプレートは、セキュリティ専門家とアナリストから構成される Microsoft のチームが既知の脅威、一般的な攻撃ベクトル、疑わしい行動の段階的拡大チェーンに基づいて設計したものです。 これらのテンプレートから作成される規則により、環境全体にわたって、疑わしい活動がないか自動的に検査されます。 テンプレートの多くは、ニーズに合わせて特定の活動を検索したり、除外したりするようにカスタマイズできます。 これらの規則で生成されるアラートによって、環境内で割り当てて調査することができるインシデントが作成されます。

このチュートリアルは、Azure Sentinel で脅威を検出するためのものです。

> [!div class="checklist"]
> * 難しい設定の要らない脅威検出機能を使用する
> * 脅威への対応を自動化する

## <a name="about-out-of-the-box-detections"></a>難しい設定の要らない検出機能について

難しい設定の要らない検出機能をすべて表示するには、 **[Analytics]** 、 **[Rule templates]\(ルール テンプレート\)** の順に選択します。 このタブには、すべての Azure Sentinel 組み込み規則が含まれています。

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="Azure Sentinel の組み込みの検出機能を利用して脅威を検出する":::

次の種類のテンプレートを使用できます。

- **Microsoft セキュリティ**
   
   Microsoft セキュリティ テンプレートでは、他の Microsoft セキュリティ ソリューションで生成されたアラートから自動的に、Azure Sentinel インシデントがリアルタイムに作成されます。 同様のロジックで新しい規則を作成するためのテンプレートとして Microsoft セキュリティ規則を利用できます。 セキュリティ規則の詳細については、「[Microsoft セキュリティ アラートからインシデントを自動的に作成する](create-incidents-from-alerts.md)」を参照してください。

- **Fusion** 

    Fusion テクノロジを基盤とする Azure Sentinel の高度なマルチステージ攻撃検出では、複数の製品にわたって多数の忠実度が低いアラートとイベントを関連付けて、忠実度が高くアクションにつながるインシデントを生成できるスケーラブルな機械学習アルゴリズムが使用されています。 Fusion は既定で有効になっています。 ロジックは非表示であるため、カスタマイズすることはできません。このテンプレートを使用して作成できる規則は 1 つだけです。

- **機械学習による行動分析**

    これらのテンプレートは、Microsoft 独自の機械学習アルゴリズムを基盤としているため、動作方法や実行のタイミングから構成される内部ロジックを参照することはできません。 ロジックは非表示であるため、カスタマイズすることはできません。この種類の各テンプレートを使用して作成できる規則は 1 つだけです。

- **スケジュール**

    スケジュール付き分析ルールは、Microsoft のセキュリティ専門家が記述した組み込みのクエリに基づいています。 ユーザーはクエリ ロジックを閲覧し、変更できます。 スケジュール付き規則のテンプレートを使用してクエリ ロジックやスケジュール設定をカスタマイズし、新しい規則を作成できます。

## <a name="use-out-of-the-box-detections"></a>難しい設定の要らない検出機能を使用する

1. 組み込みのテンプレートを使用するには、テンプレート名をクリックしてから、詳細ウィンドウの **[規則の作成]** ボタンをクリックし、そのテンプレートに基づく新しいアクティブな規則を作成します。 各テンプレートには、必要なデータ ソースの一覧があります。 テンプレートを開くと、データ ソースの可用性が自動的にチェックされます。 可用性の問題がある場合は、 **[規則の作成]** ボタンが無効にされていたり、その影響に対する警告が表示されたりすることがあります。
  
    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="検出規則のプレビュー パネル":::
 
1. **[規則の作成]** ボタンをクリックすると、選択したテンプレートに基づいて規則の作成ウィザードが開かれます。 すべての詳細が自動的に入力されます。**スケジュール付き**テンプレートや **Microsoft セキュリティ** テンプレートを使用する場合は、特定のニーズに合わせてロジックやその他の規則の設定をカスタマイズできます。 この手順を繰り返して、組み込みテンプレートに基づく追加の規則を作成できます。 最後まで規則の作成ウィザードの手順に従うと、テンプレートに基づく規則の作成が完了します。 **[アクティブな規則]** タブに新しい規則が表示されます。

    規則の作成ウィザードで規則をカスタマイズする方法の詳細については、「[チュートリアル:脅威を検出するためのカスタム分析規則を作成する](tutorial-detect-threats-custom.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Sentinel を使用して、脅威の検出を開始する方法について説明しました。 

脅威への対応を自動化する方法については、「[Azure Sentinel で脅威への自動対応を設定する](tutorial-respond-threats-playbook.md)」を参照してください。

