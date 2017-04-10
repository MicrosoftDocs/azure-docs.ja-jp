---
title: "Azure Virtual Network (クラシック) の構成 - ネットワーク構成ファイル | Microsoft Docs"
description: "Azure Portal (クラシック) を使用してネットワーク構成ファイルをエクスポート、変更、およびインポートすることで、仮想ネットワーク (クラシック) を変更する方法を説明します。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 3827dc0958c51fa0c4ecb1a2e8e3b7bbed42a75a
ms.lasthandoff: 02/28/2017


---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>ネットワーク構成ファイルを使用した仮想ネットワーク (クラシック) の構成
Azure Portal (クラシック) を使用するか、またはネットワーク構成ファイルを使用して、仮想ネットワーク (クラシック) を構成できます。 ネットワーク構成ファイルを使用して、Azure Resource Manager デプロイメント モデルを介して仮想ネットワークを作成または変更することはできません。 また、Azure Portal を使用して、仮想ネットワーク (クラシック) を作成または変更することもできません。

## <a name="creating-and-modifying-a-network-configuration-file"></a>ネットワーク構成ファイルの作成および変更
ネットワーク構成ファイルを作成する最も簡単な方法は、既存の仮想ネットワーク (クラシック) 構成からネットワーク設定をエクスポートし、仮想ネットワーク用の構成設定を含むようにそのファイルに変更を加えることです。

ネットワーク構成ファイルを編集するには、単にファイルを開き、適切な変更を加えてから保存します。 ネットワーク構成ファイルは、任意の *xml* エディターを使用して変更できます。 

[ネットワーク構成ファイルのスキーマ設定](https://msdn.microsoft.com/library/azure/jj157100.aspx)のガイダンスに厳密に従う必要があります。 

Azure では、何かがデプロイされているサブネットは **使用中**と見なされます。 使用中のサブネットは変更することができません。 サブネットを変更する前に、そのサブネットにデプロイされているすべてのものを、変更対象ではない別のサブネットに移動してください。   「 [VM またはロール インスタンスを異なるサブネットに移動する](virtual-networks-move-vm-role-to-subnet.md)」をご覧ください。

## <a name="export-and-import-virtual-network-settings-using-the-azure-portal-classic"></a>Azure Portal (クラシック) を使用した仮想ネットワークの設定のエクスポートおよびインポート
PowerShell または管理ポータルを使用して、ネットワーク構成ファイルに含まれるネットワーク構成の設定をエクスポートおよびインポートすることができます。 ここでは、管理ポータルを使用してエクスポートおよびインポートする方法について説明します。 

### <a name="to-export-your-network-settings"></a>ネットワーク設定をエクスポートするには
エクスポートすると、サブスクリプションの仮想ネットワークのすべての設定が .xml ファイルに書き込まれます。 

1. [Azure Portal ポータル (クラシック)](https://manage.windowsazure.com/) にログインします。
2. ポータルで、**[ネットワーク]** ページの下部にある **[エクスポート]** をクリックします。 
3. **[ネットワーク構成をエクスポートする]** ウィンドウで、ネットワークの設定をエクスポートするサブスクリプションを選択していることを確認します。 次に、右下にあるチェックマークをクリックします。 
4. メッセージが表示されたら、 *NetworkConfig.xml* ファイルを任意の場所に保存します。

### <a name="to-import-your-network-settings"></a>ネットワーク設定をインポートするには
1. ポータルの左下にあるナビゲーション ウィンドウで、**[新規]** をクリックします。
2. **[ネットワーク サービス]** -> **[仮想ネットワーク]** -> **[構成のインポート]** の順にクリックします。
3. **[ネットワーク構成ファイルをインポートする]** ページで、ネットワーク構成ファイルを参照して、**[次へ]** 矢印をクリックします。
4. **[ネットワークを構築しています]** ページに、変更または作成されるネットワーク構成のセクションを示す情報が表示されます。 変更が適切であることを確認して、チェック マークをクリックして仮想ネットワークの更新または作成処理に進みます。 


