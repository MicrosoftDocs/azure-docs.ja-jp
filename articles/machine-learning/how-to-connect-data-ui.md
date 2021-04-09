---
title: Azure のストレージ サービス内のデータに接続する
titleSuffix: Azure Machine Learning
description: データストアとデータセットを作成し、Azure Machine Learning スタジオを使用して Azure のストレージ サービス内のデータに安全に接続します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.custom: how-to, data4ml
ms.openlocfilehash: 0e2230bdcf2b2f4358db4a0d600506b711f39c45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508233"
---
# <a name="connect-to-data-with-the-azure-machine-learning-studio"></a>Azure Machine Learning スタジオを使用してデータに接続する

この記事では、[Azure Machine Learning スタジオ](overview-what-is-machine-learning-studio.md)を使用してデータにアクセスする方法について説明します。 [Azure Machine Learning データストア](how-to-access-data.md)を使用して Azure 上のストレージ サービス内のデータに接続し、[Azure Machine Learning データセット](how-to-create-register-datasets.md)を使用して、ML ワークフローのタスク用にそのデータをパッケージ化します。

次の表では、データストアとデータセットを定義し、そのメリットの概要を説明しています。 

|Object|説明| メリット|   
|---|---|---|
|データストア| サブスクリプション ID やトークン認証などの接続情報を、ワークスペースに関連付けられた [Key Vault](https://azure.microsoft.com/services/key-vault/) に格納することにより、Azure 上のストレージ サービスに安全に接続します。 | 情報は安全に保存されるため: <br><br> <li> 認証資格情報または元のデータソースを危険にさらすことがありません。 <li> スクリプト内でハード コーディングする必要がなくなりました。
|データセット| データセットを作成することにより、データ ソースの場所への参照とそのメタデータのコピーを作成します。 データセットを使用することで、次が可能となります。 <br><br><li> モデルのトレーニング中にデータにアクセスする。<li> 他のユーザーとデータを共有し、共同作業を行う。<li> データ探索のために、pandas のようなオープンソース ライブラリを活用する。 | データセットは遅延評価され、データは既存の場所に残るため: <br><br><li>ストレージ内にデータのコピーが 1 つ保持されます。<li> 追加のストレージ コストは発生しません。 <li> 意図せずに元のデータ ソースを変更するリスクはありません。<li>ML ワークフローのパフォーマンスが向上します。 

Azure Machine Learning のデータ アクセス ワークフロー全体におけるデータストアとデータセットの位置付けの詳細については、[データへの安全なアクセス](concept-data.md#data-workflow)に関する記事を参照してください。

コード ファーストでのエクスペリエンスについては、次の記事を参照して、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/) を使用してください。
* [データストアを使用して Azure ストレージ サービスに接続する](how-to-access-data.md)。 
* [Azure Machine Learning データセットを作成する](how-to-create-register-datasets.md)。 

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

- [Azure Machine Learning スタジオ](https://ml.azure.com/)へのアクセス。

- Azure Machine Learning ワークスペース。 [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md)します。

    -  ワークスペースを作成すると、ワークスペースに Azure BLOB コンテナーと Azure ファイル共有がデータストアとして自動的に登録されます。 これらの名前は、それぞれ `workspaceblobstore` および `workspacefilestore` となります。 ニーズに対して BLOB ストレージで十分な場合は、`workspaceblobstore` が既定のデータストアとして設定されます。これは既に使用できるように構成されています。 それ以外の場合は、[サポートされているストレージの種類](how-to-access-data.md#matrix)のストレージ アカウントが Azure 上で必要です。
    

## <a name="create-datastores"></a>データストアの作成

データストアは、[これらの Azure Storage ソリューション](how-to-access-data.md#matrix)から作成できます。 **サポートされていないストレージ ソリューションの場合**、および ML 実験中のデータ エグレス コストを節約するためには、サポートされている Azure Storage ソリューションに [データを移行](how-to-access-data.md#move)する必要があります。 [データストアに関する詳細を参照してください](how-to-access-data.md)。 

Azure Machine Learning Studio を使用して、少ない手順で新しいデータストアを作成します。

> [!IMPORTANT]
> データ ストレージ アカウントが仮想ネットワーク内にある場合は、Studio がデータにアクセスできるようにするために、追加の構成手順が必要になります。 適切な構成手順を適用するため、[ネットワーク分離とプライバシー](how-to-enable-studio-virtual-network.md)に関する記事を参照してください。

1. [Azure Machine Learning Studio](https://ml.azure.com/) にサインインします。
1. 左側のウィンドウの **[管理]** で、 **[データストア]** を選択します。
1. **[+ 新しいデータストア]** を選択します。
1. フォームに入力し、新しいデータストアを作成して登録します。 このフォームは、選択した Azure Storage の種類と認証の種類に基づいて、インテリジェントに自動更新されます。 このフォームを設定するために必要な認証資格情報の場所については、[ストレージ アクセスとアクセス許可](#access-validation)に関するセクションを参照してください。

次の例は、**Azure BLOB データストア** を作成するときにフォームがどのように表示されるかを示しています。

![新しいデータストアのフォームに入力する](media/how-to-connect-data-ui/new-datastore-form.png)

## <a name="create-datasets"></a>データセットを作成する

データストアを作成したら、データを操作するためのデータセットを作成します。 データセットを使用すると、データを機械学習タスク (トレーニングなど) 用に遅延評価された使用可能なオブジェクトにパッケージ化できます。 [データセットの詳細情報](how-to-create-register-datasets.md).

データセットには、FileDataset と TabularDataset の 2 種類があります。 
[FileDatasets](how-to-create-register-datasets.md#filedataset) は、1 つまたは複数のファイルまたはパブリック URL への参照を作成します。 一方、[TabularDatasets](how-to-create-register-datasets.md#tabulardataset) は、データを表形式で表現します。 

次の手順とアニメーションでは、[Azure Machine Learning Studio](https://ml.azure.com) でデータセットを作成する方法を示します。

> [!Note]
> Azure Machine Learning Studio を介して作成されたデータセットは、自動的にワークスペースに登録されます。

![UI を使用してデータセットを作成する](./media/how-to-connect-data-ui/create-dataset-ui.gif)

Studio でデータセットを作成するには、次の手順を実行します。
1. [Azure Machine Learning スタジオ](https://ml.azure.com/)にサインインします。
1. 左側のウィンドウの **[アセット]** セクションで **[データセット]** を選択します。
1. **[データセットの作成]** を選択して、データセットのソースを選択します。 このソースには、ローカル ファイル、データストア、パブリック URL、または [Azure Open Datasets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md) を指定できます。
1. データセットの種類として **[表形式]** または **[ファイル]** を選択します。
1. **[次へ]** を選択して、 **[データストアとファイルの選択]** フォームを開きます。 このフォームでは、作成後にデータセットを保存する場所を選択し、データセットに使用するデータ ファイルを選択します。
    1. データが仮想ネットワーク内にある場合は、検証のスキップを有効にします。 [仮想ネットワークの分離とプライバシー](how-to-enable-studio-virtual-network.md)について理解を深める。
    1. 表形式データセットの場合は、'timeseries' 特性を指定して、データセットに対する時間に関連する操作を有効にできます。 [timeseries 特性をデータセットに追加する](how-to-monitor-datasets.md#studio-dataset)方法を確認してください。
1. **[次へ]** を選択して **[Settings and preview]/(設定とプレビュー/)** および **[Schema]\(スキーマ\)** フォームを設定します。これらのフォームはファイルの種類に基づいてインテリジェントに設定され、これらのフォームで、作成前にデータセットを構成することができます。 
1. **[次へ]** を選択して、 **[詳細の確認]** フォームを確認します。 選択内容を確認し、データセットについてのオプションのデータ プロファイルを作成します。 [データ プロファイル](#profile)についてさらに理解を深める。
1. **[作成]** を選択して、データセットの作成を完了します。

<a name="profile"></a>

### <a name="data-profile-and-preview"></a>データ プロファイルとプレビュー

データセットを作成した後、次の手順に従ってスタジオでプロファイルとプレビューを表示できることを確認します。 

1. [Azure Machine Learning スタジオ](https://ml.azure.com/)にサインインします。
1. 左側のウィンドウの **[アセット]** セクションで **[データセット]** を選択します。
1. 表示するデータセットの名前を選択します。 
1. **[Explore (探索)]** タブをクリックします。 
1. **[プレビュー]** または **[プロファイル]** タブを選択します。 

![データセット プロファイルとプレビューの表示](./media/how-to-connect-data-ui/dataset-preview-profile.gif)

データ セットが ML 対応であるかどうかを確認するために、データ セット全体で幅広い概要統計情報を取得できます。 数値以外の列の場合、最小、最大、エラー数などの基本的な統計情報だけが含まれます。 数値列の場合は、その統計学的モーメントと、推定される分位点も確認できます。 

具体的には、Azure Machine Learning データセットのデータ プロファイルには次のものが含まれます。

>[!NOTE]
> 無関係な型を持つ特徴には空白のエントリが表示されます。

|統計|説明
|------|------
|機能| 集約されている列の名前。
|プロファイル| 推論された型に基づくインライン視覚化。 たとえば、文字列、ブール値、日付には値の数が示される一方、10 進数 (数値) は近似されたヒストグラムが示されます。 これにより、データの分布を簡単に把握できます。
|型の分布| 列内の型のインライン値カウント。 Null は独自の型であるため、この視覚化は変則値または欠損値を検出するために便利です。
|Type|推論される列の型。 使用可能な値には、文字列、ブール値、日付、10 進数が含まれます。
|Min| 列の最小値。 型に固有の順序がない特徴の場合 (ブール値など)、空白のエントリが表示されます。
|Max| 列の最大値。 
|Count| 列内の欠落しているエントリと欠落していないエントリの合計数。
|Not Missing Count| 列内の欠落していないエントリの数。 空の文字列とエラーは値として扱われるため、"欠落していないカウント" には含まれません。
|分位点| データの分布感を提供する、各分位点での近似値。
|平均| 列の算術平均。
|標準偏差| この列のデータの分散または変動の尺度。
|Variance| この列のデータが平均値からどのくらい離れているかを示す尺度。 
|傾斜| この列のデータが正規分布とどのくらい異なるかを示す尺度。
|尖度| この列のデータが正規分布と比較してどのくらい裾の重い分布になっているかを示す尺度。

## <a name="storage-access-and-permissions"></a>ストレージへのアクセスとアクセス許可

Azure ストレージ サービスに安全に接続できるように、Azure Machine Learning では、対応するデータ ストレージにアクセスするためのアクセス許可が必要です。 このアクセスは、データストアの登録に使用される認証資格情報に依存します。

### <a name="virtual-network"></a>仮想ネットワーク

データ ストレージ アカウントが **仮想ネットワーク** 内にある場合、Azure Machine Learning がデータにアクセスできるようにするためには、追加の構成手順が必要になります。 データストアを作成して登録するときに適切な構成手順が適用されるように、[ネットワーク分離とプライバシー](how-to-enable-studio-virtual-network.md)に関する記事を参照してください。  

### <a name="access-validation"></a>アクセス検証

**最初のデータストアの作成と登録のプロセスの一部として**、Azure Machine Learning により、基になるストレージ サービスが存在すること、およびユーザーが指定したプリンシパル (ユーザー名、サービス プリンシパル、または SAS トークン) で指定したストレージにアクセスできることが自動的に検証されます。

**データストアの作成後**、この検証は、データストア オブジェクトが取得されるたび **ではなく**、基になるストレージ コンテナーにアクセスする必要があるメソッドに対してのみ実行されます。 たとえば、データストアからファイルをダウンロードする場合は検証が行われますが、既定のデータストアを変更するだけの場合は、検証は行われません。

基になるストレージ サービスへのアクセスを認証するには、作成するデータストアの種類に従って、アカウント キー、Shared Access Signature (SAS) トークン、またはサービス プリンシパルを指定します。 [ストレージの種類のマトリックス](how-to-access-data.md#matrix)には、各データストアの種類に対応する、サポートされている認証の種類が一覧表示されています。

アカウント キー、SAS トークン、およびサービス プリンシパルの情報は、[Azure portal](https://portal.azure.com) で確認できます。

* 認証にアカウント キーまたは SAS トークンを使用する予定の場合は、左側のウィンドウで **[ストレージ アカウント]** を選択し、登録するストレージ アカウントを選択します。
  * **[概要]** ページには、アカウント名、コンテナー、ファイル共有名などの情報が表示されます。
      1. アカウント キーの場合、 **[設定]** ペインの **[アクセス キー]** に移動します。
      1. SAS トークンの場合は、 **[設定]** ペインの **[共有アクセス署名]** に移動します。

* 認証に [サービス プリンシパル](../active-directory/develop/howto-create-service-principal-portal.md)を使用する予定の場合は、 **[アプリの登録]** に移動して、使用するアプリを選択します。
    * 対応する **[概要]** ページに、テナント ID やクライアント ID などの必要な情報が記載されています。

> [!IMPORTANT]
> * Azure Storage アカウントのアクセス キー (アカウント キーまたは SAS トークン) を変更する必要がある場合は、新しい資格情報をワークスペースおよびそれに接続されているデータストアと同期してください。 [更新された資格情報を同期する](how-to-change-storage-access-key.md)方法を参照してください。 <br> <br>
> * データストアの登録を解除し、同じ名前を使用して再登録しようとして失敗した場合は、ワークスペースの Azure キー コンテナーで、論理的な削除が有効になっていない可能性があります。 既定では、ワークスペースによって作成されたキー コンテナー インスタンスでは論理的な削除が有効になっていますが、既存のキー コンテナーを使用した場合、または 2020 年 10 月より前にワークスペースを作成した場合は、論理的な削除が有効になっていないことがあります。 論理的な削除を有効にする方法の詳細については、「[既存のキー コンテナーの論理的な削除を有効にする]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault)」を参照してください。

### <a name="permissions"></a>アクセス許可

Azure BLOB コンテナーと Azure Data Lake Gen 2 ストレージの場合は、認証資格情報に **ストレージ BLOB データ閲覧者** アクセスがあることを確認します。 [ストレージ BLOB データ閲覧者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)の詳細については、こちらを参照してください。 アカウントの SAS トークンは、既定ではアクセス許可なしに設定されます。 
* データ **読み取りアクセス** の場合、認証資格情報には、コンテナーとオブジェクトに対するリストと読み取りのアクセス許可が少なくとも必要となります。 

* データ **書き込みアクセス** の場合は、書き込みと追加のアクセス許可も必要です。

## <a name="train-with-datasets"></a>データセットを使用したトレーニング

ML モデルのトレーニングのために、機械学習の実験でデータセットを使用します。 [データセットを使ってトレーニングする方法の詳細をご覧ください](how-to-train-with-datasets.md)

## <a name="next-steps"></a>次のステップ

* [TabularDatasets と自動機械学習によるトレーニングのステップバイステップの例を確認します](tutorial-first-experiment-automated-ml.md)。

* [モデルをトレーニングします](how-to-set-up-training-targets.md)。

* データセットのトレーニングのその他の例については、[サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)を参照してください。