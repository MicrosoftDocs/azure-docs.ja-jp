---
title: "アプリケーション ゲートウェイのパスベース ルールを作成する - Azure Portal | Microsoft Docs"
description: "Azure Portal を使用してアプリケーション ゲートウェイのパスベース ルールを作成する方法について説明します。"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 87bd93bc-e1a6-45db-a226-555948f1feb7
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.openlocfilehash: b207e7e7bd83e56db68288190c7bedafa8b5b7fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-azure-portal"></a>Azure Portal を使用してアプリケーション ゲートウェイのパスベース ルールを作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

URL パスベースのルーティングを使用すると、HTTP 要求の URL パスに基づいてルートを関連付けできます。 アプリケーション ゲートウェイに記載されている URL に対して構成されたバックエンド サーバー プールへのルートがあるかどうかを調べて、定義されたプールにネットワーク トラフィックを送信します。 URL パスベースのルーティングの一般的な用途は、さまざまな種類のコンテンツに対する要求をさまざまなバックエンド サーバー プールに負荷分散することです。

アプリケーション ゲートウェイには、基本ルールと URL パスベース ルールという 2 種類のルールがあります。 基本ルールの種類は、バックエンド プールにラウンド ロビン サービスを提供します。 パスベースのルールは、適切なバックエンド プールを選択する際、ラウンド ロビンによる分散だけでなく要求 URL のパス パターンも使用します。

## <a name="scenario"></a>シナリオ

次のシナリオでは、既存のアプリケーション ゲートウェイにパスベース ルールを作成します。
このシナリオでは、[ポータルを使用してアプリケーション ゲートウェイを作成する](application-gateway-create-gateway-portal.md)ための手順を、あらかじめ完了していることを前提としています。

![URL ルート][scenario]

## <a name="createrule"></a>パスベース ルールの作成

パスベースのルールには、独自のリスナーが必要です。 ルールを作成する前に、使用するリスナーが利用できる状態であることを確認してください。

### <a name="step-1"></a>手順 1

[Azure Portal](http://portal.azure.com) に移動し、既存のアプリケーション ゲートウェイを選択します。 **[ルール]** をクリックします。

![Application Gateway の概要][1]

### <a name="step-2"></a>手順 2.

**[パス ベース]** ボタンをクリックして、新しいパスベース ルールを追加します。

### <a name="step-3"></a>手順 3.

**[Add path-based rule (パスベース ルールの追加)]** ブレードには 2 つのセクションがあります。 最初のセクションでは、リスナー、ルールの名前、既定のパス設定を定義しました。 既定のパス設定は、カスタムのパスベース ルートに分類されないルートのための設定です。 **[Add path-based rule (パスベース ルールの追加)]** ブレードの 2 番目のセクションでは、パスベース ルールそのものを定義します。

**基本設定**

* **[名前]**: ポータルでアクセス可能なルールのフレンドリ名。
* **[リスナー]**: ルールで使用するリスナー。
* **[既定のバックエンド プール]**: 既定のルールに使用するバックエンド プール。
* **[既定の HTTP 設定]**: 既定のルールに使用する HTTP 設定。

**パスベース ルールの設定**

* **[名前]**: パスベース ルールのフレンドリ名。
* **[パス]**: トラフィックを転送するときにルールが検索するパス。
* **[バックエンド プール]**: ルールで使用するバックエンド。
* **[HTTP 設定]**: ルールで使用する HTTP 設定。

> [!IMPORTANT]
> **[パス]** 設定は、照合するパス パターンの一覧です。 各パターンはスラッシュで始まる必要があり、アスタリスクは末尾にのみ使用できます。 /xyz、/xyz*、/xyz/* などが有効な例です。  

![情報を入力した [Add path-based rule (パスベース ルールの追加)] ブレード][2]

既存のアプリケーション ゲートウェイへのパスベース ルールの追加は、Azure Portal を利用すると簡単にできます。 パスベース ルールを作成した後は、編集して新しいルールを追加できます。 

![新しいパスベース ルールの追加][3]

この手順では、パスベースのルートを構成します。 要求が再書き込みされないことを理解しておくことは重要です。 要求が着信すると、アプリケーション ゲートウェイは要求を検査し、URL パターンに基づいて適切なバックエンド プールに要求を送信します。

## <a name="next-steps"></a>次のステップ

Azure Application Gateway で SSL オフロードを構成する方法については、「[Azure Portal を使用して SSL オフロード用のアプリケーション ゲートウェイを構成する](application-gateway-ssl-portal.md)」をご覧ください。

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png
