---
title: "Azure Marketplace 向け仮想マシン イメージを作成するための技術的な前提条件 | Microsoft Docs"
description: "他のユーザーが購入できる仮想マシン イメージを作成して Azure Marketplace にデプロイするための要件を理解します。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 63c16966-0304-4b17-a715-368a0a5ccb2c
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 8c2dbd0a79a616e291c974c16d20d423cbb25ce2


---
# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Azure Marketplace 向け仮想マシン イメージを作成するための技術的な前提条件
始める前にプロセスをよく読み、各ステップをどこで、なぜ実行するのかを理解してください。 可能な限り、会社の情報と他のデータを準備し、必要なツールをダウンロードし、技術コンポーネントを作成してから、プラン作成プロセスを開始する必要があります。 この記事を読んでよく理解してください。  

## <a name="download-needed-tools-applications"></a>必要なツールとアプリケーションをダウンロードします。
プロセスを開始する前に、次の項目を準備してください。

* 対象とするオペレーティング システムに応じて、[Azure のダウンロード](https://azure.microsoft.com/downloads/) ページから、[Azure PowerShell コマンドレット](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids)または [Linux コマンド ライン インターフェイス ツール](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409)をインストールします。
* Azure Storage エクスプローラーを CodePlex からインストールします。
* 次のリンクから、Azure 認定用の認定テスト ツールをダウンロードおよびインストールします。
  * [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913)。 この認定ツールは、Windows ベースのコンピューターで実行する必要があります。 使用可能な Windows ベースのコンピューターがない場合は、Azure で Windows ベースの VM を使用するとツールを実行できます。

## <a name="platforms-supported"></a>サポートされているプラットフォーム
Azure ベース のVM は、Windows または Linux 上で開発することができます。 Azure と互換性のある仮想ハードディスク (VHD) の作成などの発行プロセスの一部の要素では、使用されているオペレーティング システムに合わせて異なるツールや手順を使用します。  

* Linux を使用している場合は、『 [Azure Marketplace 向け仮想マシン イメージ作成ガイド](marketplace-publishing-vm-image-creation.md)』の「Azure と互換性のある VHD の作成 (Linux ベース)」セクションを参照してください。
* Windows を使用している場合は、『 [Azure Marketplace 向け仮想マシン イメージ作成ガイド](marketplace-publishing-vm-image-creation.md)』の「Azure と互換性のある VHD の作成 (Windows ベース)」セクションを参照してください。

> [!NOTE]
> Windows ベースのコンピューターにアクセスして、次の操作を行う必要があります。
> 
> * 証明書検証ツールを実行します。
> * VHD 証明書送信用の VHD Shared Access Signature URL を作成します。
> 
> 

## <a name="develop-your-vhd"></a>VHD の開発
Azure VHD は、クラウドまたはオンプレミスでも開発できます。

* クラウド ベースで開発する場合、すべての開発手順は、Azure 上の VHD でリモート実行することになります。
* オンプレミスで開発する場合、VHD をダウンロードして、オンプレミス インフラストラクチャを使用して VHD を開発する必要があります。 これを実行することは可能ですが、お勧めしません。 オンプレミスで Windows 用または SQL 用に開発する場合は、関連するオンプレミスのライセンス キーが必要になることに注意してください。 VM の作成後に SQL Server をインストールすることはできません。 また、プランは Azure ポータルからの承認済みの SQL イメージに基づく必要があります。 オンプレミスで開発する場合は、クラウドで開発する場合とは異なる手順をいくつか行わなければなりません。 関連情報については、「 [Azure Marketplace 向けのオンプレミスでの仮想マシン イメージの開発](marketplace-publishing-vm-image-creation-on-premise.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
前提条件を確認し、必要なタスクを完了したら、『 [Azure Marketplace 向け仮想マシン イメージ作成ガイド](marketplace-publishing-vm-image-creation.md)』で詳しく説明されている、仮想マシン イメージのプランの作成を始められます。

## <a name="see-also"></a>関連項目
* [概要: Azure Marketplace へのプランの発行方法](marketplace-publishing-getting-started.md)
* [Windows を実行する仮想マシンを Azure プレビュー ポータルで作成する](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md



<!--HONumber=Nov16_HO3-->


