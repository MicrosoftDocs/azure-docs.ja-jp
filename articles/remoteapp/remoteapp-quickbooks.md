---
title: "Azure RemoteApp で QuickBooks をデプロイする | Microsoft Docs"
description: "Azure RemoteApp で QuickBooks を共有する方法について説明します。"
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: c5d00753-77c0-4f0d-a5df-9451b46a31d3
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4015aede386913607df8a9499c0f08c414ee6959


---
# <a name="how-do-you-deploy-quickbooks-in-azure-remoteapp"></a>Azure RemoteApp で QuickBooks をデプロイする方法
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

次の情報を使用して、QuickBooks を Azure RemoteApp のアプリとして共有します。

ハイブリッドまたはクラウド コレクションで、QuickBooks 2015 Enterprise を Azure RemoteApp と共有できます。 会社のファイルは、Azure RemoteApp サーバーとは別の QuickBooks データベース サーバーを実行している VM に格納する必要があります。 会社のファイルは Azure RemoteApp イメージに格納しないでください。データの損失が発生します。 標準の Windows ネットワークキング経由でアクセス可能な QuickBooks データベース サーバーとの外部共有で QuickBooks ファイルをホストできるのは、QuickBooks Enterprise のみです。   

> [!IMPORTANT]
> 会社のファイルをホストしている QuickBooks データベース サーバーは、Azure RemoteApp コレクションと同じ VNET 内の別の VM に配置する必要があります。  
> 
> 

## <a name="steps-to-deploy-quickbooks"></a>QuickBooks のデプロイ手順
1. Azure VM を作成し、QuickBooks と QuickBooks データベース サーバーをインストールし、会社のファイルを Azure VM に格納します。  ファイアウォールの規則を適切に構成します。
2. [カスタム イメージ](remoteapp-imageoptions.md)に QuickBooks をインストールし、会社のファイルがある QuickBooks データベース サーバーをホストしている VM と同じ VNET 内に [Azure RemoteApp コレクション](remoteapp-collections.md) (クラウドまたはハイブリッド) を作成します。 
3. [発行](remoteapp-publish.md) します。
4. Azure RemoteApp がホストする QuickBooks クライアントを起動し、標準の Windows ネットワークキングを使用して QuickBooks データベース サーバーをホストするサーバーに移動し、会社のファイルを開きます。 

## <a name="documentation-references"></a>ドキュメントの参照
* QuickBooks の [サポートされる構成](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top)
* QuickBooks の [デプロイメント オプション](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/)

私が作成した Ignite のプレゼンテーション「 [Fundamentals of Microsoft Azure RemoteApp Management and Administration (Microsoft Azure RemoteApp の管理の基礎)](https://channel9.msdn.com/Events/Ignite/2015/BRK3868) 」で、1:02:45 まで早送りして QuickBooks のパートを参照してください。

## <a name="deployment-architecture"></a>デプロイメント アーキテクチャ
![QuickBooks + Azure RemoteApp のデプロイメント](./media/remoteapp-quickbooks/ra-quickbooks.png)




<!--HONumber=Nov16_HO3-->


