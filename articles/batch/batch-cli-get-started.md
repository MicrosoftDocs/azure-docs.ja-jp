---
title: "Batch 用の Azure CLI の概要 | Microsoft Docs"
description: "Azure Batch サービスのリソースを管理するために使用できる Azure CLI の Batch コマンドの概要を簡単に説明します。"
services: batch
documentationcenter: 
author: v-dotren
manager: timlt
editor: 
ms.assetid: fcd76587-1827-4bc8-a84d-bba1cd980d85
ms.service: batch
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 09/28/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68a5493282fa4a0b54ba551c48ae963a42b94dca
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-batch-resources-with-azure-cli"></a>Azure CLI で Batch リソースを管理する

Azure CLI 2.0 は、Azure リソースを管理するための、Azure の新しいコマンド ライン エクスペリエンスです。 macOS、Linux、および Windows で使用できます。 Azure CLI 2.0 は、コマンド ラインから Azure リソースを管理できるように最適化されています。 Azure CLI を使用すると、Azure Batch アカウントを管理したり、プール、ジョブ、タスクなどのリソースを管理したりすることができます。 Batch API、Azure Portal、Batch PowerShell コマンドレットを使用して実行するタスクの多くは、Azure CLI でスクリプト化することができます。

この記事では、[Azure CLI バージョン 2.0](https://docs.microsoft.com/cli/azure/overview) を Batch で使用する方法の概要を説明します。 CLI を Azure で使用する方法の概要については、「[Azure CLI 2.0 を使ってみる](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)」を参照してください。

Azure CLI の最新バージョンであるバージョン 2.0 を使用することをお勧めします。 バージョン 2.0 の詳細については、「[Azure Command Line 2.0 now generally available (Azure コマンド ライン 2.0 の一般公開)](https://azure.microsoft.com/blog/announcing-general-availability-of-vm-storage-and-network-azure-cli-2-0/)」を参照してください。

## <a name="set-up-the-azure-cli"></a>Azure CLI のセットアップ

Azure CLI をインストールするには、「[Azure CLI 2.0 のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」に記載されている手順に従います。

> [!TIP]
> 最新のサービスや機能強化を活かすためにも、Azure CLI のインストールを定期的に更新することをお勧めします。
> 
> 

## <a name="command-help"></a>コマンドのヘルプ

Azure CLI のどのコマンドでも、コマンドの後ろに `-h` を付加すると、ヘルプ テキストを表示できます。 他のオプションは指定しないでください。 For example:

* `az` コマンドのヘルプを表示するには、「`az -h`」と入力します。
* CLI のすべての Batch コマンドの一覧を取得するには、「`az batch -h`」と入力します。
* Batch アカウントの作成に関するヘルプを表示するには、「 `az batch account create -h`

確信が持てない場合は、コマンド ライン オプション `-h` を使用して、任意の Azure CLI コマンドのヘルプを表示してください。

> [!NOTE]
> Azure CLI の以前のバージョンでは、CLI コマンドの先頭に `azure` を付けていました。 バージョン 2.0 では、すべてのコマンドが `az` で始まるようになりました。 バージョン 2.0 ではスクリプトを更新して新しい構文を使用するようにしてくだし。
>
>  

また、[Batch の Azure CLI コマンド](https://docs.microsoft.com/cli/azure/batch)の詳細については、Azure CLI リファレンス ドキュメントを参照してください。 

## <a name="log-in-and-authenticate"></a>ログインと認証

Batch で Azure CLI を使用するには、ログインと認証を行う必要があります。 次の 2 つの簡単な手順に従ってください。

1. **Azure にログインします。** Azure にログインすると、[Batch 管理サービス](batch-management-dotnet.md) コマンドも含め、Azure Resource Manager コマンドにアクセスできるようになります。  
2. **Batch アカウントにログインします。** Batch アカウントにログインすると、Batch サービス コマンドにアクセスできるようになります。   

### <a name="log-in-to-azure"></a>Azure へのログイン

Azure にログインするには、いくつかの方法があります。詳細については、「[Azure CLI 2.0 を使用してログインする](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)」で説明しています。

1. [対話形式でログインします](https://docs.microsoft.com/cli/azure/authenticate-azure-cli#az_authenticate_azure_cli_interactive_log_in)。 コマンド ラインから Azure CLI コマンドを実行しているときに、対話形式でログインします。
2. [サービス プリンシパルでログインします](https://docs.microsoft.com/cli/azure/authenticate-azure-cli#az_authenticate_azure_cli_logging_in_with_a_service_principal)。 スクリプトまたはアプリケーションから Azure CLI コマンドを実行しているときに、サービス プリンシパルでログインします。

この記事の目的上、対話形式で Azure にログインする方法を示します。 コマンド ラインで「[az login](https://docs.microsoft.com/cli/azure/#login)」と入力します。

```azurecli
# Log in to Azure and authenticate interactively.
az login
```

`az login` コマンドは、次のように、認証に使用するトークンを返します。 示された指示に従って、Web ページを開き、Azure にトークンを送信します。

![Azure へのログイン](./media/batch-cli-get-started/az-login.png)

「[サンプル シェル スクリプト](#sample-shell-scripts)」セクションの一覧にある例も、対話形式で Azure にログインして Azure CLI セッションを開始する方法を示しています。 ログインしたら、コマンドを呼び出して、Batch アカウント、キー、アプリケーション パッケージ、クォータなどの Batch 管理リソースを操作することができます。  

### <a name="log-in-to-your-batch-account"></a>Batch アカウントへのログイン

Azure CLI を使用してプール、ジョブ、タスクなどの Batch リソースを管理するには、Batch アカウントにログインし、認証を行う必要があります。 Batch サービスにログインするには、[az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) コマンドを使用します。 

Batch アカウントでの認証には、次の 2 つの選択肢があります。

- **Azure Active Directory (Azure AD) 認証を使用する。** 

    Azure AD での認証は、Batch で Azure CLI を使用する場合の既定の方法であり、ほとんどのシナリオに適しています。 
    
    前のセクションで説明したように、対話形式で Azure にログインすると資格情報がキャッシュされるため、Azure CLI でそれらの資格情報を使用して Batch アカウントにログインすることができます。 サービス プリンシパルを使用して Azure にログインする場合は、Batch アカウントへのログインにもこれらの資格情報が使用されます。

    Azure AD の利点は、ロール ベースのアクセス制御 (RBAC) を利用できることです。 RBAC でのユーザーのアクセスは、アカウント キーを所有しているかどうかではなく、割り当てられているロールに依存します。 アカウント キーを管理する代わりに、RBAC ロールを管理して、Azure AD にアクセスと認証を処理させることができます。  

        To log in to your Batch account using Azure AD, call the [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) command: 

    ```azurecli
    az batch account login -g myresource group -n mybatchaccount
    ```

- **共有キー認証を使用する。**

    [共有キー認証](https://docs.microsoft.com/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service#authentication-via-shared-key)では、アカウント アクセス キーを使用して、Batch サービスに対して Azure CLI コマンドを認証します。

    Batch コマンドの呼び出しを自動化する Azure CLI スクリプトを作成している場合は、共有キー認証か、Azure AD サービス プリンシパルを使用することができます。 一部のシナリオでは、サービス プリンシパルを作成するよりも共有キー認証を使用する方が簡単な場合があります。  

    共有キー認証を使用してログインするには、コマンド ラインで次のように `--shared-key-auth` オプションを含めます。

    ```azurecli
    az batch account login -g myresourcegroup -n mybatchaccount --shared-key-auth
    ```

「[サンプル シェル スクリプト](#sample-shell-scripts)」セクションの一覧にある例では、Azure CLI で Azure AD と共有キーの両方を使用して Batch アカウントにログインする方法を示しています。

## <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Azure Batch CLI のテンプレートとファイル転送の使用 (プレビュー)

Azure CLI を使用すると、コードを記述せずに、エンド ツー エンドで Batch ジョブを実行できます。 Batch テンプレート ファイルでは、Azure CLI を使用したプール、ジョブ、およびタスクの作成がサポートされます。 Azure CLI を使用すると、ジョブ入力ファイルを Batch アカウントに関連付けられた Azure ストレージ アカウントにアップロードし、そこからジョブの出力ファイルをダウンロードすることもできます。 詳しくは、「[Azure Batch CLI のテンプレートとファイル転送の使用 (プレビュー)](batch-cli-templates.md)」を参照してください。

## <a name="sample-shell-scripts"></a>サンプル シェル スクリプト

次の表に示すサンプル スクリプトは、Batch サービスと Batch 管理サービスで Azure CLI コマンドを使用して一般的なタスクを実行する方法を示しています。 これらのサンプル スクリプトでは、Batch 用の Azure CLI で使用できるコマンドの多くがカバーされています。 

| スクリプト | メモ |
|---|---|
| [Batch アカウントを作成する](./scripts/batch-cli-sample-create-account.md) | Batch アカウントを作成し、ストレージ アカウントに関連付けます。 |
| [アプリケーションを追加する](./scripts/batch-cli-sample-add-application.md) | アプリケーションを追加して、パッケージ化されたバイナリをアップロードします。|
| [Batch プールを管理する](./scripts/batch-cli-sample-manage-pool.md) | プールの作成、サイズ変更、および管理の方法を示します。 |
| [Batch でジョブとタスクを実行する](./scripts/batch-cli-sample-run-job.md) | ジョブの実行とタスクの追加を説明します。 |

## <a name="json-files-for-resource-creation"></a>リソースを作成するための JSON ファイル

プールやジョブなどの Batch リソースを作成する際、コマンド ライン オプションとしてパラメーターを渡す代わりに、新しいリソースの構成を含む JSON ファイルを指定できます。 For example:

```azurecli
az batch pool create my_batch_pool.json
```

ほとんどの Batch リソースはコマンド ライン オプションのみを使用して作成できますが、一部の機能では、リソースの詳細が含まれている JSON 形式のファイルを指定する必要があります。 たとえば、開始タスクのリソース ファイルを指定する場合は、JSON ファイルを使用する必要があります。

リソースの作成に必要な JSON 構文を確認するには、[Batch の REST API リファレンス][rest_api]のドキュメントを参照してください。 REST API リファレンスの "*リソースの種類*" の追加に関する各トピックには、そのリソースを作成するためのサンプル JSON スクリプトが含まれています。 これらのサンプル JSON スクリプトは、Azure CLI で使用する JSON ファイルのテンプレートとして使用できます。 たとえば、プールを作成する JSON 構文を確認するには、「[Add a pool to an account (アカウントにプールを追加する)][rest_add_pool]」を参照してください。

JSON ファイルを指定するサンプル スクリプトについては、[Batch でのジョブとタスクの実行](./scripts/batch-cli-sample-run-job.md)に関するページを参照してください。

> [!NOTE]
> リソースの作成時に JSON ファイルを指定すると、そのリソースに対してコマンド ラインで指定した他のすべてのパラメーターが無視されます。
> 
> 

## <a name="efficient-queries-for-batch-resources"></a>Batch リソースに対する効率的なクエリ

各バッチ リソースの種類で `list` コマンドがサポートされています。このコマンドは Batch アカウントを照会し、その種類のリソースを一覧表示します。 たとえば、アカウント内のプールやジョブ内のタスクを一覧表示することができます。

```azurecli
az batch pool list
az batch task list --job-id job001
```

`list` 操作で Batch サービスに対するクエリを行う場合、返されるデータの量を制限するために、OData 句を指定することができます。 フィルター処理はすべてサーバー側で行われるため、送信されるのは要求したデータのみです。 これらの句を使用して、一覧表示操作の実行時に帯域幅を節約できます (それに伴って時間も節約できます)。

次の表では、Batch サービスでサポートされている OData 句について説明します。

| 句 | Description |
|---|---|
| `--select-clause [select-clause]` | 各エンティティのプロパティのサブセットを返します。 |
| `--filter-clause [filter-clause]` | 指定された OData 式に一致するエンティティのみを返します。 |
| `--expand-clause [expand-clause]` | 基になる 1 つの REST 呼び出しのエンティティ情報を取得します。 現時点では、expand 句は `stats` プロパティのみをサポートしています。 |

OData 句の使用方法を示すサンプル スクリプトについては、[Batch でのジョブとタスクの実行](./scripts/batch-cli-sample-run-job.md)に関するページを参照してください。

OData 句で効率的なリスト クエリを実行する方法の詳細については、[Azure Batch サービスの効率的なクエリ](batch-efficient-list-queries.md)に関するページを参照してください。

## <a name="troubleshooting-tips"></a>トラブルシューティングのヒント

Azure CLI の問題をトラブルシューティングするときに、以下のヒントが役に立ちます。

* `-h` を使用して、任意の CLI コマンドの **ヘルプ テキスト** を取得します。
* `-v` と `-vv` を使用して、**詳細**なコマンド出力を表示します。 `-vv` フラグを指定すると、Azure CLI は実際の REST 要求および応答を表示します。 これらのスイッチは、完全なエラー出力を表示する場合に便利です。
* `--json` オプションを使用すると、**コマンド出力を JSON として**表示できます。 たとえば、 `az batch pool show pool001 --json` を実行すると、pool001 のプロパティが JSON 形式で表示されます。 この出力をコピーして変更し、 `--json-file` で使用できます (この記事の前半で説明した [JSON ファイル](#json-files) の項目を参照してください)。
<!---Loc Comment: Please, check link [JSON files] since it's not redirecting to any location.--->
* [Batch フォーラム][batch_forum]は、Batch チーム メンバーによって監視されています。 問題が発生した場合や、特定の操作についてアドバイスが欲しい場合は、質問を投稿できます。

## <a name="next-steps"></a>次のステップ

* Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)を参照してください。
* Batch リソースの詳細については、[開発者向けの Azure Batch の概要](batch-api-basics.md)に関するページを参照してください。
* Batch テンプレートを使用してコードの記述なしでプール、ジョブ、およびタスクを作成する方法については、「[Azure Batch CLI のテンプレートとファイル転送の使用 (プレビュー)](batch-cli-templates.md)」を参照してください。

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
