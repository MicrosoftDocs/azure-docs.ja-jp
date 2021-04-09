---
title: クイック スタート:初めての Ruby クエリ
description: このクイックスタートでは、手順に従って、Ruby 用の Resource Graph gem を有効にし、初めてのクエリを実行します。
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 41769359eaa1f930d34b0a182f35da38a6f5109d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920039"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-ruby"></a>クイック スタート:Ruby を使用して初めての Resource Graph クエリを実行する

Azure Resource Graph を使用する最初の手順では、Ruby に必要な gem がインストールされていることを確認します。 このクイックスタートでは、gem を Ruby のインストールに追加するプロセスについて説明します。

このプロセスを完了すると、gem を Ruby のインストールに追加し、初めての Resource Graph クエリを実行できます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
- _clientId_ と _clientSecret_ を含む Azure サービス プリンシパル。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-graph-project"></a>Resource Graph プロジェクトを作成する

Ruby で Azure Resource Graph のクエリを実行できるようにするには、gem を `Gemfile` に追加する必要があります。 この gem は、Ruby を使用できる場所であればどこでも動作します ([Azure Cloud Shell](https://shell.azure.com)、[Windows 10 上の bash](/windows/wsl/install-win10)、ローカル インストールなど)。

1. 最新の Ruby がインストールされていることを確認します (**2.7.1** 以降)。 まだインストールされていない場合は、[Ruby-Lang.org](https://www.ruby-lang.org/en/downloads/) からダウンロードします。

1. 選択した Ruby 環境の新しいプロジェクト フォルダー内で、バンドルを初期化します。

   ```bash
   # Initialize a bundle to create a new Gemfile
   bundle init
   ```

1. Azure Resource Graph に必要な gem を使用して `Gemfile` を更新します。 更新されたファイルは、次の例のようになります。

   ```file
   # frozen_string_literal: true

   source "https://rubygems.org"

   git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

   # gem "rails"
   gem 'azure_mgmt_resourcegraph', '~> 0.17.2'
   ```

1. プロジェクト フォルダーから `bundle install` を実行します。 `bundle list` と共に gem がインストールされたことを確認します。

1. 同じプロジェクト フォルダーで、次のコードを使用して `argQuery.rb` を作成し、更新したファイルを保存します。

   ```ruby
   #!/usr/bin/env ruby

   require 'azure_mgmt_resourcegraph'
   ARG = Azure::ResourceGraph::Profiles::Latest::Mgmt

   # Get arguments and set options
   options = {
       tenant_id: ARGV[0],
       client_id: ARGV[1],
       client_secret: ARGV[2],
       subscription_id: ARGV[3]
   }

   # Create Resource Graph client from options
   argClient = ARG::Client.new(options)

   # Create Resource Graph QueryRequest for subscription with query
   query_request = ARGModels::QueryRequest.new.tap do |qr|
       qr.subscriptions = [ARGV[3]]
       qr.query = ARGV[4]
   end

   # Get the resources from Resource Graph
   response = argClient.resources(query_request)

   # Convert data to JSON and output
   puts response.data.to_json
   ```

## <a name="run-your-first-resource-graph-query"></a>最初の Resource Graph クエリを実行する

Ruby スクリプトを保存し、使用する準備ができたら、単純な Resource Graph クエリを試します。 このクエリでは、各リソースの **名前** と **リソースの種類** を使用して、最初の 5 つの Azure リソースが返されます。

`argQuery` に対するそれぞれの呼び出しには、自分独自の値で置き換える必要のある変数があります。

- `{tenantId}` - 実際のテナント ID に置き換えます
- `{clientId}` - ご自分のサービス プリンシパルのクライアント ID に置き換えます
- `{clientSecret}` - ご自分のサービス プリンシパルのクライアント シークレットに置き換えます
- `{subscriptionId}` - サブスクリプション ID で置き換えます

1. `Gemfile` および `argClient.rb` ファイルを作成したプロジェクト フォルダーにディレクトリを変更します。

1. gem と `resources` メソッドを使用して、初めての Azure Resource Graph クエリを実行します。

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > このクエリの例では `order by` などの並べ替え修飾子が指定されていないため、、このクエリを複数回実行すると要求ごとに異なるリソース セットが生成される可能性があります。

1. 最後のパラメーターを `argQuery.rb` に変更し、**Name** プロパティで並べ替える (`order by`) ようにクエリを変更します。

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > 最初のクエリと同様に、このクエリを複数回実行すると要求あたり異なる一連のリソースを生成する可能性があります。 クエリ コマンドの順序が重要です。 この例では、`limit` の後に `order by` がきます。 このコマンドの順序によって、まずクエリ結果が制限され、次にその結果が並べ替えられます。

1. 最後のパラメーターを `argQuery.rb` に変更し、まず **Name** プロパティで並べ替え (`order by`)、次に上位 5 件の結果に制限 (`limit`) するようにクエリを変更します。

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

最後のクエリを複数回実行した場合、環境内で何も変更がないと仮定すると、返される結果は変わらず、**Name** プロパティで並べ替えられますが、引き続き上位 5 件の結果に制限されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

インストールした gem を、ご使用の Ruby 環境から削除する場合は、次のコマンドを使用します。

```bash
# Remove the installed gems from the Ruby environment
gem uninstall azure_mgmt_resourcegraph
```

> [!NOTE]
> gem `azure_mgmt_resourcegraph` には、`ms_rest` や `ms_rest_azure` などの依存関係があり、環境に応じてインストールされている場合もあります。 これらの gem も不要になった場合はアンインストールできます。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ご使用の Ruby 環境に Resource Graph gem を追加し、初めてのクエリを実行しました。 Resource Graph 言語の詳細については、クエリ言語の詳細ページに進んでください。

> [!div class="nextstepaction"]
> [クエリ言語に関する詳細情報を入手します](./concepts/query-language.md)
