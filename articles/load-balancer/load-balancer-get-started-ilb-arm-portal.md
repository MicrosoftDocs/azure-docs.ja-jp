---
title: "内部ロード バランサーの作成 - Azure Portal | Microsoft Docs"
description: "Azure ポータルを使用して Resource Manager で内部ロード バランサーを作成する方法について説明します"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ac14fb9-8d14-4892-bfe6-8bc74c48ae2c
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: 8fbe9d5d04d745de51e0e41516d6c12683c98637
ms.lasthandoff: 01/24/2017

---

# <a name="create-an-internal-load-balancer-in-the-azure-portal"></a>Azure Portal での内部ロード バランサーの作成

> [!div class="op_single_selector"]
> * [[アクセス ポリシー]](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [テンプレート](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../azure-resource-manager/resource-manager-deployment-model.md)&2; 種類のデプロイメント モデルがあります。  この記事では、Resource Manager デプロイメント モデルの使用方法について取り上げていますが、最新のデプロイでは、[クラシック デプロイメント モデル](load-balancer-get-started-ilb-classic-ps.md)ではなくこのモデルをお勧めします。

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="get-started-creating-an-internal-load-balancer-using-azure-portal"></a>Azure ポータルを使用した内部ロード バランサーの作成の概要

Azure Portal で内部ロード バランサーを作成するには、次の手順を使用します。

1. ブラウザーを開いて [Azure Portal](http://portal.azure.com) に移動し、Azure アカウントでサインインします。
2. 画面の左上で、**[新規]** > **[ネットワーク]** > **[ロード バランサー]** の順にクリックします。
3. **[ロード バランサーの作成]** ブレードで、ロード バランサーの**名前**を入力します。
4. **[スキーム]** の **[内部]** をクリックします。
5. **[仮想ネットワーク]**をクリックし、ロード バランサーを作成する仮想ネットワークを選択します。

   > [!NOTE]
   > 使用する仮想ネットワークが表示されない場合は、ロード バランサーに使用する **[場所]** を確認し、それに従って変更します。

6. **[サブネット]**をクリックし、ロード バランサーを作成するサブネットを選択します。
7. **[IP アドレスの割り当て]** で、ロード バランサーの IP アドレスを固定 (静的) にするかどうかに応じて、**[動的]** または **[静的]** をクリックします。

   > [!NOTE]
   > 静的 IP アドレスの使用を選択した場合は、ロード バランサーのアドレスを指定する必要があります。

8. **[リソース グループ]** で、ロード バランサーの新しいリソース グループの名前を指定するか、または **[既存のものを選択]** をクリックして既存のリソース グループを選択します。
9. **[作成]**をクリックします。

## <a name="configure-load-balancing-rules"></a>負荷分散規則の構成

ロード バランサーを作成した後は、ロード バランサーのリソースに移動して構成します。
負荷分散規則を構成する前に、バックエンド アドレス プールとプローブを構成する必要があります。

### <a name="step-1-configure-a-back-end-pool"></a>手順 1: バックエンド プールを構成する

1. Azure Portal で、**[参照]** > **[ロード バランサー]** の順にクリックし、上で作成したロード バランサーをクリックします。
2. **[設定]** ブレードで **[バックエンド プール]** をクリックします。
3. **[バックエンド アドレス プール]** ブレードで、**[追加]** をクリックします。
4. **[バックエンド プールの追加]** ブレードで、バックエンド プールの**名前**を入力して、**[OK]** をクリックします。

### <a name="step-2-configure-a-probe"></a>手順 2: プローブを構成する

1. Azure Portal で、**[参照]** > **[ロード バランサー]** の順にクリックし、上で作成したロード バランサーをクリックします。
2. **[設定]** ブレードで **[プローブ]** をクリックします。
3. **[プローブ]** ブレードで **[追加]** をクリックします。
4. **[プローブの追加]** ブレードで、プローブの**名前**を入力します。
5. **[プロトコル]** で、**[HTTP]** (Web サイトの場合) または **[TCP]** (その他の TCP ベースのアプリケーションの場合) を選択します。
6. **[ポート]**で、プローブにアクセスするときに使用するポートを指定します。
7. (HTTP プローブの場合のみ) **[パス]** で、プローブとして使用するパスを指定します。
8. **[間隔]** で、アプリケーションをプローブする頻度を指定します。
9. **[異常しきい値]** で、バックエンド仮想マシンを異常としてマークする前に許容する試行失敗回数を指定します。
10. **[OK]** をクリックしてプローブを作成します。

### <a name="step-3-configure-load-balancing-rules"></a>手順 3: 負荷分散規則を構成する

1. Azure Portal で、**[参照]** > **[ロード バランサー]** の順にクリックし、上で作成したロード バランサーをクリックします。
2. **[設定]** ブレードで、**[負荷分散規則]** をクリックします。
3. **[負荷分散規則]** ブレードで、**[追加]** をクリックします。
4. **[負荷分散規則の追加]** ブレードで、規則の**名前**を入力します。
5. **[プロトコル]** で、**[HTTP]** (Web サイトの場合) または **[TCP]** (その他の TCP ベースのアプリケーションの場合) を選択します。
6. **[ポート]** で、クライアントが接続するロード バランサーのポートを指定します。
7. **[バックエンド ポート]**で、バックエンド プールで使用するポートを指定します (通常、ロード バランサー ポートとバックエンド ポートは同じです)。
8. **[バックエンド プール]**で、上で作成したバックエンド プールを選択します。
9. **[セッション永続化]**で、セッションを永続化する方法を選択します。
10. **[アイドル タイムアウト (分)]**で、アイドル状態のタイムアウトを指定します。
11. **[フローティング IP (ダイレクト サーバー リターン)]** で、**[無効]** または **[有効]** をクリックします。
12. **[OK]**をクリックします。

## <a name="next-steps"></a>次のステップ

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)


