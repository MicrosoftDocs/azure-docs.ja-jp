---
title: "Security Center の価格 | Microsoft Docs"
description: "この記事では、Azure Security Center の価格について説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 9c20ace9112c62e733b7b679e3f0be717144f3c2
ms.openlocfilehash: 933a485e36b3cf0f514f28fc069d49161605af3a
ms.lasthandoff: 02/23/2017


---
# <a name="azure-security-center-pricing"></a>Azure Security Center の価格
Azure セキュリティ センターは、Azure リソースのセキュリティを高度に視覚化し、制御することで脅威を回避、検出し、それに対応することに役立ちます。 これにより、Azure サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

## <a name="pricing-tiers"></a>価格レベル
Azure Security Center は&2; つのレベルで提供されます。

* **Free レベル**は、すべての Azure サブスクリプションで自動的に有効になります。 無料レベルでは、Azure リソースのセキュリティ状態の表示、基本的なセキュリティ ポリシー、セキュリティに関する推奨事項、およびパートナーによるセキュリティ製品とサービスとの統合が提供されます。
* **Standard レベル** には、脅威インテリジェンス、行動分析、異常検出、セキュリティ インシデント、および脅威評価レポートなどの高度な脅威検出機能が追加されます。 Standard レベルは、最初の 60 日間は無料です。

詳細については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」を参照してください。

> [!NOTE]
> Security Center では、Azure Storage を使用して、保護対象のノードから生成したセキュリティ データを保存します。 この記憶域に関連するコストは、サービスの料金には含まれません。通常の [Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/blobs/)が別途適用されます。 ストレージ料金は、60 日間の無料試用期間中にも適用されます。
>
>

## <a name="try-standard-free-for-60-days"></a>Standard レベルのサービスを 60 日間無料で試用する
Standard レベルは、最初の 60 日間は無料です。 60 日経過した時点で、サービスの利用を継続することを選択した場合は、使用量に応じて自動的に課金が開始されます。

Standard レベルを取得するには

1. **[Security Center]** ブレードの **[ポリシー]** タイルを選択します。
2. Standard レベルにアップグレードするサブスクリプションを選択します。
3. **[Security policy]** ブレードで、**[価格レベル]** を選択します。
4. **[価格レベルの選択]** ブレードで、**[Standard]** を選択します。
5. **[選択]**をクリックします。

![Standard レベル][1]

## <a name="why-upgrade-to-standard"></a>Standard レベルにアップグレードする理由
Standard レベルの Security Center は、Free レベルのすべての機能に加え、高度な脅威検出機能を提供します。 高度な脅威検出機能によって、Azure リソースを対象とするアクティブな脅威を識別し、迅速に対応するために必要な洞察を得ることができます。

Security Center には、シグネチャ ベースの手法とは比較にならない高度なセキュリティ分析が採用されています。 ビッグ データや機械学習における革新的テクノロジを使用して、クラウド ファブリック全体にわたってイベントが評価されるので、手作業に頼った手法や攻撃の進化を予測する手法では特定できない脅威でも検出することができます。

Standard レベルには次のセキュリティ分析が付属しています。

* **脅威インテリジェンス**: グローバルな脅威インテリジェンスを使用して、既知の有害因子を探します。情報源としては、Microsoft 製品とサービス、Microsoft Digital Crimes Unit、Microsoft Security Response Center、および外部フィードがあります。
* **行動分析**: 既知のパターンを適用することによって悪質な行動を検出します。
* **異常検出**: 統計プロファイルを使用して、過去の基準を構築します。 確立された基準からの逸脱に、攻撃ベクトルとの一致が疑われる場合、警告が生成されます。

次の **[セキュリティ アラート]** ブレードでは、Security Center によって&1; 件のセキュリティ **インシデント**が検出されています。 セキュリティ インシデントは、キル チェーンのパターンと一致するリソースに関するすべての警告を集約したものです。 セキュリティ インシデントを選択すると、インシデントの詳細が表示され、関連するアラートが一覧表示されます。 アラートを選択すると、発生の詳細が表示されます。

![セキュリティ インシデント][2]

次の**ネットワーク通信**アラートは、このアラートの詳細を示しています。 詳細には、詳しい説明、重大度、現在の状態 (この例では [破棄済み] であり、ユーザーが対処済みであることを意味します)、攻撃されたリソース、および修復手順が含まれます。 Microsoft 脅威インテリジェンス レポートへのリンク一覧もあります。 これらのレポートは、セキュリティの修復や防御を目的として使用できます。

![セキュリティ アラートの詳細][3]

## <a name="enable-data-collection"></a>データ収集を有効にする
仮想マシンの動作分析を有効にするには、データ収集をオンにする必要があります。

データ収集が有効になっていることを検証するには

1. **[ポリシー]** タイルを選択します。 **[セキュリティ ポリシー]** ブレードが開き、Azure サブスクリプションが一覧表示されます。
2. サブスクリプションを選択します。
3. **[データ収集]** がオフになっている場合はオンに変更し、変更を保存します。

詳細については、「[Azure Security Center のデータ収集を有効にする](security-center-enable-data-collection.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
* このドキュメントでは、Security Center の価格について紹介しました。 価格の詳細情報については、「[Security Center の価格 ](https://azure.microsoft.com/pricing/details/security-center/)」を参照してください。
* Security Center の高度な検出機能の詳細については、「[Azure Security Center の検出機能](security-center-detection-capabilities.md)」を参照してください。
* Security Center に関する質問がある場合は、[Azure Security Center の FAQ](security-center-faq.md) をご覧ください。
* Security Center の使用に関してご不明な点がある場合は、[Azure のフォーラム](https://social.msdn.microsoft.com/Forums/home?forum=AzureSecurityCenter&filter=alltypes&sort=lastpostdesc)をご覧ください。

<!--Image references-->
[1]: ./media/security-center-pricing/standard.png
[2]: ./media/security-center-pricing/incident.png
[3]: ./media/security-center-pricing/network-alert.png

