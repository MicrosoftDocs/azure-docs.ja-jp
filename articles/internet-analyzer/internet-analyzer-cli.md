---
title: CLI を使用して Internet Analyzer テストを作成する | Microsoft Docs
description: この記事では、Azure CLI を使用して最初の Internet Analyzer テストを作成する方法について説明します。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a9a9fe93ebe302a76d69249dc56933e1bcc924d1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200089"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>CLI を使用した Internet Analyzer テストの作成 (プレビュー)

Internet Analyzer リソースを作成する方法は 2 つあります。Azure portal を使用する方法と、[CLI](internet-analyzer-create-test-portal.md) を使用する方法です。 このセクションは、CLI を使用して新しい Azure Internet Analyzer リソースを作成するのに役立ちます。 


> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。
>

## <a name="before-you-begin"></a>開始する前に

パブリック プレビューは世界中で使用できます。ただしデータ ストレージは、プレビュー期間中は *米国西部 2* に限定されます。

## <a name="object-model"></a>オブジェクト モデル
Internet Analyzer CLI では、次の種類のリソースが公開されます。
* **テスト** - テストでは、一定期間における、2 つのインターネット エンドポイント (A と B) のエンドユーザー パフォーマンスが比較されます。
* **プロファイル** - テストは Internet Analyzer プロファイルの下で作成されます。 プロファイルでは、関連のあるテストをグループにまとめることができます。1 つのプロファイルには、1 つまたは複数のテストを含めることができます。
* **構成済みのエンドポイント** - さまざまな構成 (リージョン、アクセラレーション テクノロジなど) でエンドポイントを設定しています。 テストでは、どの構成済みエンドポイントを使用してもかまいません。
* **スコアカード** - スコアカードでは、測定結果が意味のある方法で簡単にまとめられます。 「[スコアカードの解釈](internet-analyzer-scorecard.md)」を参照してください。
* **時系列** - 時系列には、一定期間におけるメトリックの変化が表示されます。

## <a name="profile-and-test-creation"></a>プロファイルとテストの作成
1. [Azure Internet Analyzer の FAQ](internet-analyzer-faq.md) に記載されている「**プレビューに参加するにはどうすればよいですか?** 」の指示に従って、Internet Analyzer プレビュー版へのアクセスを取得します。
2. [Azure CLI のインストール](/cli/azure/install-azure-cli)を実行します。
3. `login` コマンドを実行して CLI セッションを開始します。
    ```azurecli-interactive
    az login
    ```

    CLI で既定のブラウザーを開くことができる場合、開いたブラウザに Azure サインイン ページが読み込まれます。
    それ以外の場合は、 https://aka.ms/devicelogin でブラウザー ページを開き、ターミナルに表示されている認証コードを入力します。

4. ブラウザーでアカウントの資格情報を使用してサインインします。

5. Internet Analyzer パブリック プレビューへのアクセスが与えられているサブスクリプション ID を選択します。

    ログインすると、ご使用のアカウントに関連付けられているサブスクリプションの一覧が表示されます。 `isDefault: true` が示されているサブスクリプション情報が、ログイン後に、現在アクティブになっているサブスクリプションです。 別のサブスクリプションを選択するには、切り替え先のサブスクリプション ID を指定して [az account set](/cli/azure/account#az-account-set) コマンドを実行します。 サブスクリプションの選択の詳細については、「[複数の Azure サブスクリプションの使用](/cli/azure/manage-azure-subscriptions-azure-cli)」を参照してください。

    対話型以外のサインイン方法も用意されています。詳細については、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli)」を参照してください。

6. **[任意]** 新しい Azure リソース グループを作成します。
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Azure CLI Internet Analyzer 拡張機能をインストールします。
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. 新しい Internet Analyzer プロファイルを作成します。
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. 新規作成されたプロファイルで利用できる、事前構成済みのエンドポイントをすべて一覧表示します。
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. 新規作成された InternetAnalyzer プロファイルの下で新しいテストを作成します。
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    上のコマンドでは、`www.contoso.com` と `www.microsoft.com` ではカスタム パスの下で 1 ピクセルの画像 ([trans.gif](https://fpc.msedge.net/apc/trans.gif)) がホストされていることを想定しています。 オブジェクト パスが明示的に指定されていない場合、Internet Analyzer では既定でオブジェクト パスとして `/apc/trans.gif` が使用されます。事前構成済みのエンドポイントで 1 ピクセル画像がホストされている場所です。 スキーマ (https/http) を指定する必要がないことにもご注目ください。Internet Analyzer では HTTPS エンドポイントのみサポートされるため、HTTPS が想定されます。

11. 新しいテストが Internet Analyzer プロファイルの下に表示されるはずです。
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    出力例:
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. 測定生成を開始するには、テストの **scriptFileUri** で指示される JavaScript ファイルを Web アプリケーションに組み込む必要があります。 具体的な手順は、「[Internet Analyzer クライアントの埋め込み](internet-analyzer-embed-client.md)」ページに記載されています。

13. その "状態" 値を追跡することでテストの進捗状況を監視できます。
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. 時系列やスコアカードを生成することで、集めたテストの結果を調べることができます。
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>次のステップ

* サポートされているコマンドと使用例の完全な一覧については、[Internet Analyzer の CLI リファレンス](/cli/azure/ext/internet-analyzer/internet-analyzer)を参照してください。
* [Internet Analyzer の FAQ](internet-analyzer-faq.md) を読む。
* [Internet Analyzer クライアント](internet-analyzer-embed-client.md)の埋め込みと[カスタム エンドポイント](internet-analyzer-custom-endpoint.md)の作成の詳細を確認する。