---
title: "Azure Machine Learning でオンプレミスの SQL Server を使用する | Microsoft Docs"
description: "オンプレミスの SQL Server データベースのデータを使用して Azure Machine Learning で高度な分析を実行します。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 08e4610d-02b6-4071-aad7-a2340ad8e2ea
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: garye;krishnan
translationtype: Human Translation
ms.sourcegitcommit: 66fb3dc316ce25aea4dff4add5c25b7f0f56ad7a
ms.openlocfilehash: 62917bfb2124a2b8beb57e0ee38be93e8ca1c655
ms.lasthandoff: 01/31/2017


---
# <a name="perform-advanced-analytics-with-azure-machine-learning-using-data-from-an-on-premises-sql-server-database"></a>オンプレミスの SQL Server データベースのデータを使用して Azure Machine Learning で高度な分析を実行する

[!INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

多くの場合、オンプレミス データを操作する企業は、機械学習のワークロードのためにクラウドの拡張性と俊敏性という利点を活用しようと考えます。 しかし、オンプレミス データをクラウドに移動するために現在のビジネス プロセスおよびワークフローが中断されることは望みません。 Azure Machine Learning は、現在、オンプレミスの SQL Server データベースからのデータの読み取りと、そのデータによるモデルのトレーニングおよびスコア付けをサポートしています。 クラウドとオンプレミス サーバー間で、手動でデータをコピーして同期する必要がなくなりました。 代わりに、Azure Machine Learning Studio の **データのインポート** モジュールを使用すれば、トレーニングおよびスコア付けジョブのためにオンプレミス SQL Server データベースから直接読み取ることができます。

この記事では、オンプレミスの SQL サーバー データを Azure Machine Learning で受け取る方法の概要について説明します。 ワークスペース、モジュール、データセット、実験 "*など*" の Azure Machine Learning の概念を理解していることが前提となっています。

> [!NOTE]
> この機能は、無料のワークスペースでは使用できません。 Machine Learning の価格とレベルの詳細については、 [Azure Machine Learning の価格](https://azure.microsoft.com/pricing/details/machine-learning/)に関するページを参照してください。
>
>

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="install-the-microsoft-data-management-gateway"></a>Microsoft Data Management Gateway をインストールする
Azure Machine Learning でオンプレミス SQL Server データベースにアクセスするには、Microsoft Data Management Gateway をダウンロードおよびインストールする必要があります。 Machine Learning Studio でゲートウェイ接続を構成するときに、後で説明する **[Download and register data gateway (データ ゲートウェイをダウンロードして登録)]** ダイアログを使用して、ゲートウェイをダウンロードおよびインストールできます。

[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)から MSI セットアップ パッケージをダウンロードして実行し、Data Management Gateway を事前にインストールすることもできます。
コンピューターに応じて 32 ビットまたは 64 ビットを選択し、最新バージョンを使用してください。 MSI は、既存の Data Management Gateway を、すべての設定を保持しつつ最新バージョンにアップグレードするためにも使用できます。

ゲートウェイには、以下の前提条件があります。

* サポートされている Windows オペレーティング システムのバージョンは、Windows 7、Windows 8/8.1、Windows 10、Windows Server 2008 R2、Windows Server 2012、および Windows Server 2012 R2 です。
* ゲートウェイ コンピューターの推奨される最小限の構成は、2 GHz、4 コア、8 GB の RAM、80 GB のディスクです。
* ホスト コンピューターが休止状態の場合、ゲートウェイはデータ要求に応答しません。 そのため、ゲートウェイをインストールする前に、コンピューターの適切な電源プランを構成します。 コンピューターが休止状態に構成されている場合、ゲートウェイのインストール時にメッセージが表示されます。
* コピー アクティビティは特定の頻度で発生するので、コンピューターのリソース (CPU、メモリ) の使用も同じピークとアイドルのパターンに従います。 リソース使用率はまた、移動されるデータの量に大きく依存します。 複数のコピー ジョブが進行中のとき、ピーク時にはリソース使用率が上昇するのがわかります。 上記の最小限の構成は技術的には十分ですが、データ移動の特定の負荷に応じて、最小構成より多くのリソースを持つ構成が必要になる場合もあります。

Data Management Gateway のセットアップと使用の際には、以下の点を考慮します。

* 1 台のコンピューターには、Data Management Gateway のインスタンスを&1; つだけインストールできます。
* 複数のオンプレミス データ ソースに対して、1 つのゲートウェイを使用できます。
* 異なるコンピューター上の複数のゲートウェイを、同じオンプレミス データ ソースに接続することができます。
* 一度に&1; つだけのワークスペースに対して、ゲートウェイを構成します。 現在、複数のワークスペースでゲートウェイを共有することはできません。
* 1 つのワークスペースに対して複数のゲートウェイを構成することはできます。 たとえば、開発中はテスト データ ソースに接続されているゲートウェイを使用し、運用の準備ができたら運用ゲートウェイを使用したい場合があります。
* ゲートウェイはデータ ソースと同じコンピューター上に存在する必要はありませんが、 データ ソースの近くにあると、ゲートウェイがデータ ソースに接続するときの時間が短縮されます。 ゲートウェイとデータ ソースの間でリソースの競合が発生しないように、オンプレミス データ ソースをホストするコンピューターとは異なるコンピューターにゲートウェイをインストールすることをお勧めします。
* Power BI または Azure Data Factory のシナリオを提供するゲートウェイがコンピューターに既にインストールされている場合は、Azure Machine Learning 用のゲートウェイを別のコンピューターにインストールしてください。

  > [!NOTE]
  > 同じコンピューター上で Data Management Gateway と Power BI Gateway を実行することはできません。
  >
  >
* 他のデータ用に Azure ExpressRoute を使用している場合でも、Azure Machine Learning 用に Data Management Gateway を使用する必要があります。 ExpressRouteを使用する場合であっても (ファイアウォールの背後にある) オンプレミスのデータ ソースとしてデータ ソースを扱う必要があります。 Data Management Gateway を使用して Machine Learning とデータ ソースの間の接続を確立します。

インストールの前提条件、インストールの手順、およびトラブルシューティングのヒントの詳細については、「[Data Management Gateway](../data-factory/data-factory-data-management-gateway.md)」という記事を参照してください。

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>オンプレミスの SQL Server データベースから Azure Machine Learning にデータを受信する
このチュートリアルでは、Azure Machine Learning ワークスペースに Data Management Gateway をセットアップして構成し、オンプレミスの SQL Server データベースからデータを読み取ります。

> [!TIP]
> 開始する前に、`studio.azureml.net` に対するブラウザーのポップアップ ブロックを無効にしてください。 Google Chrome ブラウザーを使用している場合は、Google Chrome WebStore の [Click Once アプリ拡張機能](https://chrome.google.com/webstore/search/clickonce?_category=extensions)にあるプラグインのいずれかをダウンロードしてインストールしてください。
>
>

### <a name="step-1-create-a-gateway"></a>手順 1: ゲートウェイを作成する
最初の手順は、オンプレミス SQL データベースにアクセスするゲートウェイを作成し、セットアップすることです。

1. [Azure Machine Learning Studio](https://studio.azureml.net/Home/) にログインし、作業するワークスペースを選択します。
2. 左側の **[設定]** ブレードをクリックし、上部の **[データ ゲートウェイ]** タブをクリックします。
3. 画面の下部の **[新しいデータ ゲートウェイ]** をクリックします。

    ![新しいデータ ゲートウェイ](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. **[新しいデータ ゲートウェイ]** ダイアログ ボックスで**ゲートウェイ名**を入力し、必要に応じて**説明**を追加します。 右下隅の矢印をクリックし、構成の次の手順に進みます。

    ![ゲートウェイの名前と説明を入力](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. [Download and register data gateway (データ ゲートウェイのダウンロードと登録)] ダイアログで、ゲートウェイ登録キーをクリップボードにコピーします。

    ![データ ゲートウェイをダウンロードして登録](media/machine-learning-use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>まだ Microsoft Data Management Gateway をダウンロードしてインストールしていない場合は、 **[Download data management gateway (データ管理ゲートウェイのダウンロード)]**をクリックします。 Microsoft ダウンロード センターに移動するので、必要なゲートウェイのバージョンを選択し、それをダウンロードしてインストールします。 インストールの前提条件、インストールの手順、およびトラブルシューティングのヒントの詳細については、「 [Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](../data-factory/data-factory-move-data-between-onprem-and-cloud.md)」という記事の冒頭のセクションを参照してください。
7. ゲートウェイがインストールされると、Data Management Gateway 構成マネージャーが開き、 **[ゲートウェイの登録]** ダイアログが表示されます。 クリップボードにコピーした**ゲートウェイ登録キー**を貼り付け、**[登録]** をクリックします。
8. 既にゲートウェイがインストールされている場合は、Data Management Gateway 構成マネージャーを実行します。 **[キーの変更]** をクリックして、前の手順でクリップボードにコピーした **ゲートウェイ登録キー**を貼り付け、**[OK]** をクリックします。
9. インストールの完了時に、Microsoft Data Management Gateway 構成マネージャーの **[ゲートウェイの登録]** ダイアログが表示されます。 前の手順でクリップボードにコピーしたゲートウェイ登録キーを貼り付け、**[登録]** をクリックします。

    ![ゲートウェイを登録](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Microsoft Data Management Gateway 構成マネージャーの **[ホーム]** タブで以下の値が設定されていれば、ゲートウェイの構成は完了です。

    * **ゲートウェイ名**と**インスタンス名**がゲートウェイの名前に設定されている。
    * **[登録]** が **[登録済み]** に設定されている。
    * **[状態]** が **[開始]** に設定されている。
    * 下部のステータス バーに、緑色のチェック マークと共に "**Data Management Gateway クラウド サービスに接続済み**" と表示されている。

      ![Data Management Gateway マネージャー](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      登録が正常に完了すると、Azure Machine Learning Studio も更新されます。

    ![ゲートウェイ登録が成功](media/machine-learning-use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. **[Download and register data gateway (データ ゲートウェイのダウンロードと登録)]** ダイアログで、セットアップを完了するチェック マークをクリックします。 **[設定]** ページに、ゲートウェイの状態が "オンライン" として表示されます。 右側のウィンドウには、状態とその他の有益な情報が表示されます。

    ![ゲートウェイの設定](media/machine-learning-use-data-from-an-on-premises-sql-server/gateway-status.png)
12. Microsoft Data Management Gateway 構成マネージャーで、 **[証明書]** タブに切り替えます。 このタブで指定された証明書は、ポータルで指定したオンプレミス データ ストアの資格情報の暗号化/暗号解除に使用されます。 この証明書は、既定の証明書です。 これを、証明書管理システムにバックアップする独自の証明書に変更することをお勧めします。 代わりに独自の証明書を使用する場合は、 **[変更]** をクリックします。

    ![ゲートウェイ証明書を変更](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (省略可能) ゲートウェイの問題をトラブルシューティングするために詳細ログを有効にする場合は、Microsoft Data Management Gateway 構成マネージャーで **[診断]** タブに切り替え、**[トラブルシューティングができるように詳細なログ記録を有効にします]** オプションをオンにします。 ログ情報については、Windows イベント ビューアーで **[アプリケーションとサービス ログ]** -&gt;**[Data Management Gateway]** ノードの下を参照してください。 **[診断]** タブを使用すると、ゲートウェイを使用してオンプレミスのデータ ソースへの接続をテストすることもできます。

    ![詳細ログ記録を有効化](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

これで、Azure Machine Learning でのゲートウェイのセットアップ作業が完了しました。
オンプレミス データを使用する準備ができました。

Studio で各ワークスペースに対して複数のゲートウェイを作成し、セットアップできます。 たとえば、開発中はテスト データ ソースに接続するゲートウェイを使用し、運用データ ソース用には別のゲートウェイを使う場合があります。 Azure Machine Learning では、企業の環境に応じて、複数のゲートウェイを柔軟にセットアップできます。 現在、複数のワークスペースでゲートウェイを共有することはできず、1 台のコンピューターには&1; つのゲートウェイだけをインストールできます。 詳細については、「[Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](../data-factory/data-factory-move-data-between-onprem-and-cloud.md)」を参照してください。

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>手順 2: ゲートウェイを使用してオンプレミス データ ソースからデータを読み取る
ゲートウェイをセットアップした後は、オンプレミス SQL Server データベースからデータを入力する実験に **データのインポート** モジュールを追加できます。

1. Machine Learning Studio で **[実験]** タブを選択し、左下隅の **[+ 新規]** をクリックして、**[Blank Experiment (空白の実験)]** を選択します (または使用可能ないくつかのサンプル実験のいずれかを選択します)。
2. **データのインポート** モジュールを見つけて、実験キャンバスにドラッグします。
3. キャンバスの下にある **[名前を付けて保存]** をクリックします。 実験名として「Azure Machine Learning オンプレミス SQL Server チュートリアル」と入力し、ワークスペースを選択して **OK** チェック マークをクリックします。

   ![新しい名前で実験を保存](media/machine-learning-use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. **データのインポート** モジュールをクリックして選択し、キャンバスの右側の **[プロパティ]** ウィンドウにある **[データ ソース]** ドロップダウン リストで "オンプレミス SQL データベース" を選択します。
5. インストールして登録した **データ ゲートウェイ** を選択します。 別のゲートウェイをセットアップするには、[(add new Data Gateway…) ((新しいデータ ゲートウェイを追加...))] を選択します。

   ![データのインポート モジュールのデータ ゲートウェイを選択](media/machine-learning-use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. SQL **データベース サーバー名**と**データベース名**を、実行する SQL **データベース クエリ**と共に入力します。
7. **[ユーザー名とパスワード]** の下の **[Enter values (値の入力)]** をクリックし、データベース資格情報を入力します。 オンプレミス SQL Server の構成方法に応じて、Windows 統合認証または SQL Server 認証を使用することができます。

   ![データベースの資格情報を入力](media/machine-learning-use-data-from-an-on-premises-sql-server/database-credentials.png)

   "値が必要" というメッセージが "値が設定された" に変わり、緑色のチェック マークが付きます。 データベース情報やパスワードが変更されない限り、資格情報の入力は&1; 度だけ必要です。 Azure Machine Learning は、ゲートウェイがインストールされたときに指定された証明書を使用して、クラウド内の資格情報を暗号化します。 Azure では、オンプレミスの資格情報を暗号化せずに保存することはありません。

   ![データのインポート モジュールのプロパティ](media/machine-learning-use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. **[実行]** をクリックして、実験を実行します。

実験の実行が終了した後、**データのインポート** モジュールの出力ポートをクリックし、**[視覚化]** を選択すると、データベースからインポートしたデータを視覚化できます。

実験の開発が完了したら、モデルをデプロイし、運用可能にすることができます。 **データのインポート** モジュールで構成されているオンプレミス SQL Server データベースのデータは、Batch Execution Service を使用して読み取られ、スコア付けに使用されます。 オンプレミス データのスコア付けには Request Response Service を使用できますが、代わりに [Excel アドイン](machine-learning-excel-add-in-for-web-services.md) を使用することをお勧めします。 現時点では、 **データのエクスポート** によるオンプレミス SQL Server データベースへの書き込みは、実験でも公開済み Web サービスでもサポートされていません。

