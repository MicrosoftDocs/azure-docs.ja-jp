---
title: Azure Security Center の脅威インテリジェンス レポート | Microsoft Docs
description: このドキュメントでは、調査に Azure Security Center の脅威インテリジェンス レポートを使用し、セキュリティ アラートに関する詳しい情報を確認する方法を説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: ba5ab7ce85933545a41f23e2ecd913acbb7e72d1
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112735"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Azure Security Center の脅威インテリジェンス レポート
このドキュメントでは、セキュリティ アラートが生成される原因となった脅威に関して詳しい情報を確認するにあたり、Azure Security Center の脅威インテリジェンス レポートがどのような点で役に立つかを説明します。

## <a name="what-is-a-threat-intelligence-report"></a>脅威インテリジェンス レポートとは
Security Center の脅威検出機能は、Azure のリソースやネットワーク、接続されているパートナー ソリューションから収集したセキュリティ情報を監視するという仕組みになています。 この情報を分析し、ときには複数の情報源から得た情報との関連性を探りながら、脅威を特定します。 このプロセスは、Security Center の[検出機能](security-center-detection-capabilities.md)の一環として提供されているものです。

Security Center が脅威を発見すると、[セキュリティ アラート](security-center-managing-and-responding-alerts.md) がトリガーされます。このセキュリティ アラートでは、特定のイベントに関して推奨される修復方法などの詳しい情報を確認できます。 インシデント対応を担当するチームが脅威の調査とその修復を実施する際に役立つように、Security Center には脅威インテリジェンス レポートという機能が用意されており、検出された脅威に関して以下のような情報を確認できるようになっています。

* 攻撃者の ID または所属団体 (この情報が利用可能な場合)
* 攻撃者の目的
* 現在および過去の攻撃キャンペーン (この情報が利用可能な場合)
* 攻撃者の戦術、ツール、手順
* URL やファイル ハッシュなど、関連する侵害の兆候 (IoC)
* 被害者情報 (攻撃が発生している業界や地域に関する情報で、Azure リソースにリスクが存在するかどうかの判断に役立ちます)
* 軽減策と修復方法に関する情報

> [!NOTE]
> レポートに記載される情報の量はさまざまであり、どの程度まで詳細な情報が表示されるかは、マルウェアの活動と拡散度に応じて決まります。
>
>

Security Center には、攻撃の種類に応じて 3 種類の脅威レポートが用意されています。 具体的には、以下のとおりです。

* **活動グループ レポート**: 攻撃者、その目的、および戦術に関する詳しい情報を記載したレポートです。
* **キャンペーン レポート**: 主に特定の攻撃キャンペーンの詳細を取り扱うレポートです。
* **脅威概要レポート**: 前の 2 つのレポートの全項目を確認できるレポートです。

この種の情報は、[インシデント対応](security-center-incident-response.md)プロセスにおいて継続的に調査を実施し、攻撃元、攻撃者の動機、この問題の影響を今後軽減するための方策を把握するために利用できます。

## <a name="how-to-access-the-threat-intelligence-report"></a>脅威インテリジェンス レポートにアクセスする方法
現在の警告は、 **[セキュリティ アラート]** タイルで確認できます。 各警告の詳細を確認するには、Azure portal を開いて以下の手順に従ってください。

1. [セキュリティ センター] ダッシュボードには **[セキュリティの警告]** タイルが表示されます。
2. タイルをクリックして、**[セキュリティ アラート]** ブレードを開くと、アラートに関する詳細が表示されます。そこで、さらに詳しい情報を確認したいセキュリティ アラートをクリックします。

    ![セキュリティのアラート](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. この場合、**[Suspicious process executed (不審なプロセスの実行)]** ブレードが表示され、アラートに関して以下の図に示すような詳細が表示されます。

    ![Security alert details](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. 各セキュリティ アラートに関して表示される情報の量は、アラートの種類に応じて変わります。 **[レポート]** フィールドには、脅威インテリジェンス レポートへのリンクがあります。 リンクをクリックしてください。PDF ファイルが別のブラウザー ウィンドウで表示されます。

   ![Storage の選択](./media/security-center-threat-report/security-center-threat-report-fig3.png)

ここでは、このレポートの PDF のダウンロードのほか、検出されたセキュリティ面の問題の詳細を確認したり、入手した情報を基に各種の措置を講じたりできます。

## <a name="see-also"></a>関連項目
このドキュメントでは、セキュリティ アラートに関する調査にあたり Azure Security Center の脅威のインテリジェント レポートがどのような点で有用であるかを学習しました。 Azure セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)。 このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure Security Center を活用したインシデント対応](security-center-incident-response.md)
* [Azure Security Center の検出機能](security-center-detection-capabilities.md)
* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)。 Azure Security Center を導入するための設計上の考慮事項を計画し、理解する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)。 セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center でのセキュリティ インシデントの処理](security-center-incident.md)
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
