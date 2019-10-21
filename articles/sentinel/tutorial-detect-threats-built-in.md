---
title: Azure Sentinel でアラートを調査する | Microsoft Docs
description: このチュートリアルでは、Azure Sentinel でアラートを調査する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6d61b94a50716966ac0b3c75551ddd9d8e291965
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024463"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>チュートリアル:難しい設定なしで脅威を検出する


> [!IMPORTANT]
> 難しい設定の要らない脅威検出機能が現在、パブリック プレビューで提供されています。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Sentinel に [データ ソースを接続した](quickstart-onboard.md) 後で、不審な事態が起きたときに通知を受けるようにします。 それを可能にする目的で、Azure Sentinel には難しい設定の要らないテンプレートが組み込まれています。 このテンプレートは、セキュリティ専門家とアナリストから構成される Microsoft のチームが既知の脅威、一般的な攻撃ベクトル、疑わしい行動の段階的拡大チェーンに基づいて設計したものです。 テンプレートを有効にすると、疑わしい行動がないか、ご利用の環境全体が自動的に検査されます。 テンプレートの多くは、ニーズに合わせて特定の活動を検索または除外するようにカスタマイズできます。 このようなテンプレートで生成されるアラートにより作成されるインシデントは、ご利用の環境で指定したり、調査したりできます。

このチュートリアルは、Azure Sentinel で脅威を検出するためのものです。

> [!div class="checklist"]
> * 難しい設定の要らない検出機能を使用する
> * 脅威への対応を自動化する

## <a name="about-out-of-the-box-detections"></a>難しい設定の要らない検出機能について

難しい設定の要らない検出機能をすべて表示するには、 **[Analytics]** 、 **[Rule templates]\(ルール テンプレート\)** の順に選択します。 このタブには、すべての Azure Sentinel 組み込み規則が含まれています。

   ![Azure Sentinel の組み込み検出機能を利用して脅威を検出する](media/tutorial-detect-built-in/view-oob-detections.png)

次の種類のテンプレートを使用できます。

- **Microsoft セキュリティ** - Microsoft セキュリティ テンプレートでは、他の Microsoft セキュリティ ソリューションで生成されたアラートから Azure Sentinel インシデントが自動的にリアルタイムで作成されます。 同様のロジックで新しい規則を作成するためのテンプレートとして Microsoft セキュリティ規則を利用できます。 セキュリティ規則の詳細については、「[Microsoft セキュリティ アラートからインシデントを自動的に作成する](create-incidents-from-alerts.md)」を参照してください。
- **Fusion** - Fusion テクノロジを基盤とする Azure Sentinel の高度なマルチステージ攻撃の検出では、複数の製品にわたり、忠実度の低いアラートとイベントをたくさん関連付け、忠実度が高く、すぐに対策を講じることができるインシデントを生成できるスケーラブルな機械学習アルゴリズムが使用されます。 Fusion は既定で有効になっています。 ロジックは表示されないため、複数の規則を作成するためのテンプレートとしてこれを利用することはできません。
- **機械学習による行動分析** - このテンプレートは Microsoft が特許を所有する機械学習アルゴリズムを基盤としており、しくみや実行タイミングからなる内部ロジックをユーザーが見ることはできません。 ロジックは表示されないため、複数の規則を作成するためのテンプレートとしてこれを利用することはできません。
-   **スケジュール付き** - スケジュール付き分析ルールは Microsoft のセキュリティ専門家が記述したスケジュールのあるクエリです。 ユーザーはクエリ ロジックを閲覧し、変更できます。 同様のロジックで新しい規則を作成するためのテンプレートとしてスケジュール付きルールを利用できます。

## <a name="use-out-of-the-box-detections"></a>難しい設定の要らない検出機能を使用する

1. 組み込みテンプレートを使用するには、 **[ルールの作成]** をクリックし、そのテンプレートを基礎として新しい有効なルールを作成します。 エントリごとに与えられる必須のデータ ソースの一覧は自動的にチェックされます。その結果、 **[ルールの作成]** が無効になる場合があります。
  
   ![Azure Sentinel の組み込み検出機能を利用して脅威を検出する](media/tutorial-detect-built-in/use-built-in-template.png)
 
1. 選択したテンプレートに基づいてルール作成ウィザードが開きます。 項目にはすべて、データが自動入力されます。 **[Scheduled rules]\(スケジュール付きルール\)** と **[Microsoft セキュリティ規則]** については、組織に合わせてロジックをカスタマイズしたり、組み込みテンプレートに基づいて追加ルールを作成したりできます。 ルール作成ウィザードの手順を行い、テンプレートに基づくルールの作成を完了すると、 **[Active rules]\(有効なルール\)** タブに新しいルールが表示されます。

ウィザードのフィールドの詳細については、「[チュートリアル:疑わしい脅威を検出するカスタム分析ルールを作成する](tutorial-detect-threats-custom.md)」を参照してください。



## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure Sentinel を使用して、脅威の検出を開始する方法について説明しました。 

脅威への対応を自動化する方法については、「[Azure Sentinel で脅威への自動対応を設定する](tutorial-respond-threats-playbook.md)」を参照してください。

