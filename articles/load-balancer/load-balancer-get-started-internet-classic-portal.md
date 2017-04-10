---
title: "インターネットに接続するロード バランサーの作成 - Azure クラシック ポータル | Microsoft Docs"
description: "Azure クラシック ポータルを使用し、インターネットに接続するクラシック デプロイ モデルのロード バランサーを作成する方法について説明します"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: fa3e93c0-968a-472d-a17c-65665c050db2
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: a022154f5eca6de2d2dbfc1b9aa30d2ea0a7d650
ms.lasthandoff: 01/24/2017

---

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>Azure クラシック ポータルを使用したインターネットに接続するロード バランサー (クラシック) の作成の開始

> [!div class="op_single_selector"]
> * [Azure クラシック ポータル](../load-balancer/load-balancer-get-started-internet-classic-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Azure リソースを使用する前に、Azure は現在、Azure Resource Manager デプロイ モデルとクラシック デプロイ モデルの&2; 種類を備えていることを理解しておくことが重要です。 Azure リソースを使用する前に、必ず [デプロイ モデルとツール](../azure-classic-rm.md) について知識をつけておいてください。 この記事の上部にあるタブをクリックすると、さまざまなツールについてのドキュメントを参照できます。 この記事では、クラシック デプロイメント モデルについて説明します。 [Azure リソース マネージャーを使用してインターネットに接続するロード バランサーを作成する方法](load-balancer-get-started-internet-arm-ps.md)についても説明します。

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>インターネットに接続するロード バランサーを仮想マシンに設定する

クラウド サービスの仮想マシン全体でインターネットからのネットワーク トラフィックを負荷分散するには、負荷分散セットを作成する必要があります。 この手順では、既に仮想マシンが作成されていて、これらがすべて同じクラウド サービス内にあることを前提としています。

**仮想マシンの負荷分散セットを構成するには**

1. Azure クラシック ポータルで、 **[Virtual Machines]**をクリックし、負荷分散セットの仮想マシンの名前をクリックします。
2. **[エンドポイント]** をクリックし、**[追加]** をクリックします。
3. **[仮想マシンにエンドポイントを追加します]** ページで、右矢印をクリックします。
4. **[エンドポイントの詳細を指定します]** ページで、次のように指定します。

   * **[名前]**にエンドポイントの名前を入力するか、共通プロトコルにあらかじめ定義されているエンドポイントの一覧から選択します。
   * **[プロトコル]**で、エンドポイントの種類に応じて必要となるプロトコル (TCP または UDP) を選択します。
   * **[パブリック ポート] と [プライベート ポート]**に、仮想マシンで使用するポート番号を必要に応じて入力します。 仮想マシンのプライベート ポートとファイアウォール ルールを使って、アプリケーションに適した方法でトラフィックをリダイレクトすることができます。 プライベート ポートはパブリック ポートと同じにできます。 たとえば、Web (HTTP) トラフィック用のエンドポイントの場合、パブリック ポートとプライベート ポートの両方にポート 80 を割り当てることができます。

5. **[負荷分散セットの作成]**を選択し、右矢印をクリックします。
6. **[負荷分散セットの構成]** ページで、負荷分散セットの名前を入力し、Azure Load Balancer のプローブ動作の値を割り当てます。 Load Balancer はプローブを使用して、負荷分散セット内の仮想マシンが着信トラフィックを受信できるかどうかを判断します。
7. チェック マークをクリックして、負荷分散されたエンドポイントを作成します。 仮想マシンの **[エンドポイント]** ページの **[負荷分散セット名]** 列に **[はい]** が表示されます。
8. ポータルで、**[Virtual Machines]**、負荷分散セットの追加の仮想マシンの名前、**[エンドポイント]**、**[追加]** の順にクリックします。
9. **[仮想マシンにエンドポイントを追加します]** ページで、**[既存の負荷分散セットにエンドポイントを追加する]** をクリックし、負荷分散セットの名前を選択して右矢印をクリックします。
10. **[エンドポイントの詳細を指定します]** ページで、エンドポイントの名前を入力し、チェック マークをクリックします。

負荷分散セットに仮想マシンを追加する場合は、手順 8. ～ 10. を繰り返します。

## <a name="next-steps"></a>次のステップ

[内部ロード バランサーの構成の開始](load-balancer-get-started-ilb-arm-ps.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

