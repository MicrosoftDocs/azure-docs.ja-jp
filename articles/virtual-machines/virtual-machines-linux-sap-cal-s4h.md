---
title: "Azure VM に SAP S/4HANA または BW/4HANA をデプロイする | Microsoft Docs"
description: "Azure VM に SAP S/4HANA または BW/4HANA をデプロイします"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: 6f345bb816a3fd6f6fb8672b9a43a0d075bd94eb
ms.openlocfilehash: 939c051cad98590acffb6e550ca45bf5fec90d7e
ms.lasthandoff: 03/02/2017


---
# <a name="deploy-sap-s4hana-or-bw4hana-on-microsoft-azure"></a>Microsoft Azure に SAP S/4HANA または BW/4HANA をデプロイする
この記事では、SAP Cloud Appliance Library (SAP CAL) 3.0 を使用して Microsoft Azure に S/4HANA をデプロイする方法について説明します。 他の SAP HANA ベースのソリューション (BW/4HANA など) のデプロイも同様のプロセスで機能します。 別のソリューションを選択するだけです。

> [!NOTE]
SAP Cloud Appliance Library の詳細については、[サイトのホーム ページ](https://cal.sap.com/)参照してください。 [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience)のブログもご覧ください。

## <a name="step-by-step-process-to-deploy-the-solution"></a>ソリューションを配置するための段階的なプロセス

次のスクリーン ショットでは、Azure にS/4HANA をデプロイする方法を示します。 BW/4 HANA などの他のソリューションでもプロセスは同じです。

最初のスクリーン ショットは、Azure で使用できるすべての SAP CAL HANA ベースのソリューションを示しています。 **SAP S/4HANA オンプレミス エディション**は一番下にあることに注意してください。

![SAP Cloud Appliance Library の [Solutions (ソリューション)] ウィンドウ](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-1b.jpg)

最初に、新しい SAP CAL アカウントを作成します。 **[Accounts (アカウント)]** には、[Microsoft Azure] と [Azure option operated by 21Vianet (21Vianet によって動作する Azure オプション)] という 2 つのオプションがあります。 この例では、**[Microsoft Azure]** を選択します。

![SAP Cloud Appliance Library の [Accounts (アカウント)] ウィンドウ](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-2.jpg)

次に、Azure のポータルで確認できる Azure サブスクリプション ID を入力します。 その後、Azure 管理証明書をダウンロードします。

![SAP Cloud Appliance Library の [Accounts (アカウント)] ウィンドウ](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic3b.jpg)

> [!NOTE]
Azure サブスクリプション ID を確認するには、新しい Azure Portal ではなく Azure クラシック ポータルを使用する必要があります。 SAP CAL は新しいモデルにはまだ対応していないため、管理証明書を操作するにはクラシック ポータルを使用する必要があります。

次のスクリーンショットはクラシック ポータルを示しています。 **[設定]** で **[サブスクリプション]** タブを選択して、SAP CAL の [Accounts (アカウント)] ウィンドウに入力するサブスクリプション ID を確認します。

![Azure クラシック ポータルのスクリーンショット](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic4b.jpg)

**設定**で、**[管理証明書]** タブに切り替えます。 管理証明書をアップロードして、顧客サブスクリプション内に仮想マシンを作成する権限を SAP CAL に付与します。 (先ほど SAP CAL からダウンロードした管理証明書をアップロードします)。

![Azure クラシック ポータルの [管理証明書] タブのスクリーンショット](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic5.jpg)

ダウンロードした証明書ファイルを選択するための小さなダイアログ ボックスが表示されます。

![[管理証明書のアップロード] ダイアログ ボックスのスクリーン ショット](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic8.jpg)

証明書をアップロードした後、SAP CAl と Azure サブスクリプション間の接続を SAP CAl 内でテストできます。 接続が有効であることを示すポップアップ メッセージが表示されます。

![SAP Cloud Appliance Library の [Accounts (アカウント)] ウィンドウ](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic9.jpg)

次に、デプロイするソリューションを選択し、インスタンスを作成します。
インスタンス名を入力し、Azure リージョンを選択し、ソリューションのマスター パスワードを定義します。

![SAP Cloud Appliance Library の [Solutions (ソリューション)] ウィンドウ](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic10.jpg)

ソリューションのサイズと複雑さに応じた一定の時間が経過すると (推定時間が SAP CAL によって示されます)、ソリューションがアクティブであることが示され、使用する準備が整います。

![SAP Cloud Appliance Library の [Instances (インスタンス)] ウィンドウ](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic11.jpg)

ソリューションの詳細の一部 (デプロイされた VM の種類など) を確認できます。 この例では、サイズと目的が異なる&3; つのの Azure VM が作成されています。

![SAP Cloud Appliance Library の [Instances (インスタンス)] ウィンドウ](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic12.jpg)

Azure クラシック ポータルには、SAP CAL で指定したのと同じインスタンス名で始まる仮想マシンが表示されます。

![3 つの仮想マシンを表示している Azure クラシック ポータルのスクリーン ショット](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic13.jpg)

これで、SAP CAL ポータルの接続ボタンを使用して、ソリューションに接続できます。 ソリューションで機能するすべての既定の資格情報を説明するユーザー ガイドへのリンクが含まれたダイアログ ボックスが表示されます。

![[Connect to the Instance (インスタンスに接続)] ダイアログ ボックスのスクリーン ショット](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic14b.jpg)

別のオプションとして、クライアント Windows VM にログインして、事前構成された SAP GUI などを起動することもできます。

![事前構成された SAP GUI のスクリーン ショット](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic15.jpg)

