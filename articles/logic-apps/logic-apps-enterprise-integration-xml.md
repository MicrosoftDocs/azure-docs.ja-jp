---
title: B2B エンタープライズ統合用の XML メッセージ - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps と Enterprise Integration Pack での B2B ソリューション向け XML メッセージの処理、検証、変換、強化
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.date: 02/27/2017
ms.openlocfilehash: a75ac9773072423c13eef85ecad29c632c13d024
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873255"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Azure Logic Apps と Enterprise Integration Pack での XML メッセージとフラット ファイル

ロジック アプリを使用して、送受信する XML メッセージを処理することができます。 この機能は、Enterprise Integration Pack に含まれています。 BizTalk Server を使用しているユーザーには、Enterprise Integration Pack で、メッセージの変換と検証、フラット ファイルの操作、XPath を使用したメッセージの強化、メッセージの特定のプロパティの抽出と同等の機能が提供されています。 

このスペースを利用した経験のないユーザーがこれらの機能を利用すると、ワークフロー内でメッセージを処理する方法の幅が広がります。 たとえば、企業間取引シナリオで特定の XML スキーマを使用している場合、Enterprise Integration Pack を使用することで、社内でのこれらのメッセージの処理方法を拡張することができます。 

Enterprise Integration Pack には以下のものが含まれます。 

* [XML 検証](logic-apps-enterprise-integration-xml-validation.md "XML メッセージの検証についての詳細情報") - 送受信 XML メッセージを特定のスキーマに対照らして検証します。
* [XML 変換](../logic-apps/logic-apps-enterprise-integration-transform.md "XML メッセージの変換とマップについての詳細情報") - 社内またはパートナーの要件に基づいて、XML メッセージを変換またはカスタマイズします。
* [フラット ファイルのエンコードとデコード](logic-apps-enterprise-integration-flatfile.md "フラット ファイルのエンコードとデコードについての詳細情報") - フラット ファイルをエンコードまたはデコードします。 たとえば、SAP では、フラット ファイル形式で IDOC ファイルを送受信します。 Logic Apps を含む、多くの統合プラットフォームで XML メッセージが作成されます。 そのため、XML ファイルをフラット ファイルに "変換" するフラット ファイル エンコーダーを使用するロジック アプリを作成できます。 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) -メッセージを強化し、メッセージから特定のプロパティを抽出します。 抽出されたプロパティは、宛先または中継のエンドポイントにメッセージをルーティングするために使用できます。

## <a name="try-it-out"></a>試してみる
Azure Logic Apps の XML 機能を使用して、[本格的な機能を備えたロジック アプリをデプロイ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline)します (GitHub サンプル)。

## <a name="learn-more"></a>詳細情報
[Enterprise Integration Pack についての詳細情報](../logic-apps/logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")
