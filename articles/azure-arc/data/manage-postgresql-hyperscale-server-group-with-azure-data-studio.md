---
title: Azure Data Studio を使用して PostgreSQL インスタンスを管理する
description: Azure Data Studio を使用して PostgreSQL インスタンスを管理する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7dcc0f916a15598060e034dcf62536ee13e2672e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92320234"
---
# <a name="use-azure-data-studio-to-manage-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Data Studio を使用して、Azure Arc 対応の PostgreSQL Hyperscale サーバー グループを管理する


この記事では、次の方法について説明します。
- 概要、接続文字列、プロパティ、リソース正常性などのダッシュボード ビューで PostgreSQL インスタンスを管理する
- データとスキーマを操作する

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>前提条件

- [azdata、Azure Data Studio、Azure CLI をインストールする](install-client-tools.md)
- Azure Data Studio に、 **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** および **Azure Arc** および **PostgreSQL** 拡張機能をインストールする
- [Azure Arc データ コントローラー](create-data-controller-using-azdata.md)を作成する
- Azure Data Studio を起動する

## <a name="connect-to-the-azure-arc-data-controller"></a>Azure Arc データ コントローラーに接続する

Azure Data Studio で、 **[Azure Arc コントローラー]** ノードを展開し、 **[コントローラーの接続]** ボタンを選択します。

Azure データ コントローラーへの接続情報を入力します。

- **コントローラー URL:**

    Kubernetes でコントローラーに接続するための URL。 `https://<IP_address_of_the_controller>:<Kubernetes_port.` の形式で入力します。例:

    ```console
    https://12.345.67.890:30080
    ```
- **ユーザー名:**

    コントローラーへの接続に使用するユーザー アカウントの名前。 `azdata login` を実行する際に通常使用する名前を使用します。 これは、一般に psql から PostgreSQL データベース エンジンに接続するために使用する PostgreSQL ユーザーの名前ではありません。
- **パスワード:** コントローラーへの接続に使用するユーザー アカウントのパスワード


Azure Data Studio に、Arc データ コントローラーが表示されます。 これを展開すると、管理対象の PostgreSQL インスタンスの一覧が表示されます。

## <a name="manage-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc 対応の PostgreSQL Hyperscale サーバー グループを管理する

管理する PostgreSQL インスタンスを右クリックし、[管理] を選択します

PostgreSQL ダッシュボード ビュー:

そのウィンドウの左側に複数のダッシュボードが表示されます。

- **概要:** 名前、PostgreSQL 管理者ユーザー名、Azure サブスクリプション ID、構成、データベース エンジンのバージョン、Grafana および Kibana のエンドポイントなどのインスタンスに関する概要情報が表示されます。
- **接続文字列:** psql、Node.js、PHP、Ruby などの PostgreSQL インスタンスに接続するために必要となる可能性があるさまざまな接続文字列が表示されます。
- **問題の診断と解決:** トラブルシューティング ノートブックを展開したときに、インスタンスのトラブルシューティングに役立つさまざまなリソースが表示されるランディング ページです
- **新しいサポート要求:** パブリック プレビューの発表が開始されたら、サポート サービスからの支援を要求できるランディングページです。

## <a name="work-with-your-data-and-schema"></a>データとスキーマを操作する

[Azure Data Studio] ウィンドウの左側で、 **[サーバー]** ノードを展開します。

さらに [接続の追加] を選択し、PostgreSQL インスタンスへの接続の詳細を入力します。
- **接続の種類:** PostgreSQL
- **サーバー名:** PostgreSQL インスタンスの名前を入力します。 例: postgres01
- **認証の種類:** Password
- **ユーザー名:** たとえば、標準/既定の PostgreSQL 管理者ユーザー名を使用できます。 このフィールドでは、大文字と小文字が区別されることに注意してください。
- **パスワード:** `azdata postgres server endpoint -n postgres01` コマンドの出力の psql 接続文字列に、PostgreSQL ユーザー名のパスワードがあります
- **データベース名:** 接続先のデータベースの名前を設定します。 __既定__ に設定できます
- **サーバー グループ:** __既定__ に設定できます
- **名前 (省略可能):** 空白のままにすることができます
- **詳細:**
    - **ホスト IP アドレス:** Kubernetes クラスターのパブリック IP アドレスです
    - **ポート:** PostgreSQL インスタンスがリッスンしているポートです。 このポートは、`azdata postgres server endpoint -n postgres01` コマンドの出力の psql 接続文字列の末尾にあります。 Kubernetes がリッスンしていて、Azure Data Studio で Azure データ コントローラーに接続する際に入力したポート 30080 ではありません。
    - **その他のパラメーター:** これらの値は自明である必要があり、それらに表示された既定/空白値でも問題ありません。

**[OK] と [接続]** を選択して、サーバーに接続します。

接続すると、いくつかのエクスペリエンスを使用できるようになります。
- **新しいクエリ**
- **新しいノートブック**
- **サーバーの表示を展開し、データベース内のオブジェクトを参照/操作します**
- **...**

## <a name="next-step"></a>次の手順
[サーバー グループを監視する](monitor-grafana-kibana.md)
