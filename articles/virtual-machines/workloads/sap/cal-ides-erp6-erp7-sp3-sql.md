---
title: "Microsoft Azure での SAP ERP 6.0 向け SAP IDES EHP7 SP3 のデプロイ | Microsoft Docs"
description: "Microsoft Azure での SAP ERP 6.0 向け SAP IDES EHP7 SP3 のデプロイ"
services: virtual-machines-windows
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 75e2508aa942067fb84d34cf00f3f9f953f1573e


---
# <a name="deploying-sap-ides-ehp7-sp3-for-sap-erp-60-on-microsoft-azure"></a>Microsoft Azure での SAP ERP 6.0 向け SAP IDES EHP7 SP3 のデプロイ
この記事では、SAP Cloud Appliance Library 3.0 を使用して、SQL Server および Windows OS で実行される SAP IDES を Microsoft Azure にデプロイする方法について説明します。 スクリーン ショットでは、その手順を示します。 リストにある他のソリューションのデプロイも、同様のプロセスで行うことができます。 必要なのは別のソリューションを選択することだけです。

SAP Cloud Appliance Library (SAP CAL) の作業は [ここ](https://cal.sap.com/)から開始できます。 新しい [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience)のブログもご覧ください。 

次の各スクリーンショットで、Microsoft Azure に SAP IDES をデプロイする手順を順を追って示します。 他のソリューションでもプロセスは同じです。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

最初の画像は、Microsoft Azure で利用できるすべてのソリューションを示しています。 Azure で唯一利用可能な Windows ベースの SAP IDES ソリューション (赤枠で囲まれています) を選択して、プロセスを進めます。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic2.jpg)

最初に、新しい SAP CAL アカウントを作成する必要があります。 Azure には現在 2 つの選択肢 (標準の Azure と、パートナー 21Vianet が運営する中国本土の Azure) が用意されています。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic3.jpg)

次に、Azure ポータルにある Azure サブスクリプション ID を入力します (入手方法はこのページの以降のセクションで確認できます)。 その後、Azure 管理証明書をダウンロードする必要があります。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic6.jpg)

新しい Azure ポータルの左側に "サブスクリプション" という項目が見つかります。 クリックすると、ユーザーのすべてのアクティブなサブスクリプションが表示されます。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic7.jpg)

サブスクリプションのいずれかを選択し、[管理証明書] を選択すると、新しい Azure Resource Manager モデルの "サービス プリンシパル" を使用する新しい概念の説明が表示されます。
SAP CAL はこの新しいモデルにはまだ対応していないため、管理証明書を操作するには "クラシック" モデルと以前の Azure ポータルが必要です。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic4.jpg)

これは、以前の Azure ポータルです。 管理証明書をアップロードすると、顧客サブスクリプション内で仮想マシンを作成する権限が SAP CAL に付与されます。 [サブスクリプション] タブの下に、SAP CAL ポータルで入力する必要のあるサブスクリプション ID が表示されます。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic5.jpg)

2 番目のタブで、前に SAP CAL からダウンロードした管理証明書をアップロードできます。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic8.jpg)

ダウンロードした証明書ファイルを選択するための小さなダイアログが表示されます。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic9.jpg)

証明書がアップロードされると、SAP CAl と顧客 Azure サブスクリプション間の接続を SAP CAl 内でテストできます。 接続が有効であることを示す小さなポメッセージが表示されます。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic10.jpg)

アカウントのセットアップが完了したら、デプロイするソリューションを選択し、インスタンスを 1 つ作成する必要があります。
"基本" モードでは、簡単に作成できます。 インスタンス名を入力し、Azure リージョンを選択して、ソリューションのマスター パスワードを定義します。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic11.jpg)

ソリューションのサイズと複雑さに応じた一定の時間が経過すると (推定時間が SAP CAL によって示されます)、"アクティブ" と表示され、使用する準備が整います。 この操作は非常にシンプルです。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic12.jpg)

ソリューションの詳細を見てみると、どの種類の VM がデプロイされたかがわかります。 この場合は、SAP CAL によりサイズ D12 の Azure VM が 1 つ作成されています。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic13.jpg)

Azure Portal には、SAP CAL で指定したのと同じインスタンス名で始まる仮想マシンがあります。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic14.jpg)

これで、SAP CAL ポータルの接続ボタンを使用して、ソリューションに接続できるようになりました。 ソリューションを処理するためのすべての既定の資格情報を説明するユーザー ガイドへのリンクが含まれた小さいダイアログが表示されます。
[Here](https://caldocs.hana.ondemand.com/caldocs/help/Getting_Started_Guide_IDES607MSSQL.pdf) にあります。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

別のオプションとして、Windows VM にログインして、事前構成された SAP GUI などを起動することもできます。




<!--HONumber=Nov16_HO3-->


