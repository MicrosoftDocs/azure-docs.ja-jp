---
title: カスタム プローブを作成する - Azure Application Gateway - Azure Portal | Microsoft Docs
description: ポータルを使用してアプリケーション ゲートウェイ用カスタム プローブを作成する方法について説明します。
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: 90d576fd00a39f7e871cbe0922ce131dfbe38ff0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58862167"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>ポータルを使用して Application Gateway 用カスタム プローブを作成する

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell (Azure クラシック PowerShell)](application-gateway-create-probe-classic-ps.md)

この記事では、Azure Portal を使用して既存の Application Gateway にカスタム プローブを追加します。 カスタム プローブは、特定の正常性チェック ページがあるアプリケーションや、既定の Web アプリケーションに対して正常な応答を返さないアプリケーションに役立ちます。

## <a name="before-you-begin"></a>開始する前に

Application Gateway がまだない場合は、[Application Gateway の作成](application-gateway-create-gateway-portal.md)に関する記事を参照して、使用する Application Gateway を作成します。

## <a name="createprobe"></a>プローブの作成

プローブは、ポータルを通じて 2 段階の手順で構成されます。 最初の手順で、プローブを作成します。 次の手順で、Application Gateway のバックエンド http 設定にプローブを追加します。

1. [Azure Portal](https://portal.azure.com) にログインします。 まだアカウントを持っていない場合は、[1 か月間の無料試用版](https://azure.microsoft.com/free)にサインアップできます。

1. Azure Portal の [お気に入り] ウィンドウで [すべてのリソース] をクリックします。 [すべてのリソース] ブレードで Application Gateway をクリックします。 選択したサブスクリプションに既存のリソースがいくつもある場合は、[名前でフィルター] ボックスに「partners.contoso.net」と入力すると、 目的のアプリケーション ゲートウェイがすぐに見つかります。

1. **[プローブ]** をクリックし、**[追加]** ボタンをクリックしてプローブを追加します。

   ![Add Probe blade with information filled out][1]

1. **[正常性プローブの追加]** ブレードでプローブに必要な情報を入力し、完了したら **[OK]** をクリックします。

   |**設定** | **値** | **詳細**|
   |---|---|---|
   |**Name**|customProbe|この値は、ポータルでアクセス可能なプローブのフレンドリ名です。|
   |**プロトコル**|HTTP または HTTPS | 正常性プローブが使用するプロトコルです。|
   |**Host**|つまり  contoso.com|この値は、プローブに使用されるホスト名です。 Application Gateway でマルチサイトが構成されている場合にのみ適用されます。それ以外の場合は、"127.0.0.1" を使用します。 この値は VM ホスト名とは異なります。|
   |**パス**|/ または別のパス|カスタム プローブの完全な URL の残りの部分です。 パスは先頭が "/" である必要があります。 既定のパス http:\//contoso.com では "/" のみを使用します。 |
   |**間隔 (秒)**|30|正常性を確認するためにプローブを実行する頻度です。 30 秒未満に設定しないようにすることをお勧めします。|
   |**タイムアウト (秒)**|30|タイムアウトまでにプローブが待機する時間です。タイムアウトまでの時間は、バックエンドの正常性ページが利用可能であることを確認するために HTTP 呼び出しを実行できるだけの長さである必要があります。|
   |**異常のしきい値**|3|異常であると見なされるまでの試行の失敗回数です。 しきい値を 0 に設定すると、正常性チェックが失敗した場合に、バックエンドが異常であると即座に判断されます。|

   > [!IMPORTANT]
   > ホスト名はサーバー名と同じではありません。 この値は、アプリケーション サーバーで実行されている仮想ホストの名前です。 プローブは、 http://(ホスト名):(httpsetting のポート)/urlPath に送信されます。

## <a name="add-probe-to-the-gateway"></a>ゲートウェイへのプローブの追加

プローブが作成されたら、ゲートウェイに追加します。 プローブの設定は、アプリケーション ゲートウェイのバックエンド http 設定で行います。

1. Application Gateway の **[HTTP 設定]** をクリックし、ウィンドウに表示される現在のバックエンド http 設定をクリックして、構成ブレードを表示します。

   ![https settings window][2]

1. **appGatewayBackEndHttpSettings** 設定ブレードで、**[カスタム プローブの使用]** チェックボックスをオンにし、「[プローブの作成](#createprobe)」セクションで作成したプローブを **[カスタム プローブ]** ドロップダウンで選択します。
   完了したら、**[保存]** をクリックし、設定を適用します。

既定のプローブでは、Web アプリケーションへの既定のアクセスがチェックされます。 カスタム プローブが作成されたら、Application Gateway は、定義されているカスタム パスを使用して、バックエンド サーバーの正常性を監視します。 定義された条件に基づいて、Application Gateway は、プローブで指定されているパスをチェックします。 host:Port/path への呼び出しによって HTTP 200-399 という状態の応答が返されない場合は、異常のしきい値に達した後、サーバーがローテーションから除外されます。 プローブは、もう一度正常になるタイミングを判断するために、異常なインスタンス上で続行します。 インスタンスが正常なサーバー プールに戻されると、トラフィックはもう一度そこに流れ始め、インスタンスへのプローブは、通常どおり、ユーザーが指定した間隔で続行します。

## <a name="next-steps"></a>次の手順

Azure Application Gateway で SSL オフロードを構成する方法を学習するには、[SSL オフロードの構成](application-gateway-ssl-portal.md)に関するページをご覧ください。

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

