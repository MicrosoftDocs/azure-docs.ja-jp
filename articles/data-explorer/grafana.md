---
title: Grafana を使用して Azure Data Explorer のデータを視覚化する
description: この記事では、Azure Data Explorer を Grafana のデータ ソースとして設定し、サンプル クラスターのデータを視覚化する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 6/30/2019
ms.openlocfilehash: 0f148a97b25afb9135223ff92afb898d4734c586
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537798"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Grafana で Azure Data Explorer のデータを視覚化する

Grafana は、データのクエリを実行して視覚化し、視覚化に基づいてダッシュボードを作成して共有できる分析プラットフォームです。 Grafana には、Azure Data Explorer に接続してそのデータを視覚化できる、Azure Data Explorer "*プラグイン*" が用意されています。 この記事では、Azure Data Explorer を Grafana のデータ ソースとして設定し、サンプル クラスターのデータを視覚化する方法について説明します。

次のビデオを使用することで、Grafana の Azure Data Explorer プラグインを使って、Azure Data Explorer を Grafana 用のデータ ソースとして設定してから、データを視覚化する方法を学習できます。 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

または、以下の記事で詳しく説明されているように、[データ ソースを構成](#configure-the-data-source)し、[データを視覚化](#visualize-data)することができます。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、以下が必要です。

* お使いのオペレーティング システムに対応する [Grafana バージョン 5.3.0 以降](https://docs.grafana.org/installation/)。

* Grafana の [Azure Data Explorer プラグイン](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation)。

* StormEvents サンプル データを含むクラスター。 詳細については、「[クイック スタート: Azure Data Explorer クラスターとデータベースを作成する](create-cluster-database-portal.md)」、および「[Azure のデータ エクスプローラーにサンプル データを取り込む](ingest-sample-data.md)」をご覧ください。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-data-source"></a>データ ソースの構成

Azure Data Explorer を Grafana のデータ ソースとして構成するには、次の手順を実行します。 このセクションでは、これらの手順について詳しく説明します。

1. Azure Active Directory (Azure AD) サービス プリンシパルを作成します。 このサービス プリンシパルは、Azure Data Explorer サービスにアクセスするために Grafana によって使用されます。

1. Azure Data Explorer データベースの "*閲覧者*" ロールに Azure AD サービス プリンシパルを追加します。

1. Azure AD サービス プリンシパルの情報に基づいて Grafana の接続プロパティを指定し、接続をテストします。

### <a name="create-a-service-principal"></a>サービス プリンシパルの作成

サービス プリンシパルは、[Azure portal](#azure-portal) または [Azure CLI](#azure-cli) コマンド ライン エクスペリエンスを使用して作成できます。 どちらの方法を使用しても、作成後に、後の手順で使用する 4 つの接続プロパティの値を取得します。

#### <a name="azure-portal"></a>Azure ポータル

1. サービス プリンシパルを作成するには、[Azure portal のドキュメント](/azure/active-directory/develop/howto-create-service-principal-portal)の指示に従います。

    1. 「[アプリケーションをロールに割り当てる](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role)」で、Azure Data Explorer クラスターに**閲覧者**ロールを割り当てます。

    1. 「[サインインするための値を取得する](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)」で、後の手順で使用する 3 つのプロパティ値 (**ディレクトリ ID** (テナント ID)、**アプリケーション ID**、**パスワード**) をコピーします。

1. Azure portal で **[サブスクリプション]** を選択し、サービス プリンシパルを作成したサブスクリプションの ID をコピーします。

    ![サブスクリプション ID - ポータル](media/grafana/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. サービス プリンシパルを作成する。 適切なスコープを設定し、ロールの種類として `reader` を設定します。

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourGrafana}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    詳細については、「[Azure CLI で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)」をご覧ください。

1. このコマンドでは、次のような結果セットが返されます。 **appId**、**password**、**tenant** の 3 つのプロパティ値をコピーします。

    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourGrafana}:{PortNumber}",
      "name": "https://{UrlToYourGrafana}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. サブスクリプションの一覧を取得します。

    ```azurecli
    az account list --output table
    ```

    適切なサブスクリプション ID をコピーします。

    ![サブスクリプション ID - CLI](media/grafana/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>サービス プリンシパルを閲覧者ロールに追加する

サービス プリンシパルを作成したら、Azure Data Explorer データベースの "*閲覧者*" ロールに追加します。 この作業は、Azure portal の **[アクセス許可]** で実行することも、 **[クエリ]** で管理コマンドを使用して実行することもできます。

#### <a name="azure-portal---permissions"></a>Azure portal - [アクセス許可]

1. Azure portal で、Azure Data Explorer クラスターに移動します。

1. **[概要]** セクションで、StormEvents サンプル データが含まれたデータベースを選択します。

    ![データベースの選択](media/grafana/select-database.png)

1. **[アクセス許可]** を選択し、 **[追加]** を選択します。

    ![データベースのアクセス許可](media/grafana/database-permissions.png)

1. **[Add database permissions]\(データベース アクセス許可の追加\)** で、 **[閲覧者]** ロールを選択し、 **[Select principals]\(プリンシパルの選択\)** を選択します。

    ![データベースのアクセス許可を追加する](media/grafana/add-permission.png)

1. 作成したサービス プリンシパルを検索します (この例では、プリンシパル **mb-grafana** が表示されています)。 プリンシパルを選択し、 **[選択]** を選択します。

    ![Azure portal でアクセス許可を管理する](media/grafana/new-principals.png)

1. **[保存]** を選択します。

    ![Azure portal でアクセス許可を管理する](media/grafana/save-permission.png)

#### <a name="management-command---query"></a>管理コマンド - [クエリ]

1. Azure portal で、Azure Data Explorer クラスターに移動し、 **[クエリ]** を選択します。

    ![Query](media/grafana/query.png)

1. クエリ ウィンドウで次のコマンドを実行します。 Azure portal または CLI で取得したアプリケーション ID とテナント ID を使用します。

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    このコマンドでは、次のような結果セットが返されます。 この例では、最初の行はデータベース内の既存のユーザーを示し、2 番目の行は先ほど追加したサービス プリンシパルを示しています。

    ![結果セット](media/grafana/result-set.png)

### <a name="specify-properties-and-test-the-connection"></a>プロパティを指定し、接続をテストする

"*閲覧者*" ロールに割り当てられたサービス プリンシパルを使用して、Grafana のインスタンスでプロパティを指定し、Azure Data Explorer への接続をテストします。

1. Grafana で、左側のメニューの歯車アイコンを選択し、 **[Data Sources]\(データ ソース\)** を選択します。

    ![データ ソース](media/grafana/data-sources.png)

1. **[Add data source (データ ソースの追加)]** を選択します。

1. **[Data Sources / New]\(データソース/新規\)** ページで、データ ソースの名前を入力し、種類として **[Azure Data Explorer Datasource]\(Azure Data Explorer データ ソース\)** を選択します。

    ![接続名と種類](media/grafana/connection-name-type.png)

1. https://{クラスター名}.{リージョン}.kusto.windows.net の形式でクラスターの名前を入力します。 Azure portal または CLI で取得した他の値を入力します。 マッピングについては、次の画像の下の表をご覧ください。

    ![接続のプロパティ](media/grafana/connection-properties.png)

    | Grafana UI | Azure ポータル | Azure CLI |
    | --- | --- | --- |
    | サブスクリプション ID | サブスクリプション ID | SubscriptionId |
    | テナント ID | ディレクトリ ID | tenant |
    | クライアント ID | アプリケーション ID | appId |
    | クライアント シークレット | パスワード | password |
    | | | |

1. **[Save & Test]\(保存してテスト\)** を選択します。

    テストが成功した場合は、次のセクションに進みます。 問題が発生した場合は、Grafana で指定した値を確認し、これまでの手順を見直します。

## <a name="visualize-data"></a>データの視覚化

Azure Data Explorer を Grafana のデータ ソースとして構成したら、データを視覚化します。 ここでは基本的な例を紹介しますが、できることはほかにも多数あります。 サンプル データ セットに対して実行する他のクエリの例については、「[Azure データ エクスプローラーのクエリを記述する](write-queries.md)」を参照することをお勧めします。

1. Grafana で、左側のメニューのプラス アイコンを選択し、 **[Dashboard]\(ダッシュボード\)** を選択します。

    ![ダッシュボードの作成](media/grafana/create-dashboard.png)

1. **[Add]\(追加\)** タブの **[Graph]\(グラフ\)** を選択します。

    ![グラフの追加](media/grafana/add-graph.png)

1. グラフ パネルで、 **[Panel Title]\(パネル タイトル\)** 、 **[Edit]\(編集\)** の順に選択します。

    ![パネルの編集](media/grafana/edit-panel.png)

1. パネルの下部にある **[Data Source]\(データ ソース\)** を選択し、構成済みのデータ ソースを選択します。

    ![データ ソースの選択](media/grafana/select-data-source.png)

1. クエリ ウィンドウで、次のクエリをコピーし、 **[Run]\(実行\)** を選択します。 このクエリは、サンプル データ セットのイベント数を日単位でバケット処理します。

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Run query](media/grafana/run-query.png)

1. グラフは既定で過去 6 時間のデータを対象としているため、グラフには結果が表示されません。 上部のメニューで、 **[Last 6 hours]\(過去 6 時間\)** を選択します。

    ![過去 6 時間](media/grafana/last-six-hours.png)

1. StormEvents サンプル データ セットに含まれている 2007 年を対象とするカスタム範囲を指定します。 **[適用]** を選択します。

    ![カスタム日付範囲](media/grafana/custom-date-range.png)

    これで、日単位でバケット処理された 2007 年のデータがグラフに表示されます。

    ![完成したグラフ](media/grafana/finished-graph.png)

1. 上部のメニューで保存アイコンを選択します。 ![[Save]\(保存\) アイコン](media/grafana/save-icon.png)。

## <a name="next-steps"></a>次の手順

* [Azure Data Explorer のクエリを記述する](write-queries.md)

* [チュートリアル: Power BI で Azure Data Explorer のデータを視覚化する](visualize-power-bi.md)
