---
title: 仮想マシン上の MATLAB クラスター | Microsoft Docs
description: コンピューティング集中型の並列 MATLAB ワークロードを実行する MATLAB Distributed Computing Server クラスターを作成するには、Microsoft Azure Virtual Machines を使用します
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: gwallace
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: 857382c09192e71d9727a91274993091f2f903b2
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718674"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Azure VM での MATLAB Distributed Computing Server クラスターの作成
コンピューティング集中型の並列 MATLAB ワークロードを実行する 1 つ以上の MATLAB Distributed Computing Server クラスターを作成するには、Microsoft Azure Virtual Machines を使用します。 VM に MATLAB Distributed Computing Server ソフトウェアをインストールして基本イメージとして使用し、Azure のクイック スタート テンプレートまたは Azure PowerShell スクリプト ( [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)から入手可能) を使用してクラスターをデプロイおよび管理します。 デプロイ後には、クラスターに接続してワークロードを実行します。

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>MATLAB および MATLAB Distributed Computing Server について
[MATLAB](https://www.mathworks.com/products/matlab/) プラットフォームは、エンジニアリングおよび科学的な問題を解決するために最適化されています。 大規模なシミュレーションおよびデータ処理タスクを実行する MATLAB ユーザーは、MathWorks の並列コンピューティング製品のコンピューティング クラスターおよびグリッド サービスを利用して、コンピューティング集中型のワークロードを高速化できます。 [Parallel Computing Toolbox](https://www.mathworks.com/products/parallel-computing/) を使用し、アプリケーションを並列化し、マルチコア プロセッサ、GPU およびコンピューティング クラスターを利用できます。 [MATLAB Distributed Computing Server](https://www.mathworks.com/products/distriben/) を使用し、コンピューティング クラスター内の多数のコンピューターを使用できます。

Azure の仮想マシンを使用すると、対話型のジョブ、バッチ ジョブ、独立したタスクおよび通信タスクなどの並列作業を送信するために使用できる、すべてが同じメカニズムを持つ MATLAB Distributed Computing Server クラスターをオンプレミス クラスターとして作成できます。 Azure と MATLAB プラットフォームを組み合わせて使用した場合、従来のオンプレミス ハードウェアをプロビジョニングおよび使用する場合と比較して、さまざまな範囲の仮想マシン サイズ、使用するコンピューティング リソースの支払いのみを行うクラスターのオンデマンドでの作成、および大規模にモデルをテストする機能を利用できるなど多くの利点があります。  

## <a name="prerequisites"></a>前提条件
* **クライアント コンピューター** : デプロイ後の Azure と MATLAB Distributed Computing Server クラスターとの通信に、Windows ベースのクライアント コンピューターが必要です。
* **Azure PowerShell** : クライアント コンピューターにインストールする方法については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview) 」を参照してください。
* **Azure サブスクリプション** - サブスクリプションがない場合は、 [無料アカウント](https://azure.microsoft.com/free/) を数分で作成することができます。 大規模なクラスターでは、従量課金制サブスクリプションまたはその他の購入オプションを検討してください。
* **vCPU クォータ** : 大規模なクラスターまたは複数の MATLAB Distributed Computing Server クラスターをデプロイするには、vCPU クォータを増やす必要があります。 クォータを増やすには、 [オンライン カスタマー サポートに申請](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (無料) してください。
* **MATLAB の Parallel Computing Toolbox および MATLAB Distributed Computing Server ライセンス** : このスクリプトでは、すべてのライセンスで [MathWorks Hosted License Manager](https://www.mathworks.com/help/install/license-management.html) が使用されていることを想定しています。  
* **MATLAB Distributed Computing Server ソフトウェア** : クラスター VM の基本 VM イメージとして使用される VM にインストールされます。

## <a name="high-level-steps"></a>手順の概要
MATLAB Distributed Computing Server クラスターに Azure の仮想マシンを使用するには、大まかに次の手順を実行する必要があります。 詳細な手順については、 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)のクイック スタート テンプレートおよびスクリプトに付随するドキュメントを参照してください。

1. **基本 VM イメージを作成する**  

   * この VM に MATLAB Distributed Computing Server ソフトウェアをダウンロードおよびインストールします。

     > [!NOTE]
     > この処理には数時間かかりますが、使用する MATLAB のバージョンごとに一度しか行う必要がありません。   
     >
     >
2. **1 つ以上のクラスターを作成する**  

   * 提供されている PowerShell スクリプトまたは、クイック スタート テンプレートを使用し、基本 VM イメージからクラスターを作成します。   
   * 提供されている PowerShell スクリプトを使用すると、クラスターの一覧表示、一時停止、再開および削除のクラスターの管理ができます。

## <a name="cluster-configurations"></a>クラスター構成
現時点では、クラスターの作成スクリプトとテンプレートでは、1 つの MATLAB Distributed Computing Server トポロジが作成されます。 必要に応じて、各クラスターの worker VM 数を変えたり、VM のサイズを変えたりして、クラスターを 1 つ以上追加作成できます。

### <a name="matlab-client-and-cluster-in-azure"></a>Azure の MATLAB のクライアントとクラスター
MATLAB のクライアント ノード、MATLAB のジョブ スケジューラ ノード、および MATLAB Distributed Computing Server の "worker" ノードは、次の図に示すようにすべて仮想ネットワークで Azure VM として構成されます。


* クラスターを使用するには、クライアント ノードにリモート デスクトップで接続します。 クライアント ノードで、MATLAB のクライアントが実行されます。
* クライアント ノードには、すべての worker がアクセスできるファイル共有があります。
* MathWorks Hosted License Manager は、すべての MATLAB ソフトウェアのライセンスのチェックに使用されます。
* 既定では、worker VM には、vCPU あたり 1 つの MATLAB Distributed Computing Server が作成されますが、これには任意の数を指定することができます。

## <a name="use-an-azure-based-cluster"></a>Azure ベースのクラスターの使用
MATLAB Job Scheduler クラスター プロファイルを作成するには、他の種類の MATLAB Distributed Computing Server クラスターと同様に、(クライアント VM 上の) MATLAB クライアントで Cluster Profile Manager を使用します。

![クラスター プロファイル マネージャー](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>次の手順
* Azure に MATLAB Distributed Computing Server クラスターをデプロイおよび管理する方法の詳細については、テンプレートとスクリプトを含む [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) リポジトリを参照してください。
* MATLAB および MATLAB Distributed Computing Server の詳細な説明については、 [MathWorks サイト](https://www.mathworks.com/) を参照してください。
