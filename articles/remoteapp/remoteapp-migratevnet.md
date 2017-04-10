---
title: "RemoteApp VNET から Azure VNET に移行する方法の詳細 | Microsoft Docs"
description: "RemoteApp VNET から Azure VNET に移行する方法の詳細"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: baea5d29-353b-48f8-b47f-806f2163e067
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: dbc8a612d05f8456e55eb26c58cd4461ced4a521


---
# <a name="how-to-migrate-a-hybrid-collection-from-a-remoteapp-vnet-to-an-azure-vnet"></a>RemoteApp VNET から Azure VNET にハイブリッド コレクションを移行する方法
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

うれしいことに、 RemoteApp 固有の Vnet を作成せずに、ハイブリッド RemoteApp コレクションを直接既存の Azure 仮想ネットワーク (Vnet) にデプロイすることが可能になりました。 これにより、VNET の最新機能 (ExpressRoute など) を活用し、VNET にデプロイされている他の Azure サービスおよび仮想マシンにハイブリッド コレクションが直接ネットワーク アクセスできます。 (パフォーマンスが向上し、VNET-to-VNET 構成よりもセットアップが容易になります。  )

*RemoteAppVNET* という名前の RemoteApp VNET を持つ *OriginalCollection* という名前のハイブリッド RemoteApp コレクションを既に作成してあるとしましょう。 このコレクションを *AzureVNET*という名前の新しい Azure VNET に移行するには、次の手順を実行します。

1. [[管理ポータル]](http://manage.windowsazure.com/) の **[Networks]** タブで、*RemoteAppVNET* に使用しているのと同じ場所、DNS 構成、およびアドレス空間を使用して (*AzureVNET* サブネット用に少なくとも 1 つ)、*AzureVNET* という名前の VNET を作成します。
2. *AzureVNET* を構成し、*OriginalCollection* の参加先ドメインになっている Active Directory デプロイをホストするか、これにネットワーク接続するようにします。
3. **[Remoteapp]** タブで、*New Collection* という名前の RemoteApp コレクションを新規に作成します。 (**[VNet で作成]** オプションを使用し、**[簡易作成]** オプションは使用しないでください。)
4. *NewCollection* が *AzureVNET* のサブネットにデプロイされるように構成します。
5. *NewCollection* が *OriginalCollection* に使用したのと同じイメージとドメイン参加情報を使用するように構成します。
6. 数時間後にはアクティブな状態の *NewCollection* がコレクション リストに表示されます。

元のコレクションのユーザー情報を新しいコレクションに全く移行する必要がない場合は、次の手順を実行します。

1. *OriginalCollection*を削除します。
2. *RemoteAppVNET*を削除します。

これで完了です。

一方、元のコレクションのユーザー情報を新しいコレクションに移行する必要がある場合は、次の手順を実行します。

1. Azure サブスクリプション ID、元のコレクションの名前、新しいコレクションの名前を明記した電子メールを [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20user%20information%20migration) 宛に送信して、ユーザー情報を移行するように依頼してください。
2. RemoteApp チームにより、2 営業日以内に、ユーザー アクセス リスト、すべてのユーザー ドキュメント、およびユーザー設定が元のコレクションから新しいコレクションに移されます。
3. *OriginalCollection*を削除します。
4. *RemoteAppVNET*を削除します。

これで完了です。

ご質問がある場合、または特別なサポートが必要な場合は、 [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20VNET%20migration%20help)という名前のハイブリッド RemoteApp コレクションを既に作成してあるとしましょう。




<!--HONumber=Nov16_HO3-->


