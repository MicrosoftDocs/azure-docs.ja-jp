---
title: Striim クイック スタート
description: Striim と Azure Synapse Analytics の使用をすぐに開始します。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d6b5fa1a42201479c5d426a5bbb9188d0cb8e03d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89485976"
---
# <a name="striim-azure-synapse-analytics-marketplace-offering-install-guide"></a>Striim の Azure Synapse Analytics の Marketplace サービスのインストール ガイド

このクイックスタートでは、Azure Synapse Analytics の既存のインスタンスが既にあることを前提とします。

Azure Marketplace で Striim を検索し、[Striim for Data Integration to Azure Synapse Analytics (Staged)]\(Azure Synapse Analytics へのデータ統合用の Striim (ステージング)) オプションを選択します 

![Striim をインストールする][install]

Striim クラスター名、パスワード、および管理者のパスワードを書き留めて、指定したプロパティで Striim VM を構成します

![Striim を構成する][configure]

デプロイした後、Azure portal で \<VM Name>-masternode をクリックし、[接続] をクリックし、[Login using VM local account]\(VM ローカル アカウントを使用してログイン) をコピーします 

![Azure Synapse Analytics に Striim を接続する][connect]

sqljdbc42.jar を <https://www.microsoft.com/en-us/download/details.aspx?id=54671> からローカル コンピューターにダウンロードします。 

コマンドライン ウィンドウを開き、JDBC jar をダウンロードした場所にディレクトリを変更します。 SCP で jar ファイルを Striim VM にコピーして、Azure portal からのアドレスとパスワードを取得します

![jar ファイルを VM にコピーする][copy-jar]

別のコマンド ライン ウィンドウを開くか、ssh ユーティリティを Striim クラスターに ssh 接続します

![クラスターに SSH 接続する][ssh]

次のコマンドを実行して、JDBC jar ファイルを Striim の lib ディレクトリに移動し、サーバーを起動および停止します。

   1. sudo su
   2. cd /tmp
   3. mv sqljdbc42.jar /opt/striim/lib
   4. systemctl stop striim-node
   5. systemctl stop striim-dbms
   6. systemctl start striim-dbms
   7. systemctl start striim-node

![Striim クラスターを起動する][start-striim]

ここで、お好きなブラウザーを開いて、\<DNS Name>:9080 に移動します

![ログイン画面に移動する][navigate]

Azure portal で設定したユーザー名とパスワードでログインし、適切なウィザードを選択して作業を開始するか、アプリのページに移動してドラッグ アンド ドロップ UI の使用を開始します

![サーバーの資格情報でログインします][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
