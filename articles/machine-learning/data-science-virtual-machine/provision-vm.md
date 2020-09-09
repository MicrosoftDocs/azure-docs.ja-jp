---
title: クイック スタート:Windows Data Science Virtual Machine を作成する
titleSuffix: Azure Data Science Virtual Machine
description: 分析と機械学習を行うために、Azure でデータ サイエンス用仮想マシンを構成および作成します。
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: a955f035933955b88200e49e343f7dd0c257c89b
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816356"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>クイック スタート:Windows 用データ サイエンス仮想マシンを設定する

Windows Server 2019 Data Science Virtual Machine を起動して実行します。

## <a name="prerequisite"></a>前提条件

Windows Data Science Virtual Machine を作成するには、Azure サブスクリプションが必要です。 [Azure を無料で試す](https://azure.com/free)。
Azure 無料アカウントでは、GPU 対応の仮想マシン SKU がサポートされないので注意してください。

## <a name="create-your-dsvm"></a>DSVM を作成する

DSVM インスタンスを作成するには:

1. [Azure portal](https://portal.azure.com) にアクセスします。まだサインインしていない場合は、Azure アカウントへのサインインを求めるメッセージが表示される可能性があります。
1. 「data science virtual machine」と入力して仮想マシンの一覧を検索し、[Data Science Virtual Machine - Windows 2019] を選択します。

1. 下部にある **[作成]** ボタンを選択します。

1. [仮想マシンの作成] ブレードにリダイレクトされます。

1. **[基本]** タブに次のように入力します。
      * **サブスクリプション**:複数のサブスクリプションがある場合は、マシンが作成されて課金されるサブスクリプションを選択します。 そのサブスクリプションに対するリソース作成権限が必要です。
      * **[リソース グループ]** :新しいグループを作成するか、既存のグループを使用します。
      * **[仮想マシン名]** : 仮想マシンの名前を入力します。 これは Azure portal に表示される名前です。
      * **[場所]** :最適なデータ センターを選択します。 ネットワーク アクセスを最速にするために、データの大半が存在するデータセンターか、物理的に最も近くにあるデータセンターを選びます。 詳細については、[Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)に関する記事を参照してください。
      * **イメージ**:既定値をそのまま使用します。
      * **Size**:これには、一般的なワークロードに適したサイズが自動的に設定されます。 詳細については、[Azure の Windows VM のサイズ](../../virtual-machines/windows/sizes.md)に関する記事を参照してください。
      * **[ユーザー名]** : 管理者のユーザー名を入力します。 これは、仮想マシンへのログインに使用するユーザー名であり、Azure ユーザー名と同じにする必要はありません。
      * **パスワード**:仮想マシンへのログインに使用するパスワードを入力します。    
1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. **[レビュー + 作成]**
   * 入力したすべての情報が正しいことを確認します。 
   * **［作成］** を選択します


> [!NOTE]
> * 仮想マシンに事前に読み込まれているソフトウェアのライセンス料金はかかりません。 **[サイズ]** の手順で選択したサーバー サイズのコンピューティング コストがかかります。
> * プロビジョニングには 10 から 20 分かかります。 Azure portal で VM の状態を表示できます。

## <a name="access-the-dsvm"></a>DSVM にアクセスする

VM の作成とプロビジョニングが完了したら、「[Azure ベースの仮想マシンに接続する](../../marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm.md)」の手順を実行します。 仮想マシン作成の **[基本]** ステップで構成した、管理者アカウントの資格情報を使用します。 

VM にインストールされて構成されたツールを使い始めることができます。 多くのツールには、**スタート** メニューのタイルとデスクトップ アイコンからアクセスできます。

また、DSVM を Azure Notebooks に接続して、VM で Jupyter Notebook を実行し、無料のサービス レベルの制限をバイパスすることもできます。 詳細については、[Notebooks プロジェクトの管理と構成](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects)に関するページを参照してください。

<a name="tools"></a>


## <a name="next-steps"></a>次のステップ

* **[スタート]** メニューを開いて DSVM 上のツールを調べます。
* Azure Machine Learning については、「[Azure Machine Learning とは](../overview-what-is-azure-ml.md)」を参照し、[チュートリアル](../index.yml)をお試しください。
* [Azure での Windows Data Science Virtual Machine を使用したデータ サイエンス](./vm-do-ten-things.md)に関する記事を参照してください。

