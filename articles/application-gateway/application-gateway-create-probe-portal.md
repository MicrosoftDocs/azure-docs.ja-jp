---
title: "カスタム プローブを作成する - Azure Application Gateway - Azure Portal | Microsoft Docs"
description: "ポータルを使用してアプリケーション ゲートウェイ用カスタム プローブを作成する方法について説明します。"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: 14b715013b4154a1fa079c0dc470e675d7cf4c1f
ms.lasthandoff: 01/24/2017


---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>ポータルを使用して Application Gateway 用カスタム プローブを作成する
> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell (Azure クラシック PowerShell)](application-gateway-create-probe-classic-ps.md)

[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## <a name="scenario"></a>シナリオ

次のシナリオでは、既存のアプリケーション ゲートウェイに、カスタムの正常性プローブを作成します。
このシナリオでは、 [アプリケーション ゲートウェイの作成](application-gateway-create-gateway-portal.md)に関する手順を既に実行したことを前提としています。

## <a name="a-namecreateprobeacreate-the-probe"></a><a name="createprobe"></a>プローブの作成

プローブは、ポータルを通じて&2; 段階の手順で構成されます。 最初の手順で、プローブを作成します。 次に、アプリケーション ゲートウェイのバックエンド http 設定にプローブを追加します。

### <a name="step-1"></a>手順 1

[Azure ポータル](http://portal.azure.com)に移動し、既存のアプリケーション ゲートウェイを選択します。

![Application Gateway の概要][1]

### <a name="step-2"></a>手順 2.

**[プローブ]** をクリックし、**[追加]** ボタンをクリックしてプローブを追加します。

![Add Probe blade with information filled out][2]

### <a name="step-3"></a>手順 3.

プローブに必要な情報を入力し、完了したら **[OK]**をクリックします。

* **[名前]** - この値は、ポータルでアクセス可能なプローブのフレンドリ名です。
* **[ホスト]** - この値は、プローブに使用されるホスト名です。 Application Gateway でマルチサイトが構成されている場合にのみ適用されます。それ以外の場合は、"127.0.0.1" を使用します。 この値は VM ホスト名とは異なります。
* **[パス]** - カスタム プローブの完全な URL の残りの部分です。 パスは先頭が "/" である必要があります。
* **[間隔 (秒)]** - 正常性を確認するためにプローブを実行する頻度です。 30 秒未満に設定しないようにすることをお勧めします。
* **[タイムアウト (秒)]** - タイムアウトまでにプローブが待機する時間です。 タイムアウトまでの時間は、バックエンドの正常性ページが利用可能であることを確認するために HTTP 呼び出しを実行できるだけの長さである必要があります。
* **[異常しきい値]** - 異常であると見なされるまでの試行の失敗回数です。 しきい値を 0 に設定すると、正常性チェックが失敗した場合に、バックエンドが異常であると即座に判断されます。

> [!IMPORTANT]
> ホスト名はサーバー名と同じではありません。 この値は、アプリケーション サーバーで実行されている仮想ホストの名前です。 プローブは、http://(ホスト名):(httpsetting のポート)/urlPath に送信されます。

![probe configuration settings][3]

## <a name="add-probe-to-the-gateway"></a>ゲートウェイへのプローブの追加

プローブが作成されたら、ゲートウェイに追加します。 プローブの設定は、アプリケーション ゲートウェイのバックエンド http 設定で行います。

### <a name="step-1"></a>手順 1

アプリケーション ゲートウェイの **[HTTP 設定]** をクリックし、ウィンドウで現在のバックエンド http 設定をクリックして、構成ブレードを表示します。

![https settings window][4]

### <a name="step-2"></a>手順 2.

**appGatewayBackEndHttp** 設定ブレードで、**[カスタム プローブの使用]** をクリックし、「[プローブの作成](#createprobe)」で作成したプローブを選択します。
完了したら、 **[OK]** をクリックし、設定を適用します。

![appgatewaybackend settings blade][5]

既定のプローブでは、Web アプリケーションへの既定のアクセスがチェックされます。 カスタム プローブが作成されたら、アプリケーション ゲートウェイは、定義されているカスタム パスを使用して、選択されているバックエンドの正常性を監視します。 定義された条件に基づいて、アプリケーション ゲートウェイは、プローブで指定されているファイルをチェックします。 host:Port/path への呼び出しによって HTTP 200 OK という状態の応答が返されない場合は、異常のしきい値に達した後、サーバーがローテーションから除外されます。 プローブは、もう一度正常になるタイミングを判断するために、異常なインスタンス上で続行します。 インスタンスが正常なサーバー プールに戻されると、トラフィックはもう一度そこに流れ始め、インスタンスへのプローブは、通常どおり、ユーザーが指定した間隔で続行します。

## <a name="next-steps"></a>次のステップ

Azure Application Gateway で SSL オフロードを構成する方法を学習するには、[SSL オフロードの構成](application-gateway-ssl-portal.md)に関するページをご覧ください。

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png

