---
title: Micro Focus Enterprise Server 5.0 を AKS にデプロイする |Microsoft Docs
description: Azure 仮想マシン (VM) 上で Micro Focus 開発およびテスト環境を使用して IBM z/OS メインフレーム ワークロードをリホストします。
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 58ba530e434a9dc141ba8cb98120f6325eb06f7e
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950475"
---
# <a name="deploy-micro-focus-enterprise-server-50-to-aks"></a>Micro Focus Enterprise Server 5.0 を AKS にデプロイする

別の[アーティクル](./run-enterprise-server-container.md)では、Docker コンテナーで Micro Focus Enterprise Server 5.0 を実行する手順について説明しました。 それに続くものとして、さらに Azure Kubernetes Service (AKS) に作成した Docker イメージをデプロイする方法について説明します。

Azure Kubernetes Service は、Kubernetes に基づく管理されたオーケストレーション サービスです。 これにより、コンテナー ホストのクラスター全体で Docker コンテナー (およびその他のコンテナー ベースのアプリケーション) をデプロイ、拡張、および管理することができます。

これは 3 段階のプロセスです。 以下を実行する必要があります。

1.  Docker イメージを格納する Azure Container Registry を作成します。

2.  Azure Kubernetes クラスターを作成して、Docker イメージを実行します。

3.  アプリケーションを実行します。

これにより、Azure でのメインフレームの最新化ワークロードのスケールアウト (およびスケールダウン) が可能になり、クラウド プラットフォームの真の利点を活用できます。

準備はいいですか。 それでは始めましょう。

## <a name="create-the-azure-container-registry"></a>Azure Container Registry を作成する

Azure portal で、左上隅にある **[リソースの作成]** を選択します。 Marketplace のダッシュボードで、 **[コンテナー]** を選択し、次に **[Container Registry]** を選択します。 これにより、 **[コンテナー レジストリの作成]** ウィンドウが表示されます。ここで、**レジストリ名**、**Azure サブスクリプション**、**リソース グループ**、**場所** を入力する必要があります。 **レジストリ名** は解決する必要があるため、一意である必要があります。 前のブログ投稿で使用した **リソース グループ** と、それに対応する **場所** を選択します。 **管理者ユーザー** に対して **[有効にする]** を選択し、**SKU** には **[Basic]** を選択します。 すべての情報を入力したら、 **[作成する]** を選択 ます。

![コンテナー レジストリ インターフェイスの作成](media/deploy-image-1.png)

レジストリがデプロイされたら、 **[リソースに移動]** を選択します。 これにより、Azure Container Registry のメイン ブレードが表示されます。 便利な機能として、 **[クイック スタート]** メニュー オプションがあります。 これを選択すると、レジストリに対してイメージのプッシュおよびプルを行うために必要な手順が表示されます。 確認しましょう。

1.  **Docker のインストール** – これは既に完了しているため、心配する必要はありません。

2.  **"hello – world" 基本イメージの実行** - こちらも必要ありませんが、試してみることができます。

3.  **コンテナー レジストリへのログイン** - これは、仮想マシン (VM) から実行する必要があります。 コマンドをクリップボードまたはメモ帳にコピーします。

    作成したレジストリのコマンドは次のようになります。 **docker login acrmf50.azurecr.io**

4.  **レジストリにプッシュする** - これは、VM にログインした後、Micro Focus イメージに対しても実行する必要があります。

5.  **レジストリからプルする** - このチュートリアルには関係ありませんが、別の Docker イメージを実行する必要があるかどうかを把握しておくことをお勧めします。

ポータルから移動する前に、ログインできるように、レジストリの資格情報を取得する必要があります。 **[クイック スタート]** ブレードを終了し、[レジストリ] メニューから **[アクセス キー]** を選択します。 **ユーザー名** と **パスワード** の 1 つ (2 つあります) をクリップボードまたはメモ帳にコピーします。 この情報は後でログインするために必要になります。

実行する必要があることを確認したので、VM にログインします。

## <a name="rdp-to-the-virtual-machine-you-used-to-create-the-docker-image"></a>Docker イメージの作成に使用した仮想マシンへの RDP

Windows 2016 サーバーに Docker イメージを既に作成しているため、その VM にログインする必要があります。 この VM から、先ほど作成した Azure Container Registry にイメージをプッシュできます。 Azure portal 内の VM に移動し、 **[概要]** を選択してから、 **[接続]** を選択します。 これにより、リモート デスクトップ プロトコル (RDP) を介して VM に接続されます。 VM を作成したときの資格情報を使用する必要があります。

## <a name="log-in-and-push-the-image-to-the-registry"></a>ログインしてイメージをレジストリにプッシュします。

ログインしたら、コマンド プロンプトを開き、次の Docker コマンドを開始します。

-   **docker images** - VM で現在インストールされているすべてのイメージの一覧が表示されます。 **microfocus/es-acctdemo** に注目してください。これを使用するからです。

-   **docker login acrmf50.azurecr.io** - ここでの正しい形式は、*docker login\<registry name\>* です。 レジストリの作成時に使用した名前に置き換えます。

    -   Azure portal からコピーした **ユーザー名** と **パスワード** が必要になります。 次のような画像が表示されます。

    ![管理者コマンド プロンプトのスクリーンショット](media/deploy-image-2.png)

-   **docker tag microfocus/es-acctdemo acrmf50.azurecr.io/es-acctdemo** - リポジトリの名前を使用して適切なイメージをタグ付けします。 **注**:名前 \<microfocus/es-acctdemo\> が機能しない場合は、イメージ ID 全体を使用してみてください。 コマンドを実行した後、**docker images –no-trunc** と入力します。 画面表示は次の画像のようになります。 画像に適切にタグが付けられていることに注意してください。

    ![管理者のコマンド プロンプト画面を選択する](media/deploy-image-3.png)

-   **docker push acrmf50.azurecr.io/es-acctdemo** - 実際のリポジトリへのプッシュを開始します。 サイズは 15 GB であるため、実行には数分かかります。 うまくいくと、次の画像のように表示されます。

    ![管理者コマンド プロンプトの画面](media/deploy-image-4.png)

ここで、Azure portal に戻ります。具体的には **[リポジトリ]** です。 **[リポジトリ]** のメニューで、 **[リポジトリ]** を選択すると、**es-acctdemo** が表示されます。 ここで、AKS クラスターを作成します。

## <a name="create-the-azure-kubernetes-aks-cluster"></a>Azure Kubernetes Service (AKS) クラスターを作成する

Azure portal から、 **[リソースの作成]** を選択し、 **[Marketplace]** メニューから **[コンテナー/Kubernetes Service]** を選択します。 次に、 **[Kubernetes クラスターを作成]** ブレードに入力する必要があります。 クラスターは、使用してきたものと同じリージョンとリソース グループに保持してください。 残りの既定値をそのまま使用することができます。ただし、**ノード数** は 1 にする必要があります。 完了したら、 **[確認および作成]** を選択します。

![Kubernetes クラスターを作成する画面](media/deploy-image-5.png)

完了すると、デプロイはブレードで選択した **リソースグループ** に **Kubernetes Service** アーティファクトを配置します。 ただし、実際のクラスターには、デプロイ時に独自のリソース グループが作成されます。 左側のメニューから **[リソースグループ]** を選択した場合は、名前付け規則に基づいていることが確認できます。 画像は私のものです。一覧の最後にあります。

![リソース グループのスクリーンショット](media/deploy-image-6.png)

**イメージを実行する**

次に、イメージをプルして、AKS で実行します。 Azure portal からこれを行う最も簡単な方法は、Cloud Shell を使うことです。 Azure portal の右上にアイコンがあります。 このチュートリアルでは、Bash シェルを使用していることに注意してください。

![Cloud Shell アイコンのスクリーンショット](media/deploy-image-7.png)

シェルが読み込まれたら、次のコマンドを入力します。

**kubectl run es-acctdemo --image acrmf50.azurecr.io/es-acctdemo --port=9040**

これにより、**acrmf50.azurecr.io** リポジトリからイメージがプルされ、AKS に読み込まれます。 次に、ポート 9040 が開いた状態でイメージを実行します。 これは、Docker イメージ用に開いていたポートであることを思い出してください。 Enterprise Server にアクセスするために必要です。

Kubernetes は、デプロイが作成されたことを示すメッセージで応答する必要があります。

![デプロイ メッセージのスクリーンショット](media/deploy-image-8.jpg)

コンテナーが実際に実行されているかどうかを確認するには、**kubectl get pods** と入力します。

次の画像に示すように、実行中のポッドとして es-acctdemo が表示されます。

![実行中のポッドとしての es-acctdemo のスクリーンショット](media/deploy-image-9.png)

お疲れさまでした。 これで、Azure Kubernetes Service で Enterprise Server を実行できています。 次のアーティクルでは、Enterprise Server 管理コンソールにアクセスする方法と、Kubernetes を活用してデプロイをスケールアウトする方法について説明します。
