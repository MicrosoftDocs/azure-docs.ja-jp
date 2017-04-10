---
title: "ワークフロー内の XML メッセージの操作 - Azure Logic Apps | Microsoft Docs"
description: "Enterprise Integration Pack を使用した、ロジック アプリでの XML メッセージの処理、検証、変換、強化と、企業間取引シナリオ"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: mandia
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5913c81088724ef946ae147f4f3154fa6aefd22e
ms.openlocfilehash: 9d40c5028fd8b3ab20f7562dce9274664e4e56ac
ms.lasthandoff: 03/01/2017


---
# <a name="validate-and-transform-xml-encode-and-decode-flat-files-and-enrich-messages-features-in-logic-apps"></a>ロジック アプリでの XML の検証と変換、フラット ファイルのエンコードとデコード、メッセージ機能の強化

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

