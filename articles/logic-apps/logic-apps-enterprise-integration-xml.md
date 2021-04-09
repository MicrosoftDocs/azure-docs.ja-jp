---
title: XML メッセージとフラット ファイル
description: Enterprise Integration Pack を使用して Azure Logic Apps で XML メッセージを処理、検証、および変換する
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74792154"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Azure Logic Apps と Enterprise Integration Pack での XML メッセージとフラット ファイル

[Azure Logic Apps](logic-apps-overview.md) では、Enterprise Integration Pack を使用して、送受信する XML メッセージを処理できます。 BizTalk Server を使用したことがある場合は、Enterprise Integration Pack により、メッセージの変換と検証、フラット ファイルの操作、場合によっては XPath を使用したメッセージの機能強化やメッセージからの特定のプロパティの抽出という同様の機能が提供されます。 この領域の経験がない場合は、これらの機能によって、ロジック アプリのワークフローでのメッセージの処理方法が拡張されます。 たとえば、企業間 (B2B) のシナリオで特定の XML スキーマを使用している場合は、Enterprise Integration Pack を使用して、会社でのこれらのメッセージの処理方法を拡張できます。

たとえば、Enterprise Integration Pack には次の機能が含まれています。

* [XML 検証](logic-apps-enterprise-integration-xml-validation.md): 受信または送信 XML メッセージを特定のスキーマに対して検証します。

* [XML 変換](logic-apps-enterprise-integration-transform.md): マップを使用して、自社の要件またはパートナーの要件に基づいて XML メッセージを変換またはカスタマイズします。

* [フラット ファイルのエンコードとフラット ファイルのデコード](logic-apps-enterprise-integration-flatfile.md): フラット ファイルをエンコードまたはデコードします。

  たとえば、SAP では、フラット ファイル形式で IDOC ファイルを送受信します。 Logic Apps を含む、多くの統合プラットフォームで XML メッセージが作成されます。 そのため、XML ファイルをフラット ファイルに "変換" するフラット ファイル エンコーダーを使用するロジック アプリを作成できます。

* [XPath](workflow-definition-language-functions-reference.md#xpath): メッセージを機能強化し、メッセージから特定のプロパティを抽出します。 抽出されたプロパティは、宛先または中継のエンドポイントにメッセージをルーティングするために使用できます。

## <a name="sample"></a>サンプル

Azure Logic Apps の XML 機能を使用して、[本格的な機能を備えたロジック アプリをデプロイ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline)します (GitHub サンプル)。

## <a name="next-steps"></a>次のステップ

[Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) について詳細を確認する
