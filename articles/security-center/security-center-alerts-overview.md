---
title: Azure Security Center のセキュリティ アラート | Microsoft Docs
description: このトピックでは、セキュリティ アラートとは何かと、Azure Security Center で利用できるさまざまな種類について説明します。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 91dd397095718b3b43e41767af422801fd50b7f1
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295695"
---
# <a name="security-alerts-in-azure-security-center"></a>Azure Security Center のセキュリティ アラート

この記事では、Azure Security Center (ASC) で利用できるさまざまな種類のセキュリティ アラートについて説明します。 さまざまなリソースの種類に対する各種のアラートがあります。 ASC は、Azure にデプロイされたリソースと、オンプレミス環境およびハイブリッド クラウド環境にデプロイされたリソースの両方に対してアラートを生成します。 

## <a name="what-are-security-alerts"></a>セキュリティの警告とは何か

アラートは、Security Center がリソース上の脅威を検出したときに生成する通知です。 アラートに優先順位を付け、問題の迅速な調査に必要な情報と共に一覧表示します。 Security Center は、攻撃をどのように修復できるかに関する推奨事項も提供します。

## <a name="how-does-security-center-detect-threats"></a>Security Center での脅威の検出方法

Security Center は、真の脅威を検出し、誤検知を減らすために、Azure のリソースやネットワークのほか、接続されているパートナー ソリューション (ファイアウォールやエンドポイント保護ソリューションなど) から、ログ データを収集、分析、統合します。 Security Center は、この情報を分析し、多くの場合、複数の情報源から得た情報との関連性を探りながら、脅威を特定します。

ASC は、Azure にデプロイされているか、他のオンプレミス環境やハイブリッド クラウド環境にデプロイされているかにかかわらず、リソースを監視します。 脅威の検出と対応の詳細については、[Security Center での脅威の検出と対応](security-center-detection-capabilities.md#asc-detects)に関する記事を参照してください。

## <a name="security-alert-types"></a>セキュリティ アラートの種類

次のトピックでは、リソースの種類に応じてさまざまな ASC アラートを説明します。

* [IaaS VM とサーバーのアラート](security-center-alerts-iaas.md)
* [ネイティブ コンピューティングのアラート](security-center-alerts-compute.md)
* [データ サービスのアラート](security-center-alerts-data-services.md)

次のトピックでは、Security Center が、 Azure にデプロイされたリソースに追加の保護レイヤーを適用するために、Azure インフラストラクチャとの統合から収集したさまざまなテレメトリを活用する方法について説明します。

* [サービス層のアラート](security-center-alerts-service-layer.md)
* [Azure のセキュリティ製品との統合](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>アラートのインシデントとは

セキュリティ インシデントは、各アラートの個別の一覧ではなく、関連するアラートのコレクションです。 Security Center は、フュージョンを使用して、さまざまなアラートと忠実度が低いシグナルをセキュリティ インシデントに関連付けます。

Security Center はインシデントを使用して、攻撃活動とすべての関連するアラートを単一のビューに表示します。 このビューにより、攻撃者がどのようなアクションを実行し、どのリソースが影響を受けたかを迅速に把握できます。 詳細については、[クラウドのスマート アラートの関連付け](security-center-alerts-cloud-smart.md)に関する記事を参照してください。

## <a name="get-started-with-alerts"></a>アラートの使用

ASC によって監視されるリソースの詳細、および ASC によって表示されるアラートへの対応方法に関するガイドラインについては、以下のトピックを参照してください。

* ASC によって保護されているプラットフォームと機能を確認するには、「[Azure Security Center でサポートされているプラットフォームと機能](security-center-os-coverage.md)」を参照してください。  
* セキュリティ インシデントの概要と ASC がそれらにどのように対応するかを理解するには、[Azure Security Center でのセキュリティ インシデントの処理方法](security-center-incident.md)に関する記事を参照してください。 
* 受け取るアラートの管理方法については、「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」をご覧ください。
* Security Center が正しく構成されていることを検証する方法と、テスト アラートの表示方法については、「[Azure Security Center でのアラート検証](security-center-alert-validation.md)」を参照してください。  


## <a name="upgrade-to-standard-for-advanced-detections"></a>高度な検出のための Standard へのアップグレード

高度な検出をセットアップする場合には、Azure Security Center Standard にアップグレードする必要があります。 

1. Security Center メニューで、 **[セキュリティ ポリシー]** を選択します。
2. Standard レベルに移動するサブスクリプションで、 **[設定の編集]** をクリックします。 
3. [設定] ページで **[価格レベル]** を選択します。 
   無料試用版が 1 か月間使用できます。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。 

## <a name="next-steps"></a>次の手順

この記事では、セキュリティ アラートとは何かと、Security Center で使用できる各種のアラートについて説明しました。 詳細については、次のトピックを参照してください。

* [Azure Security Center 計画および運用ガイド](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Security Center に関する FAQ](https://docs.microsoft.com/azure/security-center/security-center-faq): このサービスの使用に関してよく寄せられる質問が記載されています。

