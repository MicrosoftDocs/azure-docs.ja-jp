---
title: "B2B のための Enterprise Integration - Azure Logic Apps | Microsoft Docs"
description: "Enterprise Integration Pack を使用して、ロジック アプリで B2B ワークフローを構築してエンタープライズ統合シナリオをサポートする"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: dd517c4d-1701-4247-b83c-183c4d8d8aae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: estfan
translationtype: Human Translation
ms.sourcegitcommit: 5b1b65e3d1066bea6958fa6461a157ee39fbe7dc
ms.openlocfilehash: 514942b2552564f669d73b997a2d355ddb477b22
ms.lasthandoff: 02/15/2017


---
# <a name="overview-b2b-scenarios-and-communication-with-the-enterprise-integration-pack"></a>概要: Enterprise Integration Pack を使用した B2B シナリオと通信

Azure Logic Apps での企業間 (B2B) のワークフローとシームレスな通信を実現するために、Microsoft のクラウド ベースのソリューションである Enterprise Integration Pack を使用して、エンタープライズ統合シナリオを有効にすることができます。 組織は、異なるプロトコルと形式を使用している組織ともメッセージを電子的に交換することができます。 Enterprise Integration Pack により、メッセージの形式が組織のシステムで解釈して処理できる形式に変換されます。 メッセージの交換には、[AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md)、[EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md) などの業界標準のプロトコルを使用できます。 暗号化とデジタル署名を使用して、メッセージをセキュリティで保護することもできます。

BizTalk Server または Microsoft Azure BizTalk Services を使い慣れている方は、ほとんどの概念が類似しているため、Enterprise Integration の機能を使いやすいと感じるものと思われます。 主な違いの&1; つは、B2B 通信で使用されるアーティファクトの保管と管理を簡単にするために、Enterprise Integration では統合アカウントを使用する点です。 

Enterprise Integration Pack のアーキテクチャは、"統合アカウント" をベースにしています。 統合アカウントは、スキーマ、パートナー、証明書、マップ、契約などのアーティファクトをすべて格納するクラウド ベースのコンテナーです。 これらのアーティファクトを使用して、B2B アプリケーションを設計、デプロイ、管理できるほか、ロジック アプリの B2B ワークフローを構築することもできます。 ただし、アーティファクトを使用する前に、統合アカウントとロジック アプリの関連付けを行っておく必要があります。 その後、ロジック アプリから統合アカウントのアーティファクトにアクセスできるようになります。

## <a name="why-should-you-use-enterprise-integration"></a>Enterprise Integration を使用する理由

* Enterprise Integration を使用すれば、対象のアーティファクトをすべて&1; か所 (統合アカウント) に格納できます。
* Azure Logic Apps エンジンとそのコネクタすべてを利用することで、B2B ワークフローを構築し、サード パーティの SaaS アプリケーション、オンプレミスのアプリケーション、カスタム アプリケーションと統合できます。
* Azure Functions を使用して、ロジック アプリのカスタム コードを作成できます。

## <a name="how-to-get-started-with-enterprise-integration"></a>Enterprise Integration の使用を開始する方法

**Azure Portal** のロジック アプリ デザイナーを利用して、Enterprise Integration Pack を使用した B2B アプリを構築、管理できます。 Logic Apps の管理に [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "Logic Apps PowerShell 関連トピック") を使用することもできます。

Azure Portal でアプリを作成する前に必要な手順の概要は、次のとおりです。

![概要のイメージ](media/logic-apps-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>いくつかの一般的なシナリオ

Enterprise Integration では、次の業界標準がサポートされています。

* EDI - Electronic Data Interchange (電子データ交換)
* EAI - Enterprise Application Integration (エンタープライズ アプリケーション統合)

## <a name="heres-what-you-need-to-get-started"></a>使用を開始するために必要なもの

* 統合アカウントが付属した Azure サブスクリプション
* マップとスキーマを作成するための Visual Studio 2015
* [Microsoft Azure Logic Apps Enterprise Integration Tools for Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it-now"></a>今すぐ試す

Azure Logic Apps の B2B 機能を使用する、[完全に動作する AS2 送信および受信ロジック アプリのサンプルをデプロイ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)します。

## <a name="learn-more"></a>詳細情報
* [契約](../logic-apps/logic-apps-enterprise-integration-agreements.md "エンタープライズ統合契約についての詳細情報")
* [企業間 (B2B) シナリオ](../logic-apps/logic-apps-enterprise-integration-b2b.md "B2B 機能を使用したロジック アプリの作成方法についての詳細情報")  
* [証明書](logic-apps-enterprise-integration-certificates.md "Enterprise Integration の証明書についての詳細情報")
* [フラット ファイル エンコード/デコード](logic-apps-enterprise-integration-flatfile.md "フラット ファイル コンテンツをエンコードおよびデコードする方法についての詳細情報")  
* [統合アカウント](../logic-apps/logic-apps-enterprise-integration-accounts.md "統合アカウントについての詳細情報")
* [マップ](../logic-apps/logic-apps-enterprise-integration-maps.md "Enterprise Integration のマップについての詳細情報")
* [パートナー](logic-apps-enterprise-integration-partners.md "Enterprise Integration のパートナーについての詳細情報")
* [スキーマ](logic-apps-enterprise-integration-schemas.md "Enterprise Integration のスキーマについての詳細情報")
* [XML メッセージの検証](logic-apps-enterprise-integration-xml.md "ロジック アプリを使用した XML メッセージの検証方法についての詳細情報")
* [XML 変換](logic-apps-enterprise-integration-transform.md "Enterprise Integration のマップについての詳細情報")
* [エンタープライズ統合コネクタ](../connectors/apis-list.md "Enterprise Integration Pack コネクタについての詳細情報")
* [統合アカウント メタデータ](../logic-apps/logic-apps-enterprise-integration-metadata.md "統合アカウントのメタデータについての詳細情報")
* [B2B メッセージを監視する](logic-apps-monitor-b2b-message.md "B2B メッセージの監視方法についての詳細情報")
* [OMS ポータルでの B2B メッセージの追跡](logic-apps-track-b2b-messages-omsportal.md "OMS ポータルでの B2B メッセージの追跡についての詳細情報")


