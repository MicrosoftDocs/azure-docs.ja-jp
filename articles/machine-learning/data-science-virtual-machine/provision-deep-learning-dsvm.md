---
title: "Azure でのディープ ラーニング データ サイエンス仮想マシンのプロビジョニング | Microsoft Docs"
description: "分析と機械学習を行うために、Azure でディープ ラーニング データ サイエンス用仮想マシンを構成および作成します。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: db1360fa54d82c50adc04194697d994925338296
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="provision-a-deep-learning-virtual-machine-on-azure"></a>Azure でディープ ラーニング 仮想マシンをプロビジョニングする 

ディープ ラーニング仮想マシン (DLVM) は、ディープ ラーニング モデルの迅速なトレーニングに GPU ベースの VM インスタンスを容易に使用できるように、一般的な[データ サイエンス仮想マシン](http://aka.ms/dsvm) (DSVM) を特別に構成したバリアントです。 ベースとして Windows 2016 または Ubuntu DSVM によってサポートされています。 DLVM は、同じコア VM イメージ、したがって DSVM で使用可能なすべての機能豊富なツールセットを共有しています。 

DLVM には、Microsoft Cognitive Toolkit、TensorFlow、Keras、Caffe2、Chainer など、人気のディープ ラーニング フレームワークの GPU エディションを含む AI 用のツール、イメージ、テキスト データを取得し前処理するためのツール、Microsoft R Server Developer Edition、Anaconda Python、Python 用および R 用 Jupyter Notebook、Python 用および R 用 IDE などのデータ サイエンス モデリングおよび開発アクティビティ用のツール、SQL データベース、およびその他のデータ サイエンスと ML のツールがいくつか含まれます。 

## <a name="create-your-deep-learning-virtual-machine"></a>ディープ ラーニング 仮想マシンを作成する
ディープ ラーニング仮想マシンのインスタンスを作成する手順を以下に示します。 

1. [Azure ポータル](https://portal.azure.com/#create/microsoft-ads.dsvm-deep-learningtoolkit
)に一覧表示されている仮想マシンに移動します。
2. 下部にある **[作成]** ボタンを選択して、ウィザードを起動します。![create-dlvm](./media/dlvm-provision-wizard.PNG)
3. DLVM を作成するために使用されるウィザードでは、この図の右側に列挙されている **4 つのステップ**それぞれで**入力**が必要です。 以下は、これらの各ステップを構成するために必要な入力項目です。
   
   1. **基本**
      
      1. **名前**: 作成するデータ サイエンス サーバーの名前です。
      2. **Select OS type for the Deep Learning VM\(ディープ ラーニング VM の OS の種類を選択\)**: Windows または Linux (Windows 2016 および Ubuntu Linux ベースの DSVM) を選択します。
      2. **ユーザー名**: 管理者アカウントのログイン ID です。
      3. **パスワード**: 管理者アカウントのパスワードです。
      4. **サブスクリプション**: 複数のサブスクリプションがある場合は、マシンが作成されて課金されるサブスクリプションを選択します。
      5. **リソース グループ**: 新しいリソース グループを作成するか、サブスクリプション内にある**空**の既存の Azure リソース グループを使用することができます。
      6. **場所**: 最適なデータ センターを選択します。 通常は、ネットワーク アクセスを最速にするために、データの大部分があるか、物理的に最も近いデータ センターを選びます。 
      
> [!NOTE]
> DLVM は Azure NC シリーズの GPU VM インスタンスでプロビジョニングされるため、GPU のある Azure 内のいずれかの場所を選択する必要があります。 現在 GPU VM がある場所は、**米国東部、米国中北部、米国中南部、米国西部 2、北ヨーロッパ、西ヨーロッパ**です。 最新の一覧については、[リージョン別の Azure 製品](https://azure.microsoft.com/en-us/regions/services/)に関するページを確認して、**コンピューティング**の **NC シリーズ**を探してください。 

   2. **設定**: 機能要件とコスト制約を満たしている、いずれかのNC シリーズ GPU 仮想マシン サイズを選択します。 VM のストレージ アカウントを作成します。  ![dlvm-settings](./media/dlvm-provision-step-2.PNG)
   
   3. **まとめ**: 入力したすべての情報が正しいことを確認します。
   5. **購入**: プロビジョニングを開始するには、**[購入]** をクリックします。 取引条件へのリンクが用意されています。 **[サイズ]** ステップで選択したサーバー サイズのコンピューティングを超える追加の課金が VM によって発生することはありません。 

> [!NOTE]
> プロビジョニングには、10 ～ 20 分くらいかかります。 プロビジョニングの状態は、Azure ポータルに表示されます。
> 


## <a name="how-to-access-the-deep-learning-virtual-machine"></a>ディープ ラーニング 仮想マシンにアクセスする方法

### <a name="windows-edition"></a>Windows エディション
VM を作成した後は、前述の **[基本]** セクションで作成した管理者アカウントの資格情報を使って、リモート デスクトップを使用できます。 

### <a name="linux-edition"></a>Linux エディション

VM を作成したら、SSH を使用してサインインできます。 テキスト シェル インターフェイスで、手順 3. の **[基本]** セクションで作成したアカウントの資格情報を使用します。 Windows クライアントでは、[Putty](http://www.putty.org) などの SSH クライアント ツールをダウンロードできます。 グラフィカル デスクトップ (X Windows System) を使用する場合は、Putty で X11 転送を使用するか、X2Go クライアントをインストールすることができます。

> [!NOTE]
> テストでは、パフォーマンスは、X11 転送よりも、X2Go クライアントの方が優れていました。 グラフィカル デスクトップ インターフェイスでは、X2Go クライアントを使用することをお勧めします。
> 
> 

#### <a name="installing-and-configuring-x2go-client"></a>X2Go クライアントのインストールと構成
Linux DLVM は、既に X2Go サーバーでプロビジョニングされており、クライアント接続を受け入れる準備ができています。 Linux VM のグラフィカル デスクトップに接続するには、クライアントで次の手順を実行します。

1. [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient)のページから、お使いのクライアント プラットフォーム向けの X2Go クライアントをダウンロードしてインストールします。    
2. X2Go クライアントを実行し、 **[New Session (新しいセッション)]**を選択します。 複数のタブがある構成ウィンドウが開きます。 次の構成パラメーターを入力します。
   * **[Session] \(セッション) タブ**:
     * **[Host (ホスト)]**: Linux データ サイエンス VM のホスト名または IP アドレス。
     * **[Login (ログイン)]**: Linux VM のユーザー名。
     * **[SSH Port] \(SSH ポート)**: 既定値の 22 のままにします。
     * **[Session Type]\(セッションの種類\)**: 値を **XFCE** に変更します。 現在、Linux DSVM では XFCE デスクトップのみをサポートしています。
   * **[Media] \(メディア) タブ**: 音声のサポートとクライアントの印刷を使用しない場合は、それらをオフにできます。
   * **[Shared folders] \(共有フォルダー)**: クライアント コンピューターのディレクトリを Linux VM にマウントする場合は、このタブで VM と共有するクライアント コンピューターのディレクトリを追加します。

SSH クライアントを使用するか、X2Go クライアントから XFCE グラフィカル デスクトップを使用して VM にサインインしたら、VM にインストールされ、構成されているツールをすぐに使い始めることができます。 XFCE では、アプリケーション メニューのショートカットとさまざまなツールのデスクトップ アイコンが表示されます。

VM が作成され、プロビジョニングされた後は、VM にインストールされて構成されたツールを使い始めることができます。 多くのツールには、スタート メニュー タイルとデスクトップ アイコンがあります。 
