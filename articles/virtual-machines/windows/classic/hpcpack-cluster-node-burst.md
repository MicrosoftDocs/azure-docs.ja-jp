---
title: "HPC Pack クラスターへのバースト ノードの追加 | Microsoft Docs"
description: "クラウド サービスで実行される worker ロール インスタンスを追加して、Azure の HPC Pack クラスターをオンデマンドで拡張する方法について説明します"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 24b79a8a-24ad-4002-ae76-75abc9b28c83
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 10/14/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: d27376e81f5be3d4f473ef044a84a18ac451921f
ms.lasthandoff: 03/27/2017


---
# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Azure での HPC Pack クラスターへのオンデマンドの "バースト" ノードの追加
[Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) クラスターを Azure に設定した場合、事前構成された一連のコンピューティング ノード VM を保持することなく、クラスターの容量を迅速にスケールアップまたはスケールダウンする方法が必要になる可能性があります。 この記事では、オンデマンドの "バースト" ノード (クラウド サービスで実行されている worker ロール インスタンス) をコンピューティング リソースとして Azure のヘッド ノードに追加する方法について説明します。 

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

![Burst nodes][burst]

この記事の手順を実行すれば、テストまたは概念実証のデプロイのために、クラウドベースの HPC Pack ヘッド ノード VM に Azure ノードをすばやく追加できます。 手順は基本的にオンプレミスの HPC Pack クラスターにクラウド コンピューティング能力を追加する "Azure へのバースト" の手順と同じです。 チュートリアルが必要な場合、「 [Microsoft HPC Pack を使用したハイブリッド コンピューティング クラスターのセットアップ](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)」を参照してください。 詳細なガイドと本稼働デプロイにおける考慮事項については、「 [Microsoft HPC Pack を使用した Azure へのバースト](https://technet.microsoft.com/library/gg481749.aspx)」を参照してください。

## <a name="prerequisites"></a>前提条件
* **HPC Pack ヘッド ノードを Azure VM にデプロイ済みである** -スタンドアロンのヘッド ノード VM または大規模なクラスターの一部であるヘッド ノード VM を使用することができます。 スタンドアロンのヘッド ノードを作成するには、 [Azure VM での HPC Pack ヘッド ノードのデプロイ](../../virtual-machines-windows-hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。 自動化された HPC Pack クラスター デプロイ オプションについては、「 [Microsoft HPC Pack を使用して Azure で Windows ハイ パフォーマンス コンピューティング (HPC) クラスターを作成および管理するためのオプション](../../virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。
  
  > [!TIP]
  > [HPC Pack IaaS デプロイ スクリプト](hpcpack-cluster-powershell-script.md) を使用し、Azure でクラスターを作成する場合、自動化されたデプロイに Azure バーストを追加できます。 この記事の例を参照してください。
  > 
  > 
* **Azure サブスクリプション** - Azure ノードを追加するには、ヘッド ノード VM をデプロイするための同じサブスクリプションを選択するか、別のサブスクリプションを選択します。
* **コア クォータ** - 場合によっては、コアのクォータを増やす必要があります。特に、マルチコア サイズの Azure ノードをいくつかデプロイする場合に必要となる可能性があります。 クォータを増やすには、 [オンライン カスタマー サポートに申請](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (無料) してください。

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>手順 1: Azure ノード用のクラウド サービスとストレージ アカウントを作成する
Azure クラシック ポータルまたは同様のツールを利用し、Azure ノードをデプロイするために必要な次のリソースを構成します。

* 新しい Azure クラウド サービス
* 新しい Azure ストレージ アカウント

> [!NOTE]
> サブスクリプションでは既存のクラウド サービスを再利用しないでください。 
> 
> 

**考慮事項**

* 作成する予定の Azure ノード テンプレートごとに別個のクラウド サービスを構成します。 ただし、複数のノード テンプレートに同じストレージ アカウントを使用できます。
* デプロイ用のクラウド サービスとストレージ アカウントを同じ Azure リージョンに配置することをお勧めします。

## <a name="step-2-configure-an-azure-management-certificate"></a>手順 2: Azure 管理証明書の作成
Azure ノードをコンピューティング リソースとして追加するには、ヘッド ノードに管理証明書を用意し、デプロイに使用される Azure サブスクリプションに該当証明書をアップロードする必要があります。

このシナリオの場合、HPC Pack によりヘッド ノードに自動的にインストールされ、構成される **既定の HPC Azure 管理証明書** を選択できます。 この証明書はテスト目的と概念実証のデプロイで役立ちます。 この証明書を使用するには、ヘッド ノード VM からサブスクリプションに C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer ファイルをアップロードします。 [Azure クラシック ポータル](https://manage.windowsazure.com)で証明書をアップロードするには、**[設定]**  >  **[Management Certificates (管理証明書)]** をクリックします。

管理証明書を構成するための追加オプションについては、「 [Azure バースト デプロイの Azure 管理証明書を構成するためのシナリオ](http://technet.microsoft.com/library/gg481759.aspx)」を参照してください。

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>手順 3: クラスターに Azure ノードをデプロイする
このシナリオで Azure ノードを追加し、起動する手順は、おおむね、オンプレミス ヘッド ノードで使用される手順と同じです。 詳しくは、「 [Microsoft HPC Pack で Azure ノードをデプロイするための手順](https://technet.microsoft.com/library/gg481758.aspx)」の次のセクションを参照してください。

* Azure ノード テンプレートを作成する
* Azure ノードを Windows HPC クラスターに追加する
* Azure ノードを起動 (プロビジョニング) する

ノードを追加し、起動したら、ノードを利用してクラスター ジョブを実行できます。

Azure ノードのデプロイで問題が発生した場合は、「 [Troubleshoot Deployments of Azure Nodes with Microsoft HPC Pack (Microsoft HPC Pack で Azure ノードをデプロイするときのトラブルシューティング)](http://technet.microsoft.com/library/jj159097.aspx)」をご覧ください。

## <a name="next-steps"></a>次のステップ
* バースト ノードにコンピューティング集中型インスタンス サイズを使用する場合は、「[H シリーズとコンピューティング集中型 A シリーズの VM について](../../virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」の考慮事項を参照してください。
* クラスターのワークロードに合わせて Azure コンピューティング リソースを自動的に拡大縮小する方法については、 [HPC Pack クラスターでの Azure コンピューティング リソースの自動的な拡大縮小](hpcpack-cluster-node-autogrowshrink.md)に関する記事をご覧ください。

<!--Image references-->
[burst]: ./media/hpcpack-cluster-node-burst/burst.png

