---
title: Azure Security Center でのセキュリティ インシデントの管理 | Microsoft Docs
description: このドキュメントは、Azure Security Center を使用してセキュリティ インシデントを処理するために役立ちます。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: memildin
ms.openlocfilehash: 2a22039b3499f1ba0f2d7acb2e86a1991d35a380
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202124"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Azure Security Center でのセキュリティ インシデントの管理

セキュリティ アラートのトリアージと調査は、熟練のセキュリティ アナリストでさえ長時間を要することのある作業であり、多くのユーザーにとっては、どこから始めればよいのかもわかりません。 Security Center では、[分析](security-center-detection-capabilities.md)を利用して異なる[セキュリティ アラート](security-center-managing-and-responding-alerts.md)間の情報を結び付け、攻撃キャンペーンとそれに関連するすべてのアラートを単一のビューに表示します。これにより、攻撃者がどのような操作を実行し、どのリソースが影響を受けたのかを迅速に把握することができます。

このトピックでは Security Center のインシデントの概要とアラートを修復する方法について説明します。

## <a name="what-is-a-security-incident"></a>セキュリティ インシデントとは

Security Center でのセキュリティ インシデントとは、 [キル チェーン](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) のパターンに一致するリソースに関するすべての警告を集約したものです。 インシデントは [[セキュリティ アラート]](security-center-managing-and-responding-alerts.md) の一覧に表示されます。 インシデントをクリックすると関連アラートが表示されるので、発生後との詳細を入手できます。

## <a name="managing-security-incidents"></a>セキュリティ インシデントの管理

1. Security Center ダッシュボードで **[セキュリティ アラート]** タイルをクリックします。 インシデントとアラートが一覧表示されます。 セキュリティ インシデントの説明には、他の警告とは異なるアイコンが付いている点に注意してください。

    ![セキュリティ インシデントの表示](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 詳細を表示するには、インシデントをクリックします。 **[セキュリティインシデントが検出されました]** ブレードに詳細が表示されます。 **[一般情報]** セクションには、セキュリティ アラートを始動させたものに関する分析情報があります。 ターゲット リソース、発信元 IP アドレス (該当する場合)。アラートが現在もアクティブかどうか、推奨修復方法などの情報が表示されます。  

    ![Azure Security Center でのセキュリティ インシデントへの対応](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. 各アラートの詳細を取得するには、アラートをクリックします。 Security Center から提案される修復方法は、セキュリティ アラートによって異なります。

   > [!NOTE]
   > 同じアラートが 1 つのインシデントに含まれることもあれば、スタンドアロン アラートとして表示されることもあります。

    ![[アラートの詳細]](./media/security-center-incident/security-center-incident-alert.png)

1. 各アラートに対して与えられている修復手順に従います。

アラートの詳細については、[セキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md)に関するページを参照してください。

次のトピックでは、リソースの種類に応じてさまざまなアラートを説明します。

* [IaaS VM とサーバーのアラート](security-center-alerts-iaas.md)
* [ネイティブ コンピューティングのアラート](security-center-alerts-compute.md)
* [データ サービスのアラート](security-center-alerts-data-services.md)

次のトピックでは、Security Center が、Azure にデプロイされたリソースに追加の保護レイヤーを適用するために、Azure インフラストラクチャとの統合から収集したさまざまなテレメトリを活用する方法について説明します。

* [サービス層のアラート](security-center-alerts-service-layer.md)
* [Azure のセキュリティ製品との統合](security-center-alerts-integration.md)

## <a name="see-also"></a>関連項目
このドキュメントでは、Security Center でのセキュリティ インシデント機能の使用方法について説明しました。 セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center のセキュリティ アラート](security-center-alerts-overview.md)
* [セキュリティ アラートの管理](security-center-managing-and-responding-alerts.md)
* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)
* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/)-- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
