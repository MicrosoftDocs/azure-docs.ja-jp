---
title: Azure Lab Services を使用してビッグ データ分析を教えるためのラボを設定する | Microsoft Docs
description: Hortonworks Data Platform (HDP) の Docker デプロイを使用して、ビッグ データ分析を教えるためのラボを設定する方法について説明します。
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 5eb9cd00350c41645d4427e30a6f25a6c163358c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659898"
---
# <a name="set-up-a-lab-for-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Hortonworks Data Platform の Docker デプロイを使用してビッグ データ分析のためのラボを設定する

この記事では、ビッグ データ分析クラスを教えるためのラボを設定する方法について説明します。  この種類のクラスでは、学生は大量のデータを処理し、機械と統計の学習のアルゴリズムを適用してデータの分析情報を導き出す方法について学習します。  学生の主な目的は、ビッグデータを格納、管理、および処理するためのツールを提供する [Apache Hadoop のオープンソース ソフトウェア パッケージ](https://hadoop.apache.org/)などのデータ分析ツールを使用する方法について学習することです。

このラボでは、学生は、[Hortonworks Data Platform (HDP)](https://www.cloudera.com/products/hdp.html) と呼ばれる、[Cloudera](https://www.cloudera.com/) が提供する一般的な商用バージョンの Hadoop を使用します。  具体的には、学生は [HDP Sandbox 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) を使用します。これは、無料で提供され、学習や実験を目的としたシンプルで使いやすいバージョンのプラットフォームです。  このクラスでは、HDP Sandbox がデプロイされた Windows または Linux の仮想マシン (VM) を使用できますが、この記事では Windows の使用方法について説明します。

このラボのもう 1 つの興味深い点は、[Docker](https://www.docker.com/) コンテナーを使用してラボ VM に HDP Sandbox をデプロイするということです。  各 Docker コンテナーには、ソフトウェア アプリケーションを内部で実行するための独自の分離環境が用意されています。  概念的には、Docker コンテナーは入れ子になった VM のようなもので、[Docker Hub](https://www.docker.com/products/docker-hub) で提供されているコンテナー イメージに基づいて、さまざまなソフトウェア アプリケーションを簡単にデプロイして実行するために使用することができます。  HDP Sandbox 用の Cloudera の配置スクリプトは、Docker Hub から [HDP Sandbox 3.0.1 Docker イメージ](https://hub.docker.com/r/hortonworks/sandbox-hdp)を自動的に取得し、次の 2 つの Docker コンテナーを実行します。
  - sandbox-hdp
  - sandbox-proxy

## <a name="lab-configuration"></a>ラボの構成

このラボを設定するには、Azure サブスクリプションとラボ アカウントが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成できます。 新しいラボ アカウントの作成の詳細については、[ラボ アカウントを設定するためのチュートリアル](tutorial-setup-lab-account.md)を参照してください。  既存のラボ アカウントを使用することもできます。

### <a name="lab-account-settings"></a>ラボ アカウントの設定

以下の表に記載されているラボ アカウントの設定を有効にします。 Marketplace イメージを有効にする方法の詳細については、「[ラボ作成者が利用できる Marketplace イメージを指定する](./specify-marketplace-images.md)」を参照してください。

| ラボ アカウントの設定 | Instructions |
| ------------------- | ------------ |
|Marketplace イメージ| ラボ アカウント内で使用する Windows 10 Pro イメージを有効にします。|

### <a name="lab-settings"></a>ラボの設定

クラスルーム ラボを設定するときは、次の表の設定を使用します。  クラスルーム ラボの作成方法の詳細については、[クラスルーム ラボの設定に関するチュートリアル](tutorial-setup-classroom-lab.md)を参照してください。

| ラボの設定 | 値/説明 |
| ------------ | ------------------ |
|仮想マシンのサイズ| 中 (入れ子になった仮想化)。 この VM サイズは、リレーショナル データベース、メモリ内キャッシュ、および分析に最適です。  また、このサイズは入れ子になった仮想化もサポートしています。|  
|仮想マシン イメージ| Windows 10 Pro|

> [!NOTE] 
> Docker を使用して HDP Sandbox をデプロイするには次のものが必要であるため、[中 (入れ子になった仮想化)] を使用する必要があります。
>   - 入れ子になった仮想化を使用した Windows Hyper-V
>   - 10 GB 以上の RAM

## <a name="template-machine-configuration"></a>テンプレート マシンの構成

テンプレート マシンを設定するには、次の操作を行います。
- Docker をインストールする
- HDP Sandbox をデプロイする
- PowerShell と Windows タスク スケジューラを使用して Docker コンテナーを自動的に開始する

### <a name="install-docker"></a>Docker をインストールする

このセクションの手順は、[Docker コンテナーを使用してをデプロイするための Cloudera の手順](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)に基づいています。 

Docker コンテナーを使用するには、まずテンプレート VM に Docker Desktop をインストールする必要があります。

1. [前提条件に関するセクション](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites)の手順に従い [Docker for Windows](https://docs.docker.com/docker-for-windows/install/) をインストールします。 

    > [!IMPORTANT] 
    > **[Use Windows containers instead of Linux containers]\(Linux コンテナーの代わりに Windows コンテナーを使用する\)** 構成オプションがオフのままになっていることを確認します。

1. **[Windows Containers and Hyper-V features]\(Windows コンテナーと Hyper-V 機能\)** がオンになっていることを確認します。
   ![Windows の機能の有効化または無効化](./media/class-type-big-data-analytics/windows-hyperv-features.png)

1. [Windows のメモリ](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows)に関するセクションの手順に従って、Docker のメモリ構成を設定します。

    > [!WARNING]
    > Docker のインストール時に、 **[Use Windows containers instead of Linux containers]\(Linux コンテナーの代わりに Windows コンテナーを使用する\)** オプションを誤ってオンにした場合、メモリ構成の設定は表示されません。  これを修正するには、[Windows システム トレイの Docker アイコンをクリック](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)して、Linux コンテナーの使用に切り替えることができます。Docker Desktop メニューが開いたら、 **[Switch to Linux containers]\(Linux コンテナーに切り替える\)** を選択します。
 
### <a name="deploy-hdp-sandbox"></a>HDP Sandbox をデプロイする

このセクションでは、HDP Sandbox をデプロイし、ブラウザーを使用して HDP Sandbox にアクセスします。

1. ガイドの[前提条件に関するセクション](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites)に記載されているように、次の手順を実行するために推奨されている [Git Bash](https://gitforwindows.org/) がインストールされていることを確認します。

1. [Cloudera の Docker のデプロイおよびインストール ガイド](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)を使用して、次のセクションの手順を実行します。
   
   -    HDP Sandbox をデプロイする
   -    HDP Sandbox を検証する

    > [!WARNING] 
    > HDP の最新の .zip ファイルをダウンロードするときは、空白を含むディレクトリ パスに .zip ファイルを保存 *しない* ようにしてください。

    > [!NOTE] 
    > デプロイ中に **ドライブが共有されていない** ことを示す例外が発生した場合は、HDP の Linux コンテナーがローカルの Windows ファイルにアクセスできるように、C ドライブを Docker と共有する必要があります。  これを修正するには、[Windows システム トレイ の Docker アイコンをクリック](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)して Docker デスクトップ メニューを開き、 **[設定]** を選択します。  **[Docker 設定]** ダイアログが開いたら、 **[リソース] > [ファイル共有]** を選択し、**C** ドライブにチェックを入れます。  その後、HDP Sandbox をデプロイする手順を繰り返すことができます。

1. HDP Sandbox 用の Docker コンテナーがデプロイされて実行中になったら、ブラウザーを起動し、Cloudera の手順に従って [Sandbox のウェルカム ページ](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page)を開いて HDP ダッシュボードを起動することで、その環境にアクセスできます。

    > [!NOTE] 
    > この手順では、サンドボックス環境のローカル IP アドレスを、テンプレート VM 上のホストファイルの sandbox-hdp.hortonworks.com にあらかじめマップしていることを前提としています。  このマッピングを **行わない** 場合、`http://localhost:8080` に移動して、Sandbox のウェルカム ページにアクセスできます。

### <a name="automatically-start-docker-containers-when-students-log-in"></a>学生のログイン時に Docker コンテナーを自動的に開始する

学生に使いやすい環境を提供するために、次のことを自動的に実行する PowerShell スクリプトを使用します。
  - 学生がラボ VM を開始して接続したときに、HDP Sandbox Docker コンテナーを起動する。
  - ブラウザーを起動し、Sandbox のウェルカム ページに移動する。
また、Windows タスク スケジューラを使用して、学生が VM にログインしたときに、このスクリプトを自動的に実行します。
これを設定するには、次の手順を実行します。[ビッグ データ分析のスクリプト作成](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/)。

## <a name="cost-estimate"></a>コストの見積もり

このラボのコストを見積もるには、次の例を使用できます。

20 時間の授業が予定されていて、宿題または課題のために 10 時間のクォータが用意されている、25 人の学生のクラスの場合、ラボの価格は次のようになります:
  - 学生 25 人 * (20 + 10) 時間 * 55 ラボ ユニット * 0.01 USD/時間 = 412.50 USD

価格について詳しくは、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」をご覧ください。

## <a name="conclusion"></a>まとめ

この記事では、Docker と共にデプロイされた Hortonworks Data Platform を使用する、ビッグ データ分析クラス用のラボを作成するために必要な手順について説明しました。  このクラスの種類のセットアップは、同様のデータ分析クラスに使用できます。  このセットアップは、デプロイに Docker を使用する他の種類のクラスにも適用できます。

## <a name="next-steps"></a>次のステップ

次の手順は、すべてのラボの設定で共通です。

- [テンプレートの作成と管理](how-to-create-manage-template.md)
- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)