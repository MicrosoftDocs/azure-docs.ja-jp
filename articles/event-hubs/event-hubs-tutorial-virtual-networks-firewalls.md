---
title: 'チュートリアル: Event Hubs で仮想ネットワークの統合とファイアウォールを有効にする | Microsoft Docs'
description: このチュートリアルでは、Event Hubs と仮想ネットワークおよびファイアウォールを統合して、セキュリティで保護されたアクセスを有効にする方法について説明します。
services: event-hubs
author: axisc
manager: darosa
ms.author: aschhab
ms.date: 11/28/2018
ms.topic: tutorial
ms.service: event-hubs
ms.custom: mvc
ms.openlocfilehash: 0f7c7e348c154aab1deb10273346a5395599b745
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605857"
---
# <a name="tutorial-enable-virtual-networks-integration-and-firewalls-on-event-hubs-namespace"></a>チュートリアル: Event Hubs 名前空間で仮想ネットワークの統合とファイアウォールを有効にする

[仮想ネットワーク (VNet) サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)では、直接接続によって、仮想ネットワークのプライベート アドレス空間と VNet の ID が Azure サービスまで拡張されます。 エンドポイントを使用することで、重要な Azure サービス リソースへのアクセスを仮想ネットワークのみに限定することができます。 VNet から Azure サービスへのトラフィックは常に、Microsoft Azure のバックボーン ネットワーク上に残ります。

ファイアウォールを使用すると、Event Hubs 名前空間へのアクセスを、特定の IP アドレス (または IP アドレス範囲) に制限できます。

このチュートリアルでは、ポータルを使用して、既存の Azure Event Hubs 名前空間で仮想ネットワーク サービス エンドポイントを統合してファイアウォール (IP フィルタリング) を設定する方法を示します。

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * 仮想ネットワーク サービス エンドポイントと Event Hubs 名前空間を統合する方法。
> * Event Hubs 名前空間でファイアウォール (IP フィルタリング) を設定する方法。

>[!WARNING]
> 仮想ネットワーク統合を実装すると、他の Azure サービスが Event Hubs と対話するのを防ぐことができます。
>
> 仮想ネットワークが有効になっているときはファースト パーティ統合はサポートされません。
> 仮想ネットワークでは動作しない Azure の一般的なシナリオは次のとおりです。
> * Azure Diagnostics とログ記録
> * Azure Stream Analytics
> * Event Grid の統合
> * Web アプリと関数は仮想ネットワーク上に存在する必要があります。
> * IoT Hub ルート
> * IoT Device Explorer


> [!IMPORTANT]
> 仮想ネットワークは、**Standard** レベルと **Dedicated** レベルの Event Hubs でサポートされます。 Basic レベルではサポートされません。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント][] を作成してください。

## <a name="prerequisites"></a>前提条件

ここでは既存の Event Hubs 名前空間を使うので、使用可能な Event Hubs 名前空間があることを確認してください。 ない場合は、[こちらのチュートリアル](./event-hubs-create.md)をご覧ください

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

まず [Azure portal][Azure portal] に移動し、ご利用の Azure サブスクリプションを使用してサインインします。

## <a name="select-event-hubs-namespace"></a>Event Hubs 名前空間を選択する

このチュートリアルのための Event Hubs 名前空間を作成してあるので、それに移動します。

## <a name="navigate-to-firewalls-and-virtual-networks-experience"></a>ファイアウォールと仮想ネットワークのエクスペリエンスに移動する

ポータルの左側のウィンドウのナビゲーション メニューで、 **[ファイアウォールと仮想ネットワーク]** オプションを選択します。

  ![メニューに移動する](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-landing-page.png)

  初めてこのページに移動したときは、 **[すべてのネットワーク]** オプション ボタンが選択されているはずです。 これは、その Event Hubs 名前空間ですべての着信接続が許可されることを意味します。

## <a name="add-virtual-network-service-endpoint"></a>仮想ネットワーク サービス エンドポイントを追加する

アクセスを制限するには、この Event Hubs 名前空間に対して仮想ネットワーク サービス エンドポイントを統合する必要があります。

1. ページの上部にある **[選択されたネットワーク]** オプション ボタンをクリックして、メニュー オプションが含まれるページの残りの部分を有効にします。
  ![選択されたネットワーク](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. ページの [仮想ネットワーク] セクションで、***[+ 既存の仮想ネットワークを追加]*** オプションを選択します。 これによりスライドして表示されるウィンドウでは、作成済みの仮想ネットワークを選択できます。
  ![既存の仮想ネットワークを追加する](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane.png)
3. 一覧から仮想ネットワークを選択し、サブネットを選択します。
   ![サブネットを選択する](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-with-subnet-query.png)
4. 仮想ネットワークを一覧に追加する前に、サービス エンドポイントを有効にする必要があります。 サービス エンドポイントが有効になっていない場合は、有効にするように求められます。
  ![サブネットを選択してエンドポイントを有効にする](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-enabling.png)
    > [!NOTE]
    > サービス エンドポイントを有効にできない場合は、ARM テンプレートを使用して不足している仮想ネットワーク サービス エンドポイントを無視することができます。 この機能はポータルでは使用できません。

5. 選択したサブネットでサービス エンドポイントを有効にした後は、許可された仮想ネットワークの一覧にそれを追加できます。
  ![エンドポイントを有効にした後でサブネットを追加する](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-adding.png)

6. 上部のリボンの **[保存]** ボタンをクリックして、サービスに仮想ネットワークの構成を保存します。 ポータルの通知に確認が表示されるまで、数分間お待ちください。

## <a name="add-firewall-for-specified-ip"></a>指定した IP に対するファイアウォールを追加する

ファイアウォール規則を使用して、Event Hubs 名前空間へのアクセスを、限られた範囲の IP アドレスまたは特定の IP アドレスに制限できます。

1. ページの上部にある **[選択されたネットワーク]** オプション ボタンをクリックして、メニュー オプションが含まれるページの残りの部分を有効にします。
  ![選択されたネットワーク](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. **[ファイアウォール]** セクションの ***[アドレス範囲]*** グリッドでは、1 つ以上の特定の IP アドレスまたは IP アドレスの範囲を追加することができます。
  ![IP アドレスを追加する](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall.png)
3. 複数の IP アドレス (または IP アドレスの範囲) を追加した後は、上部のリボンの **[保存]** をクリックして、確実に構成がサービス側に保存されるようにします。 ポータルの通知に確認が表示されるまで、数分間お待ちください。
  ![IP アドレスを追加して [保存] をクリックする](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall-hitting-save.png)

## <a name="adding-your-current-ip-address-to-the-firewall-rules"></a>現在の IP アドレスをファイアウォール規則に追加する

1. ***[アドレス範囲]*** グリッドのすぐ上にある ***[クライアント IP アドレス ('<現在の IP アドレス>') の追加]*** チェック ボックスをオンにすることで、現在の IP アドレスをすぐに追加することもできます。
  ![現在の IP アドレスの追加](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save.png)
2. 現在の IP アドレスをファイアウォール規則に追加したら、上部のリボンの **[保存]** をクリックして、確実に構成がサービス側に保存されるようにします。 ポータルの通知に確認が表示されるまで、数分間お待ちください。
  ![現在の IP アドレスを追加して [保存] をクリックする](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save-after-saving.png)

## <a name="conclusion"></a>まとめ

このチュートリアルでは、仮想ネットワーク エンドポイントとファイアウォール規則を、既存の Event Hubs 名前空間と統合しました。 次の方法を学習しました。
> [!div class="checklist"]
> * 仮想ネットワーク サービス エンドポイントと Event Hubs 名前空間を統合する方法。
> * Event Hubs 名前空間でファイアウォール (IP フィルタリング) を設定する方法。


[Azure portal]: https://portal.azure.com/
