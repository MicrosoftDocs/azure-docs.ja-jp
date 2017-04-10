---
title: "サーバー エクスプローラーから Azure Virtual Machines へのアクセス | Microsoft Docs"
description: "Visual Studio のサーバー エクスプローラーで Azure Virtual Machines (VM) を作成したり管理したりする方法について簡単に説明します。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: eb3afde6-ba90-4308-9ac1-3cc29da4ede0
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 009b325054737b63d5bdaefbe005d6a613126f52


---
# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>サーバー エクスプローラーから Azure Virtual Machines へのアクセス
Azure でホストされている仮想マシンについての情報は、Visual Studio のサーバー エクスプローラーで表示できます。

## <a name="accessing-virtual-machines-in-server-explorer"></a>サーバー エクスプローラーの仮想マシンへのアクセス
Azure でホストされている仮想マシンには、サーバー エクスプローラーでアクセスできます。 まず Azure サブスクリプションにサインインして、ご利用の Mobile Services を表示してください。 サインインするには、サーバー エクスプローラーで Azure ノードのショートカット メニューを開き、 **[Microsoft Azure への接続]**をクリックします。

### <a name="to-get-information-about-your-virtual-machines"></a>仮想マシンの情報を取得するには
1. サーバー エクスプローラーで仮想マシンを選択し、F4 キーを押してプロパティ ウィンドウを表示します。
   
    次の表に示したのは、アクセスできるプロパティの一覧です。ただし、これらはすべて読み取り専用です。 これらのプロパティを変更するには、[Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)を使用します。
   
   | プロパティ | Description |
   | --- | --- |
   | DNS 名 |仮想マシンのインターネット アドレスを含む URL。 |
   | 環境 |仮想マシンの場合、このプロパティの値は常に [運用] です。 |
   | 名前 |仮想マシンの名前。 |
   | サイズ |仮想マシンのサイズ。使用できるメモリとディスク領域のサイズが反映されます。 詳細については、仮想マシンのサイズの構成方法に関するページを参照してください。 |
   | 状態 |"開始中"、"開始"、"停止中"、"停止"、"状態を取得中" などの値があります。 "状態を取得中" と表示された場合、現在の状態は不明です。 このプロパティの値は、 [Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)で使用される値とは異なります。 |
   | サブスクリプション ID |ご利用の Azure アカウントのサブスクリプション ID。 この情報を [Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885) に表示するには、サブスクリプションのプロパティを表示します。 |
2. エンドポイント ノードを選択し、 **[プロパティ]** ウィンドウを表示します。
3. 次の表は、エンドポイントに関してアクセスできるプロパティの説明です。これらは読み取り専用となります。 仮想マシンのエンドポイントを追加したり編集したりするには、[Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)を使用します。 
   
   | プロパティ | Description |
   | --- | --- |
   | 名前 |エンドポイントの ID。 |
   | プライベート ポート |アプリケーションの内部ネットワーク アクセス用ポート。 |
   | プロトコル |このエンドポイントのトランスポート レイヤーで使用されるプロトコル (TCP または UDP)。 |
   | パブリック ポート |アプリケーションに外部からアクセスする際に使用するポート。 |

## <a name="next-steps"></a>次のステップ
Visual Studio での Azure ロールの使用方法の詳細については、「 [Azure ロールでのリモート デスクトップの使用](vs-azure-tools-remote-desktop-roles.md)」を参照してください。




<!--HONumber=Nov16_HO3-->


