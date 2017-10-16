---
title: "Azure Logic Apps での ProjectOnline コネクタ | Microsoft Docs"
description: "Azure App Service を使用してロジック アプリを作成します。 Project Online は、プロジェクト ポートフォリオ管理 (PPM) や日常業務を行うための Microsoft の柔軟なオンライン ソリューションです。 Office 365 経由で配信される Project Online により、強力なプロジェクト管理機能が直ちに実現します。ほとんどすべての場所やデバイスから、プロジェクトとプロジェクト ポートフォリオ投資の計画、優先順位付け、管理を実行できます。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: b075e2eb36f54afb7544e0aeb698701cd224ff93
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-projectonline-connector"></a>ProjectOnline コネクタの使用
Project Online は、プロジェクト ポートフォリオ管理 (PPM) や日常業務を行うための Microsoft の柔軟なオンライン ソリューションです。 Office 365 経由で配信される Project Online により、強力なプロジェクト管理機能が直ちに実現します。ほとんどすべての場所やデバイスから、プロジェクトとプロジェクト ポートフォリオ投資の計画、優先順位付け、管理を実行できます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事を参照してください。

## <a name="create-a-connection-to-projectonline"></a>ProjectOnline への接続を作成する
ProjectOnline を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。 

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |はい |ProjectOnline の資格情報を提供します |

> [!INCLUDE [Steps to create a connection to ProjectOnline](../../includes/connectors-create-api-projectonline.md)]
> 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/projectonline/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。