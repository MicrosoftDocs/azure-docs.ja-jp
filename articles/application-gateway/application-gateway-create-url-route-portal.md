---
title: "パスベース ルールを作成する - Azure Application Gateway - Azure Portal | Microsoft Docs"
description: "ポータルを使用して Application Gateway のパスベース ルールを作成する方法を説明します"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 87bd93bc-e1a6-45db-a226-555948f1feb7
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: e92c33b81aa1b69da0336bec1260cbda96c7a72e


---
# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-portal"></a>ポータルを使用して Application Gateway のパスベース ルールを作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-url-route-arm-ps.md)

URL パスベースのルーティングを使用すると、HTTP 要求の URL パスに基づいてルートの関連付けを行うことができます。 Application Gateway の URL 一覧に対して構成されたバックエンド プールへのルートがあるかどうかを調べ、定義されたバックエンド プールにネットワーク トラフィックを送信します。 URL ベースのルーティングの一般的な用途は、さまざまな種類のコンテンツに対する要求をさまざまなバックエンド サーバー プールに負荷分散することです。

URL ベースのルーティングでは、新しいルールの種類が Application Gateway に導入されています。 Application Gateway には、基本ルールとパスベース ルールという&2; つのルールの種類があります。 基本ルールは、バックエンド プールに対してラウンドロビン サービスを提供します。一方、パスベース ルールはラウンドロビン サービスに加えて、バックエンド プールを選択しながら要求 URL のパス パターンも考慮に入れます。

## <a name="scenario"></a>シナリオ

次のシナリオでは、既存の Application Gateway に、パスベース ルールを作成します。
このシナリオでは、 [アプリケーション ゲートウェイの作成](application-gateway-create-gateway-portal.md)に関する手順を既に実行したことを前提としています。

![url ルート][scenario]

## <a name="a-namecreateruleacreate-the-path-based-rule"></a><a name="createrule"></a>パスベース ルールを作成する

パスベース ルールには独自のリスナーが必要です。ルールを作成する前に、使用する利用可能なリスナーがあることを確認します。

### <a name="step-1"></a>手順 1

[Azure ポータル](http://portal.azure.com)に移動し、既存のアプリケーション ゲートウェイを選択します。 **[ルール]**

![Application Gateway の概要][1]

### <a name="step-2"></a>手順 2.

**[Path-based (パスベース)]** ボタンをクリックして、新しいパスベース ルールを追加します。

### <a name="step-3"></a>手順 3.

**[Add path-based rule (パスベース ルールの追加)]** ブレードには&2; つのセクションがあります。 最初のセクションでは、リスナー、ルールの名前、既定のパス設定を定義しました。 既定のパス設定は、カスタムのパスベース ルートに分類されないルートのための設定です。 **[Add path-based rule (パスベース ルールの追加)]** ブレードの&2; 番目のセクションでは、パスベース ルールそのものを定義します。

**基本設定**

* **名前** - ポータルでアクセス可能なルールのフレンドリ名です。
* **リスナー** - ルールで使用するリスナーです。
* **Default backend pool (既定のバックエンド プール)** - 既定のルールで使用するバックエンドを定義する設定です。
* **Default HTTP settings (既定の HTTP 設定)** - 既定のルールで使用する HTTP 設定を定義する設定です。

**パスベース ルール**

* **名前** - パスベース ルールのフレンドリ名です。
* **パス** - トラフィックを転送するときに、ルールで検索するパスを定義します。
* **バックエンド プール** - ルールで使用するバックエンドを定義する設定です。
* **HTTP setting (HTTP 設定)** - ルールで使用する HTTP 設定を定義する設定です。

> [!IMPORTANT]
> パス: 照合するパス パターンの一覧です。 それぞれ "/" で始まる必要があり、"\*" を配置できるのは末尾だけです。 有効な例としては、/xyz、/xyz*、または /xyz/* があります。  

![情報を入力した [Add path-based rule (パスベース ルールの追加)] ブレード][2]

既存の Application Gateway へのパスベース ルールの追加は、ポータルを使うと簡単にできます。 パスベース ルールを作成したら、編集して追加のルールを簡単に追加できます。 

![追加のパスベース ルールの追加][3]

## <a name="next-steps"></a>次のステップ

Azure Application Gateway で SSL オフロードを構成する方法を学習するには、 [SSL オフロードの構成](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png



<!--HONumber=Jan17_HO4-->


