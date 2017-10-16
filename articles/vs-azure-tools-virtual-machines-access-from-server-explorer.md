---
title: "サーバー エクスプローラーから Azure Virtual Machines へのアクセス | Microsoft Docs"
description: "Visual Studio のサーバー エクスプローラーで Azure Virtual Machines (VM) を作成したり管理したりする方法について簡単に説明します。"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: eb3afde6-ba90-4308-9ac1-3cc29da4ede0
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/31/2017
ms.author: kraigb
ms.openlocfilehash: 75dcc603327b50718b279f3ce055663ec0bc2596
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>サーバー エクスプローラーから Azure Virtual Machines へのアクセス
Azure でホストされている仮想マシンについての情報は、Visual Studio のサーバー エクスプローラーで表示できます。

## <a name="accessing-virtual-machines-in-server-explorer"></a>サーバー エクスプローラーの仮想マシンへのアクセス
Azure でホストされている仮想マシンには、サーバー エクスプローラーでアクセスできます。 まず Azure サブスクリプションにサインインして、ご利用の Mobile Services を表示してください。 サインインするには、サーバー エクスプローラーで Azure ノードのショートカット メニューを開き、 **[Microsoft Azure への接続]**をクリックします。

### <a name="to-get-information-about-your-virtual-machines"></a>仮想マシンの情報を取得するには
1. Cloud Explorer で仮想マシンを選択し、F4 キーを押してプロパティ ウィンドウを表示します。
   
    次の表に示したのは、アクセスできるプロパティの一覧です。ただし、これらはすべて読み取り専用です。 これらのプロパティを変更するには、[Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) を使用します。
   
   | プロパティ | Description |
   | --- | --- |
   | DNS 名 |仮想マシンのインターネット アドレスを含む URL。 |
   | 環境 |仮想マシンの場合、このプロパティの値は常に [運用] です。 |
   | 名前 |仮想マシンの名前。 |
   | サイズ |仮想マシンのサイズ。使用できるメモリとディスク領域のサイズが反映されます。 詳細については、[仮想マシンのサイズ](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs)に関する記事をご覧ください。 |
   | 状態 |"開始中"、"開始"、"停止中"、"停止"、"状態を取得中" などの値があります。 "状態を取得中" と表示された場合、現在の状態は不明です。 このプロパティの値は、[Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) で使用される値とは異なります。 |
   | サブスクリプション ID |ご利用の Azure アカウントのサブスクリプション ID。 この情報を [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) に表示するには、サブスクリプションのプロパティを表示します。 |
2. エンドポイント ノードを選択し、 **[プロパティ]** ウィンドウを表示します。
3. 次の表は、エンドポイントに関してアクセスできるプロパティの説明です。これらは読み取り専用となります。 仮想マシンのエンドポイントを追加または編集するには、[Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) を使用します。 
   
   | プロパティ | Description |
   | --- | --- |
   | 名前 |エンドポイントの ID。 |
   | プライベート ポート |アプリケーションの内部ネットワーク アクセス用ポート。 |
   | プロトコル |このエンドポイントのトランスポート レイヤーで使用されるプロトコル (TCP または UDP)。 |
   | パブリック ポート |アプリケーションに外部からアクセスする際に使用するポート。 |

## <a name="next-steps"></a>次のステップ
Visual Studio での Azure ロールの使用方法の詳細については、「 [Azure ロールでのリモート デスクトップの使用](vs-azure-tools-remote-desktop-roles.md)」を参照してください。

