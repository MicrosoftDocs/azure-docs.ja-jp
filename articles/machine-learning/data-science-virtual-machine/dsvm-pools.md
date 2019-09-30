---
title: 共有プール
titleSuffix: Azure Data Science Virtual Machine
description: DSVM のプールをチームの共有リソースとして展開します
keywords: ディープ ラーニング, AI, データ サイエンス ツール, データ サイエンス仮想マシン, 地理空間分析, チーム データ サイエンス プロセス
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 929040b5dc8650a757fb9c0da58cb82a76a72b5b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195616"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Data Science Virtual Machine の共有プールを作成する

この記事では、チームで使用する Data Science Virtual Machine (DSVM) の共有プールを作成する方法について説明します。 共有プールの使用の利点には、リソースの使用率の向上、より簡単な共有とコラボレーション、DSVM リソースの管理の効率化があります。

多くの方法とテクノロジを使って、DSVM のプールを作成することができます。 この記事では、対話型仮想マシン (VM) のプールに焦点を当てます。 代替のマネージド コンピューティング インフラストラクチャは Azure Machine Learning コンピューティングです。 詳細については、[コンピューティング ターゲットの設定](../service/how-to-set-up-training-targets.md#amlcompute)に関するページを参照してください。

## <a name="interactive-vm-pool"></a>対話型の VM プール

AI/データ サイエンス チーム全体で共有される対話型 VM のプールにより、ユーザーは、ユーザーのセットごとに専用のインスタンスを持つ代わりに DSVM の使用可能なインスタンスにログインできます。 この設定により、リソースの可用性の向上と効率的な利用が可能になります。

対話型 VM プールの作成には、[Azure 仮想マシン スケール セット](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) テクノロジを使用します。 スケール セットを使って、負荷分散と自動スケーリングが行われる同一の VM のグループを作成して管理できます。

ユーザーは、メイン プールの IP または DNS アドレスにログインします。 スケール セットは、セッションをスケール セット内の使用可能な DSVM に自動的にルーティングします。 ログインする VM に関係なく、ユーザーは一貫した、精通している環境を望むので、スケール セット内の VM のすべてのインスタンスは Azure Files 共有や NFS (Network File System) 共有などの共有ネットワーク ドライブをマウントします。 通常、ユーザーの共有ワークスペースは各インスタンスにマウントされている共有ファイル ストアに保持されます。

Ubuntu DSVM インスタンスでスケール セットを作成する Azure Resource Manager テンプレートのサンプルが [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json) にあります。 Azure Resource Manager テンプレート用の[パラメーター ファイル](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json)のサンプルも同じ場所にあります。

Azure CLI でパラメーター ファイルの値を指定することで、Azure Resource Manager テンプレートからスケール セットを作成できます。

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
上のコマンドでは次のものがあることが想定されています。
* パラメーター ファイルのコピーと、スケール セットのインスタンスに指定されている値。
* VM インスタンスの数。
* Azure ファイル共有へのポインター。
* 各 VM にマウントされるストレージ アカウントの資格情報。

パラメーター ファイルは、コマンドでローカルに参照されています。 パラメーターをインラインで渡すか、スクリプトで要求することもできます。  

上記のテンプレートは、フロントエンドのスケール セットから Ubuntu DSVM のバックエンド プールへの SSH と JupyterHub ポートも有効にします。 ユーザーは、Secure Shell (SSH) または JupyterHub 上の VM に通常の方法でログインします。 VM インスタンスは動的にスケールアップまたはスケールダウンできるので、マウントされた Azure Files 共有に状態を保存する必要があります。 同じアプローチを使用して、Windows DSVM のプールを作成できます。

[Azure ファイル共有をマウントするスクリプト](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh)も、GitHub の Azure DataScienceVM リポジトリにあります。 スクリプトは、パラメーター ファイルで指定されているマウント ポイントに Azure ファイル共有をマウントします。 またスクリプトは、初期ユーザーのホーム ディレクトリにマウントされたドライブへのソフト リンクも作成します。 Azure Files 共有内のユーザー固有の notebook ディレクトリは `$HOME/notebooks/remote` ディレクトリにソフトリンクされており、ユーザーは Jupyter ノートブックにアクセスしてそれを実行および保存できます。 VM 上に追加ユーザーを作成して各ユーザーの Jupyter ワークスペースで Azure ファイル共有をポイントするときも同じ規則を使用できます。

仮想マシン スケール セットは自動スケーリングをサポートします。 追加インスタンスを作成するとき、およびインスタンスをスケールダウンするときのルールを設定できます。 たとえば、VM がまったく使われていないときはゼロ インスタンスにスケールダウンしてクラウド ハードウェア使用コストを節約できます。 仮想マシン スケール セットのドキュメント ページには、[自動スケーリング](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)に関する詳しい手順が記載されています。

## <a name="next-steps"></a>次の手順

* [共通 ID を設定する](dsvm-common-identity.md)
* [クラウド リソースにアクセスするための資格情報を安全に保存する](dsvm-secure-access-keys.md)















