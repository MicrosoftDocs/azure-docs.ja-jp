---
title: Azure Security Center の脅威インテリジェンス レポート | Microsoft Docs
description: このページでは、調査に Azure Security Center の脅威インテリジェンス レポートを使用し、セキュリティ アラートについての詳しい情報を確認する方法を説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: ec6d227059c3f4fd1285f224e13169a2479bc65f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438235"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Azure Security Center の脅威インテリジェンス レポート

このページでは、セキュリティ アラートがトリガーされる原因となった脅威に関して詳しい情報を確認するにあたり、Azure Security Center の脅威インテリジェンス レポートがどのような点で役に立つかを説明します。


## <a name="what-is-a-threat-intelligence-report"></a>脅威インテリジェンス レポートとは

Security Center の脅威保護機能は、Azure のリソース、ネットワーク、接続されているパートナー ソリューションからのセキュリティ情報を監視することで機能します。 この情報を分析し、ときには複数の情報源から得た情報との関連性を探りながら、脅威を特定します。 詳細は、「[Azure Security Center での脅威の検出と対応方法](security-center-alerts-overview.md#detect-threats)」を参照してください。

Security Center が脅威を発見すると、[セキュリティ アラート](security-center-managing-and-responding-alerts.md) がトリガーされます。このセキュリティ アラートでは、イベントに関して推奨される修復方法などの詳しい情報を確認できます。 インシデント対応チームによる脅威の調査と修復に役立つように、Security Center では、検出された脅威に関する情報を含む脅威インテリジェンス レポートが提供されます。 レポートには次のような情報が含まれます。

* 攻撃者の ID または所属団体 (この情報が利用可能な場合)
* 攻撃者の目的
* 現在および過去の攻撃キャンペーン (この情報が利用可能な場合)
* 攻撃者の戦術、ツール、手順
* URL やファイル ハッシュなど、関連する侵害の兆候 (IoC)
* 被害者情報 (攻撃が発生している業界や地域に関する情報で、Azure リソースにリスクが存在するかどうかの判断に役立ちます)
* 軽減策と修復方法に関する情報

> [!NOTE]
> レポートに記載される情報の量はさまざまであり、どの程度まで詳細な情報が表示されるかは、マルウェアの活動と拡散度に応じて決まります。

Security Center には、攻撃の種類に応じて 3 種類の脅威レポートが用意されています。 具体的には、以下のとおりです。

* **活動グループ レポート**: 攻撃者、その目的、戦術に関する詳しい情報を記載したレポートです。
* **キャンペーン レポート**: 主に特定の攻撃キャンペーンの詳細を取り扱うレポートです。
* **脅威概要レポート**: 前の 2 つのレポートの全項目を確認できるレポートです。

この種の情報は、インシデント対応プロセスにおいて継続的に調査を実施し、攻撃元、攻撃者の動機、この問題の影響を将来軽減するための方策を把握するために利用できます。



## <a name="how-to-access-the-threat-intelligence-report"></a>脅威インテリジェンス レポートにアクセスする方法

1. Security Center のサイドバーから、 **[Security alerts]\(セキュリティ アラート\)** ページを開きます。
1. アラートを選択します。 
    アラートの詳細ページが開き、アラートの詳細が表示されます。 次に示すのは、 **[Ransomware indicators detected]\(検出されたランサムウェア インジケーター\)** アラート詳細ページです。

    [![[Ransomware indicators detected]\(検出されたランサムウェア インジケーター\) アラート詳細ページ](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. レポートへのリンクを選択すると、既定のブラウザーで PDF が開きます。

    [![[Potentially Unsafe Action]\(安全でない可能性があるアクション\) アラート詳細ページ](media/security-center-threat-report/threat-intelligence-report.png)](media/security-center-threat-report/threat-intelligence-report.png#lightbox)

    PDF レポートをダウンロードすることもできます。 

    >[!TIP]
    > 各セキュリティ アラートに関して表示される情報の量は、アラートの種類に応じて変わります。



## <a name="next-steps"></a>次のステップ

このページでは、セキュリティ アラートの調査時に脅威インテリジェンス レポートを開く方法について説明しました。 関連情報については、次のページを参照してください。

* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)。 セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center でのセキュリティ インシデントの処理](security-center-incident.md)