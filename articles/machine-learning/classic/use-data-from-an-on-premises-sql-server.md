---
title: ML Studio (classic):オンプレミスの SQL Server - Azure
description: SQL Server データベースのデータを使用して Azure Machine Learning Studio (クラシック) で高度な分析を実行します。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 8cdf1029371e0e11c38616e7800652ca9debbba7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517401"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-a-sql-server-database"></a>SQL Server データベースを使用して Azure Machine Learning Studio (クラシック) で分析を実行する

**適用対象:** ![適用対象: ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![適用対象外: ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


多くの場合、オンプレミス データを操作する企業は、機械学習のワークロードのためにクラウドの拡張性と俊敏性という利点を活用しようと考えます。 しかし、オンプレミス データをクラウドに移動するために現在のビジネス プロセスおよびワークフローが中断されることは望みません。 Azure Machine Learning Studio (クラシック) では、現在、SQL Server データベースからのデータの読み取りと、そのデータを使用したモデルのトレーニングとスコア付けがサポートされています。 クラウドとオンプレミス サーバー間で、手動でデータをコピーして同期する必要がなくなりました。 代わりに、Azure Machine Learning Studio (クラシック) の **データのインポート** モジュールを使用すれば、トレーニングおよびスコア付けジョブのために SQL Server データベースから直接読み取ることができます。

この記事では、SQL Server データを Azure Machine Learning Studio (クラシック) で受け取る方法の概要について説明します。 ワークスペース、モジュール、データセット、実験 "*など*" の Studio (クラシック) の概念を理解していることが前提となっています。

> [!NOTE]
> この機能は、無料のワークスペースでは使用できません。 Machine Learning の価格とレベルの詳細については、 [Azure Machine Learning の価格](https://azure.microsoft.com/pricing/details/machine-learning/)に関するページを参照してください。
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Data Factory セルフホステッド統合ランタイムをインストールする
Azure Machine Learning Studio (クラシック) で SQL Server データベースにアクセスするには、Data Factory セルフホステッド統合ランタイム (旧称 Data Management Gateway) をダウンロードしてインストールする必要があります。 Machine Learning Studio (クラシック) で接続を構成するときに、後で説明する **[データ ゲートウェイをダウンロードして登録]** ダイアログを使用して、統合ランタイム (IR) をダウンロードしてインストールできます。


[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)から MSI セットアップ パッケージをダウンロードして実行することにより、IR を事前にインストールすることもできます。MSI を使うと、すべての設定を保持したまま、既存の IR を最新バージョンにアップグレードすることもできます。

Data Factory セルフホステッド統合ランタイムには、次の前提条件があります。

* Data Factory セルフホステッド統合には、.NET Framework 4.6.1 以降を含む 64 ビット オペレーティング システムが必要です。
* サポートされる Windows オペレーティング システムのバージョンは、Windows 10、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016 です。 
* IR コンピューターの推奨される最小限の構成は、2 GHz、4 コア CPU、8 GB の RAM、80 GB のディスクです。
* ホスト コンピューターが休止状態の場合、IR はデータ要求に応答しません。 そのため、IR をインストールする前に、コンピューターの適切な電源プランを構成します。 コンピューターが休止状態に構成されている場合、IR のインストール時にメッセージが表示されます。
* コピー アクティビティは特定の頻度で発生するので、コンピューターのリソース (CPU、メモリ) の使用も同じピークとアイドルのパターンに従います。 リソース使用率はまた、移動されるデータの量に大きく依存します。 複数のコピー ジョブが進行中のとき、ピーク時にはリソース使用率が上昇するのがわかります。 上記の最小限の構成は技術的には十分ですが、データ移動の特定の負荷に応じて、最小構成より多くのリソースを持つ構成が必要になる場合もあります。

Data Factory セルフホステッド統合ランタイムを設定して使用するときは、次のことを考慮してください。

* 1 台のコンピューターには、IR のインスタンスを 1 つだけインストールできます。
* 複数のオンプレミス データ ソースに対して、1 つの IR を使用できます。
* 異なるコンピューター上の複数の IR を、同じオンプレミス データ ソースに接続することができます。
* 一度に 1 つだけのワークスペースに対して、IR を構成します。 現在、複数のワークスペースで IR を共有することはできません。
* 1 つのワークスペースに対して複数の IR を構成することはできます。 たとえば、開発中はテスト データ ソースに接続されている IR を使用し、運用の準備ができたら運用 IR を使用したい場合があります。
* IR はデータ ソースと同じコンピューター上に存在する必要はありませんが、 データ ソースの近くにあると、ゲートウェイがデータ ソースに接続するときの時間が短縮されます。 IR とデータ ソースの間でリソースの競合が発生しないように、オンプレミス データ ソースをホストするコンピューターとは異なるコンピューターに IR をインストールすることをお勧めします。
* Power BI または Azure Data Factory のシナリオを提供する IR がコンピューターに既にインストールされている場合は、Azure Machine Learning Studio (クラシック) 用の IR を別のコンピューターにインストールしてください。

  > [!NOTE]
  > Data Factory セルフホステッド統合ランタイムと Power BI Gateway を同じコンピューターで実行することはできません。
  >
  >
* 他のデータ用に Azure ExpressRoute を使用している場合でも、Azure Machine Learning Studio (クラシック) 用に Data Factory セルフホステッド統合ランタイムを使用する必要があります。 ExpressRouteを使用する場合であっても (ファイアウォールの背後にある) オンプレミスのデータ ソースとしてデータ ソースを扱う必要があります。 Data Factory セルフホステッド統合ランタイムを使用して、Machine Learning とデータ ソースの間の接続を確立します。

インストールの前提条件、インストールの手順、およびトラブルシューティングのヒントの詳細については、「[Azure Data Factory の統合ランタイム](../../data-factory/concepts-integration-runtime.md)」をご覧ください。

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>SQL Server データベースから Azure Machine Learning にデータを受信する
このチュートリアルでは、Azure Machine Learning ワークスペースに Azure Data Factory Integration Runtime を設定して構成し、SQL Server データベースからデータを読み取ります。

> [!TIP]
> 開始する前に、`studio.azureml.net` に対するブラウザーのポップアップ ブロックを無効にしてください。 Google Chrome ブラウザーを使用している場合は、Google Chrome WebStore の [Click Once アプリ拡張機能](https://chrome.google.com/webstore/search/clickonce?_category=extensions)にあるプラグインのいずれかをダウンロードしてインストールしてください。
>
> [!NOTE]
> Azure Data Factory セルフホステッド統合ランタイムは、以前は Data Management Gateway と呼ばれていました。 ステップ バイ ステップ チュートリアルでは、引き続きゲートウェイと呼ばれています。  

### <a name="step-1-create-a-gateway"></a>手順 1:ゲートウェイを作成する
最初の手順は、SQL データベースにアクセスするゲートウェイを作成し、セットアップすることです。

1. [Azure Machine Learning Studio (クラシック)](https://studio.azureml.net/Home/) にログインし、作業するワークスペースを選択します。
2. 左側の **[設定]** ブレードをクリックし、上部の **[データ ゲートウェイ]** タブをクリックします。
3. 画面の下部の **[新しいデータ ゲートウェイ]** をクリックします。

    ![新しいデータ ゲートウェイ](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. **[新しいデータ ゲートウェイ]** ダイアログ ボックスで **ゲートウェイ名** を入力し、必要に応じて **説明** を追加します。 右下隅の矢印をクリックし、構成の次の手順に進みます。

    ![ゲートウェイの名前と説明を入力](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. [Download and register data gateway (データ ゲートウェイのダウンロードと登録)] ダイアログで、ゲートウェイ登録キーをクリップボードにコピーします。

    ![データ ゲートウェイをダウンロードして登録](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>まだ Microsoft Data Management Gateway をダウンロードしてインストールしていない場合は、 **[Download data management gateway (データ管理ゲートウェイのダウンロード)]** をクリックします。 Microsoft ダウンロード センターに移動するので、必要なゲートウェイのバージョンを選択し、それをダウンロードしてインストールします。 インストールの前提条件、インストールの手順、およびトラブルシューティングのヒントの詳細については、「 [Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](../../data-factory/tutorial-hybrid-copy-portal.md)」という記事の冒頭のセクションを参照してください。
7. ゲートウェイがインストールされると、Data Management Gateway 構成マネージャーが開き、 **[ゲートウェイの登録]** ダイアログが表示されます。 クリップボードにコピーした **ゲートウェイ登録キー** を貼り付け、 **[登録]** をクリックします。
8. 既にゲートウェイがインストールされている場合は、Data Management Gateway 構成マネージャーを実行します。 **[キーの変更]** をクリックして、前の手順でクリップボードにコピーした **ゲートウェイ登録キー** を貼り付け、 **[OK]** をクリックします。
9. インストールの完了時に、Microsoft Data Management Gateway 構成マネージャーの **[ゲートウェイの登録]** ダイアログが表示されます。 前の手順でクリップボードにコピーしたゲートウェイ登録キーを貼り付け、 **[登録]** をクリックします。

    ![ゲートウェイを登録](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Microsoft Data Management Gateway 構成マネージャーの **[ホーム]** タブで以下の値が設定されていれば、ゲートウェイの構成は完了です。

    * **ゲートウェイ名** と **インスタンス名** がゲートウェイの名前に設定されている。
    * **[登録]** が **[登録済み]** に設定されている。
    * **[状態]** が **[開始]** に設定されている。
    * 下部のステータス バーに、緑色のチェック マークと共に "**Data Management Gateway クラウド サービスに接続済み**" と表示されている。

      ![Data Management Gateway マネージャー](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      登録が正常に完了すると、Azure Machine Learning Studio (クラシック) も更新されます。

    ![ゲートウェイ登録が成功](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. **[Download and register data gateway (データ ゲートウェイのダウンロードと登録)]** ダイアログで、セットアップを完了するチェック マークをクリックします。 **[設定]** ページに、ゲートウェイの状態が "オンライン" として表示されます。 右側のウィンドウには、状態とその他の有益な情報が表示されます。

    ![ゲートウェイの設定](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. Microsoft Data Management Gateway 構成マネージャーで、 **[証明書]** タブに切り替えます。このタブで指定された証明書は、ポータルで指定したオンプレミス データ ストアの資格情報の暗号化/暗号解除に使用されます。 この証明書は、既定の証明書です。 これを、証明書管理システムにバックアップする独自の証明書に変更することをお勧めします。 代わりに独自の証明書を使用する場合は、 **[変更]** をクリックします。

    ![ゲートウェイ証明書を変更](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (省略可能) ゲートウェイの問題をトラブルシューティングするために詳細ログを有効にする場合は、Microsoft Data Management Gateway 構成マネージャーで **[診断]** タブに切り替え、 **[トラブルシューティングができるように詳細なログ記録を有効にします]** オプションをオンにします。 ログ情報については、Windows イベント ビューアーで **[アプリケーションとサービス ログ]**  -&gt; **[Data Management Gateway]** ノードの下を参照してください。 **[診断]** タブを使用すると、ゲートウェイを使用してオンプレミスのデータ ソースへの接続をテストすることもできます。

    ![詳細ログ記録を有効化](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

これで、Azure Machine Learning Studio (クラシック) でのゲートウェイのセットアップ作業が完了しました。
オンプレミス データを使用する準備ができました。

Studio (クラシック) で各ワークスペースに対して複数のゲートウェイを作成してセットアップできます。 たとえば、開発中はテスト データ ソースに接続するゲートウェイを使用し、運用データ ソース用には別のゲートウェイを使う場合があります。 Azure Machine Learning Studio (クラシック) では、企業の環境に応じて、複数のゲートウェイを柔軟にセットアップできます。 現在、複数のワークスペースでゲートウェイを共有することはできず、1 台のコンピューターには 1 つのゲートウェイだけをインストールできます。 詳細については、「[Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](../../data-factory/tutorial-hybrid-copy-portal.md)」を参照してください。

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>手順 2:ゲートウェイを使用してオンプレミス データ ソースからデータを読み取る
ゲートウェイをセットアップした後は、SQL Server データベースからデータを入力する実験に **データのインポート** モジュールを追加できます。

1. Machine Learning Studio (クラシック) で **[実験]** タブを選択し、左下隅の **[+ 新規]** をクリックして、 **[Blank Experiment]\(空の実験\)** を選択します (または使用可能ないくつかのサンプル実験のいずれかを選択します)。
2. **データのインポート** モジュールを見つけて、実験キャンバスにドラッグします。
3. キャンバスの下にある **[名前を付けて保存]** をクリックします。 実験名として「Azure Machine Learning Studio (クラシック) オンプレミス SQL Server チュートリアル」と入力し、ワークスペースを選択して **[OK]** チェック マークをクリックします。

   ![新しい名前で実験を保存](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. **データのインポート** モジュールをクリックして選択し、キャンバスの右側の **[プロパティ]** ウィンドウにある **[データ ソース]** ドロップダウン リストで "オンプレミス SQL データベース" を選択します。
5. インストールして登録した **データ ゲートウェイ** を選択します。 別のゲートウェイをセットアップするには、[(add new Data Gateway…) ((新しいデータ ゲートウェイを追加...))] を選択します。

   ![データのインポート モジュールのデータ ゲートウェイを選択](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. SQL **データベース サーバー名** と **データベース名** を、実行する SQL **データベース クエリ** と共に入力します。
7. **[ユーザー名とパスワード]** の下の **[Enter values (値の入力)]** をクリックし、データベース資格情報を入力します。 SQL Server の構成方法に応じて、Windows 統合認証または SQL Server 認証を使用できます。

   ![データベースの資格情報を入力](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   "値が必要" というメッセージが "値が設定された" に変わり、緑色のチェック マークが付きます。 データベース情報やパスワードが変更されない限り、資格情報の入力は 1 度だけ必要です。 Azure Machine Learning Studio (クラシック) は、ゲートウェイがインストールされたときに指定された証明書を使用して、クラウド内の資格情報を暗号化します。 Azure では、オンプレミスの資格情報を暗号化せずに保存することはありません。

   ![データのインポート モジュールのプロパティ](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. **[実行]** をクリックして、実験を実行します。

実験の実行が終了した後、**データのインポート** モジュールの出力ポートをクリックし、 **[視覚化]** を選択すると、データベースからインポートしたデータを視覚化できます。

実験の開発が完了したら、モデルをデプロイし、運用可能にすることができます。 **データのインポート** モジュールで構成されている SQL Server データベースのデータは、Batch Execution Service を使用して読み取られ、スコア付けに使用されます。 オンプレミス データのスコア付けには Request Response Service を使用できますが、代わりに [Excel アドイン](excel-add-in-for-web-services.md) を使用することをお勧めします。 現時点では、 **データのエクスポート** による SQL Server データベースへの書き込みは、実験でも公開済み Web サービスでもサポートされていません。