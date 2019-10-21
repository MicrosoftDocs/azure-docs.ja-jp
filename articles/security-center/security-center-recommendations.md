---
title: Azure Security Center でのセキュリティに関する推奨事項 | Microsoft Docs
description: このドキュメントでは、Azure セキュリティ センターでの推奨事項に従ってご使用の Azure のリソースを保護し、セキュリティ ポリシーを使用してコンプライアンスを順守する方法について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 32b7f1d699c0d620d70614c441a8c18520c1b2d5
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201049"
---
# <a name="security-recommendations-in-azure-security-center"></a>Security recommendations in Azure Security Center 
このトピックでは、Azure セキュリティ センターで推奨事項を確認し、理解し、Azure リソースの保護に役立てる方法について説明します。

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。  このドキュメントはステップ バイ ステップ ガイドではありません。
>

## <a name="what-are-security-recommendations"></a>セキュリティに関する推奨事項とは

推奨事項とは、リソースを保護するために実行する操作です。

Security Center は Azure リソースのセキュリティの状態を定期的に分析して、潜在的なセキュリティ脆弱性を特定します。 その後、それらを削除する方法の推奨事項を提供します。

それぞれの推奨事項の内容は次のとおりです。

- 推奨されている内容の簡単な説明。
- 推奨事項を実装するために実行する修復手順。 <!-- In some cases, one-click remediation is available. -->
- 推奨される操作を実行するために必要なリソース。
- **セキュリティ スコアの影響**。これは、この推奨事項を実装した場合に、セキュリティ スコアが上昇する量です。

## 推奨事項の監視<a name="monitor-recommendations"></a>

Security Center はリソースのセキュリティの状態を分析して、潜在的な脆弱性を特定します。 **[概要]** の **[推奨事項]** タイルには、Security Center で特定された推奨事項の総数が表示されます。

![Security Center の概要](./media/security-center-recommendations/asc-overview.png)

1. **[概要]** で **[推奨事項]** タイルを選択します。 **[推奨事項]** リストが開きます。

      ![推奨事項の表示](./media/security-center-recommendations/view-recommendations.png)

    推奨事項をフィルター処理できます。 推奨事項をフィルター処理するには、 **[推奨事項]** ブレードで **[フィルター]** を選択します。 **[フィルター]** ブレードが開いたら、確認する重要度と状態の値を選択します。

   * **[推奨事項]** : 推奨事項。
   * **[セキュリティ スコアの影響]** : セキュリティに関する推奨事項を使用し、各推奨事項の重要度を判断する高度なアルゴリズムを適用することで Security Center で生成されるスコア。 詳細については、「[セキュリティ スコアの計算](security-center-secure-score.md#secure-score-calculation)」をご覧ください。
   * **リソース**: この推奨事項が適用されるリソースの一覧を表示します。
   * **ステータス バー**: 特定の推奨事項の重要度を示します。
       * **高 (赤)** : 重要なリソース (アプリケーション、VM、ネットワーク セキュリティ グループなど) に脆弱性が存在しており、注意が必要です。
       * **中 (オレンジ)** : 脆弱性が存在しており、脆弱性を排除するかプロセスを完了するには重大ではない手順または追加の手順が必要です。
       * **低 (青)** : 対処する必要はあるが、早急な注意を必要としない脆弱性が存在します (既定では、重要度の低い推奨事項は表示されないが、重要度の低い推奨事項にフィルターを適用すると表示できる)。 
       * **正常 (緑)** :
       * **(灰色)** :

1. 各推奨事項の詳細を表示するには、推奨事項をクリックします。

    ![推奨事項の詳細](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Azure リソースの詳細については、[クラシック デプロイ モデルと Resource Manager デプロイ モデル](../azure-classic-rm.md)に関するページを参照してください。
 
## <a name="next-steps"></a>次の手順

このドキュメントでは、セキュリティ センターのセキュリティに関する推奨事項について説明しました。 推奨事項を修正する方法については、次を参照してください。

* [推奨事項の修正](security-center-remediate-recommendations.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明します。
