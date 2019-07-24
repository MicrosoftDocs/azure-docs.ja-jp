---
title: Azure Security Center でのセキュリティに関する推奨事項の管理 | Microsoft Docs
description: このドキュメントでは、Azure セキュリティ センターでの推奨事項に従ってご使用の Azure のリソースを保護し、セキュリティ ポリシーを使用してコンプライアンスを順守する方法について説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: e3b4da1c1d835e9d630c000055af058aa7b45968
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109190"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Azure セキュリティ センターでのセキュリティに関する推奨事項の管理
このドキュメントでは、Azure セキュリティ センターでの推奨事項を使用して、ご使用の Azure のリソースを保護する方法について説明します。

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。  このドキュメントはステップ バイ ステップ ガイドではありません。
>
>

## <a name="what-are-security-recommendations"></a>セキュリティに関する推奨事項とは
セキュリティ センターは、Azure リソースのセキュリティの状態を定期的に分析します。 セキュリティ センターでは、潜在的なセキュリティの脆弱性が特定されると、推奨事項が作成されます。 推奨事項では、必要なコントロールを構成する手順を説明します。

## <a name="implementing-security-recommendations"></a>セキュリティに関する推奨事項の実装
### <a name="set-recommendations"></a>推奨事項の設定
「 [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md)」では、次のことを学習します。

* セキュリティ ポリシーを構成する。
* データ収集を有効にする。
* セキュリティ ポリシーの一部として表示する推奨事項を選択する。

現在のポリシーの推奨事項は、システムの更新プログラム、基準規則、マルウェア対策プログラム、サブネットとネットワーク インターフェイス上の[ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)、SQL データベースの監査、SQL データベースの透過的なデータ暗号化、Web アプリケーション ファイアウォールを軸として展開しています。  [セキュリティ ポリシーの設定](tutorial-security-policy.md) では、推奨事項の各オプションについて説明します。

### <a name="monitor-recommendations"></a>推奨事項の監視
セキュリティ ポリシーを設定すると、セキュリティ センターではリソースのセキュリティの状態が分析され、潜在的な脆弱性が特定されます。 **[概要]** の **[推奨事項]** タイルでは、Security Center で特定された推奨事項の総数がわかります。

![Recommendations tile][1]

各推奨事項の詳細を表示するには、**[概要]** の **[推奨事項] タイル**を選択します。 **[推奨事項]** が開きます。

![Filter recommendations][2]

推奨事項をフィルター処理できます。 推奨事項をフィルター処理するには、**[推奨事項]** ブレードで **[フィルター]** を選択します。 **[フィルター]** ブレードが開いたら、確認する重要度と状態の値を選択します。


* **[推奨事項]**: 推奨事項。
* **[セキュリティ スコアの影響]**: 
* **リソース**: この推奨事項が適用されるリソースの一覧を表示します。
* **ステータス バー**: 特定の推奨事項の重要度を示します。
   * **高 (赤)**: 重要なリソース (アプリケーション、VM、ネットワーク セキュリティ グループなど) に脆弱性が存在しており、注意が必要です。
   * **中 (オレンジ)**: 脆弱性が存在しており、脆弱性を排除するかプロセスを完了するには重大ではない手順または追加の手順が必要です。
   * **低 (青)**: 対処する必要はあるが、早急な注意を必要としない脆弱性が存在します  (既定では、重要度の低い推奨事項は表示されないが、重要度の低い推奨事項にフィルターを適用すると表示できる)。 
   * **正常 (緑)**: 
   *  **(灰色)**: 
 


> [!NOTE]
> Azure リソースの [クラシック デプロイ モデルと Resource Manager デプロイ モデル](../azure-classic-rm.md) について理解しておく必要があります。
> 
> 
> ### <a name="apply-recommendations"></a>推奨事項の適用
> すべての推奨事項を確認したら、最初に適用する推奨事項を決めます。 重要度の評価をメイン パラメーターとして使用して、最初にどの推奨事項を適用する必要があるか評価することをお勧めします。



## <a name="next-steps"></a>次の手順
このドキュメントでは、セキュリティ センターのセキュリティに関する推奨事項について説明しました。 セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」 -- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」 -- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」 -- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
