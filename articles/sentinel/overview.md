---
title: Azure Sentinel とは | Microsoft Docs
description: スケーラブルでクラウドネイティブ型のセキュリティ情報イベント管理 (SIEM) およびセキュリティ オーケストレーション自動応答 (SOAR) ソリューションである Azure Sentinel について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 4bc5732c1b2349f34297ecaacc7eb3176fdba611
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570482"
---
# <a name="what-is-azure-sentinel"></a>Azure Sentinel とは

Microsoft Azure Sentinel は、スケーラブルでクラウドネイティブ型の **セキュリティ情報イベント管理 (SIEM)** および **セキュリティ オーケストレーション自動応答 (SOAR)** ソリューションです。 Azure Sentinel は、高度なセキュリティ分析と脅威インテリジェンスを企業全体で実現し、アラートの検出、脅威の可視性、予防的な捜索、および脅威への対応のための 1 つのソリューションを提供します。 

Azure Sentinel を使用すると、ますます巧妙化する攻撃、増加するアラート、解決までに長い期間がかかることに伴うストレスを軽減し、企業全体を俯瞰的に見ることができます。

- **クラウドの規模でデータを収集** します。オンプレミスと複数のクラウド内の両方ですべてのユーザー、デバイス、アプリケーション、インフラストラクチャにわたって収集します。 

- **以前に検出されなかった脅威を検出** します。Microsoft の 分析と類を見ない脅威インテリジェンスを使用して、誤判定を最小限に抑えます。 

- **人工知能を使用して脅威を調査** します。Microsoft の長年にわたるサイバー セキュリティ業務を活用しながら、大規模に疑わしいアクティビティを捜索します。 

- **インシデントに迅速に対応** します。一般的なタスクの組み込みのオーケストレーションとオートメーションを使用します。

![Azure Sentinel のコア機能](./media/overview/core-capabilities.png)

Azure Sentinel は、既存のさまざまな Azure サービスを基に構築され、Log Analytics や Logic Apps などの実績のある基盤をネイティブに組み込んでいます。 Azure Sentinel は、AI による調査と検出を強化し、Microsoft の脅威インテリジェンス ストリームを提供しています。また、ユーザーが独自の脅威インテリジェンスを取り入れることもできます。 

## <a name="connect-to-all-your-data"></a>すべてのデータに接続する

Azure Sentinel をオンボードするには、まず[セキュリティ ソースに接続する](connect-data-sources.md)必要があります。 Azure Sentinel には、Microsoft 365 Defender (旧 Microsoft Threat Protection) ソリューションや、Office 365、Azure AD、Microsoft Defender for Identity (旧 Azure ATP)、Microsoft Cloud App Security を含む Microsoft 365 ソースなど、すぐに使用できる、かつリアルタイムの統合を提供する Microsoft ソリューション用コネクタが多数付属しています。 さらに、Microsoft 以外のソリューション用のより広範なセキュリティ エコシステムへの組み込みコネクタがあります。 一般的なイベント形式 (Syslog や REST-API) を使用して、データ ソースを Azure Sentinel に接続することもできます。 

![データ コレクター](./media/collect-data/collect-data-page.png)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="workbooks"></a>Workbooks

Azure Sentinel に[データ ソースを接続](quickstart-onboard.md)したら、Azure Sentinel と Azure Monitor ブックの統合を使用してデータを監視できます。これにより、多用途のカスタム ブックを作成できます。 ブックの表示は Azure Sentinel では異なりますが、[Azure Monitor ブックを使用して対話型レポートを作成する](../azure-monitor/visualize/workbooks-overview.md)方法を確認すると役立つ場合があります。 Azure Sentinel を使用すると、データ全体に対してカスタム ブックを作成できます。また、用意されている組み込みのブック テンプレートを使用してデータ ソースに接続すると、すぐにデータ全体の分析情報をすばやく得ることもできます。

![ダッシュボード](./media/tutorial-monitor-data/access-workbooks.png)

## <a name="analytics"></a>Analytics

ノイズを減らし、確認および調査する必要があるアラートの数を最小限に抑えるために、Azure Sentinel は[分析を使用してアラートをインシデントに関連付けます](tutorial-detect-threats-built-in.md)。 **インシデント** は、調査して解決できる対応可能な脅威をまとめた、関連するアラートのグループです。 組み込みの相関関係ルールをそのまま使用するか、独自のルールを作成する際の出発点として使用します。 Azure Sentinel には、ネットワークの動作をマップし、リソース全体の異常を探すための機械学習ルールも用意されています。 これらの分析では、さまざまなエンティティに関する信頼度の低いアラートを組み合わせて信頼度の高いセキュリティ インシデントにすることで、点を結び付けます。

![インシデント](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>セキュリティのオートメーションとオーケストレーション

一般的なタスクを自動化し、Azure サービスおよび既存のツールと統合する[プレイブックを使用してセキュリティ オーケストレーションを簡略化](tutorial-respond-threats-playbook.md)します。 Azure Logic Apps を基に構築された Azure Sentinel のオートメーションおよびオーケストレーション ソリューションは、新しいテクノロジや脅威が発生したときにスケーラブルなオートメーションを可能にする高度に拡張可能なアーキテクチャを備えています。 Azure Logic Apps を使用してプレイブックを作成する際は、成長を続けている組み込みプレイブックのギャラリーから選択できます。 これらには、Azure Functions などのサービス用に [200 以上のコネクタ](../connectors/apis-list.md)が含まれます。 コネクタを使用すると、コード、ServiceNow、Jira、Zendesk、HTTP 要求、Microsoft Teams、Slack、Windows Defender ATP、および Cloud App Security に任意のカスタム ロジックを適用できます。

たとえば、ServiceNow チケット発行システムを使用している場合は、提供されているツールを使用して、Azure Logic Apps を使用してワークフローを自動化し、特定のイベントが検出されるたびに ServiceNow でチケットを開くことができます。

![プレイブック](./media/tutorial-respond-threats-playbook/logic-app.png)


## <a name="investigation"></a>調査

現在プレビュー段階にある Azure Sentinel の[詳細調査](tutorial-investigate-cases.md)ツールを使用して、潜在的なセキュリティの脅威の範囲を把握し、根本的な原因を見つけるために役立てることができます。 対話型グラフ上のエンティティを選択し、特定のエンティティについて興味がある質問をして、そのエンティティとその関連性を掘り下げて脅威の根本的な原因を突き止めることができます。 

![調査](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>検出

MITER フレームワークに基づく Azure Sentinel の[強力な捜索検索およびクエリ ツール](hunting.md)を使用すると、アラートがトリガーされる前に、組織のデータ ソース全体でセキュリティの脅威を予防的に捜索することができます。 攻撃の可能性について重要な分析情報を提供する捜索クエリがわかったら、クエリに基づいてカスタム検出ルールを作成し、セキュリティ インシデントの対応者へのアラートとしてそのような分析情報を表示することができます。 捜索中に興味深いイベントのブックマークを作成し、後で戻って他のユーザーと共有し、他の関連イベントとグループ化して、説得力のある調査のインシデントを作成することができます。

![ハンティング機能の概要](./media/overview/hunting.png)

## <a name="community"></a>コミュニティ

Azure Sentinel コミュニティは、脅威の検出とオートメーションのための強力なリソースです。 Microsoft のセキュリティ アナリストは、新しいブック、プレイブック、捜索クエリなどを常に作成して追加し、お客様の環境で使用できるようにそれらをコミュニティに投稿しています。 プライベート コミュニティの GitHub [リポジトリ](https://aka.ms/asicommunity)からサンプル コンテンツをダウンロードして、Azure Sentinel 用のカスタム ブック、捜索クエリ、ノートブック、プレイブックを作成できます。 

![ユーザー コミュニティを探索する](./media/overview/community.png)

## <a name="next-steps"></a>次のステップ

- Azure Sentinel を使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、 [無料試用版](https://azure.microsoft.com/free/)にサインアップできます。
- [データを Azure Sentinel にオンボード](quickstart-onboard.md)し、[データや潜在的な脅威を視覚化する](quickstart-get-visibility.md)方法を確認します。