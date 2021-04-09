---
title: GitHub リポジトリを App Configuration と同期する
description: GitHub リポジトリを更新するときに、GitHub Actions を使用して App Configuration インスタンスを自動的に更新します。
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 05/28/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 1c290032f7a33079b560d3c4cc1fcb9526e70331
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98762145"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>GitHub リポジトリを App Configuration と同期する

既存のソース管理手法を引き続き使用するチームは、GitHub Actions を使用して、GitHub リポジトリを App Configuration ストアと自動的に同期させることができます。 これにより、通常どおり構成ファイルに変更を加えることが可能になる一方で、次のような App Configuration の利点を得ることができます。 <br>
&nbsp;&nbsp;&nbsp;&nbsp;•   コード外での一元化された構成 <br>
&nbsp;&nbsp;&nbsp;&nbsp;•   アプリ全体を再デプロイせずに構成を更新する <br>
&nbsp;&nbsp;&nbsp;&nbsp;•   Azure App Service や Functions などのサービスとの統合。 

GitHub Actions の[ワークフロー](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions#the-components-of-github-actions)は、GitHub リポジトリの自動化されたプロセスを定義します。 *Azure App Configuration Sync* アクションは、ソース リポジトリに変更が加えられたときに App Configuration インスタンスの更新をトリガーします。 リポジトリの `/.github/workflows/` パスにある YAML (.yml) ファイルを使用して、ステップとパラメーターを定義します。 アプリ コードと同様に、アプリ構成ファイルをプッシュ、レビュー、またはブランチするときに構成の更新をトリガーできます。

GitHub の[ドキュメント](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions)では、GitHub ワークフローとアクションについて詳細に説明されています。 

## <a name="enable-github-actions-in-your-repository"></a>リポジトリで GitHub Actions を有効にする
この GitHub アクションの使用を開始するには、リポジトリにアクセスし、 **[Actions]\(アクション\)** タブを選択します。 **[新しいワークフロー]** 、 **[Set up a workflow yourself]\(ワークフローを自分で設定する\)** の順に選択します。 最後に、マーケットプレース上で "Azure App Configuration Sync" を検索します。
> [!div class="mx-imgBorder"]
> ![[Actions]\(アクション\) タブを選択する](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![App Configuration Sync アクションを選択する](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>プッシュ後に構成ファイルを同期する
このアクションは、変更が `appsettings.json` にプッシュされたときに Azure App Configuration ファイルを同期します。 開発者が `appsettings.json` に対する変更をプッシュすると、Azure App Configuration Sync アクションにより、App Configuration インスタンスが新しい値で更新されます。

このワークフローの最初のセクションは、`appsettings.json` を含む "*プッシュ*" が *main* ブランチに対して "*行われたとき*" にアクションがトリガーされることを指定しています。 2 番目のセクションは、アクションがトリガーされると実行されるジョブを一覧で示しています。 このアクションは、リポジトリにシークレットとして格納されている接続文字列を使用して、関連するファイルをチェックアウトし、App Configuration インスタンスを更新します。  GitHub でのシークレットの使用の詳細については、暗号化されたシークレットの作成と使用に関する [GitHub の記事](https://docs.github.com/en/actions/reference/encrypted-secrets)を参照してください。

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-strict-sync"></a>厳格な同期を使用する
既定では、GitHub アクションの厳格モードは有効になっていません。つまり、同期では、構成ファイルのキー値がアプリ構成インスタンスに追加されるだけです (キーと値のペアは削除されません)。 厳格モードの有効化とは、構成ファイルに含まれていないキーと値のペアをアプリ構成インスタンスから削除して、構成ファイルと一致するようにすることを意味します。 複数のソースから同期する場合、またはアプリ構成で Azure Key Vault を使用する場合は、他のファイルから構成設定が消去されるのを避けるために、厳格な同期で異なるプレフィックスまたはラベルを使用することをお勧めします (以下のサンプルを参照してください)。 

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```
## <a name="sync-multiple-files-in-one-action"></a>1 つのアクションで複数のファイルを同期する 

構成が複数のファイルに含まれている場合は、次のパターンを使用して、いずれかのファイルが変更されたときに同期をトリガーできます。 このパターンでは、glob ライブラリ https://www.npmjs.com/package/glob が使用されます。 構成ファイル名にコンマが含まれている場合はバックスラッシュを使用してそのコンマをエスケープできることに注意してください。 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'
      - 'appsettings2.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: '{appsettings.json,appsettings2.json}'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
```

## <a name="sync-by-prefix-or-label"></a>プレフィックスまたはラベルによる同期
同期アクションでプレフィックスまたはラベルを指定すると、その特定のセットのみが同期されます。 これは、複数のファイルで厳格な同期を使用する場合に重要です。 構成の設定方法によっては、プレフィックスまたはラベルを各ファイルに関連付けた後、各プレフィックスまたはラベルを別々に同期して、何も上書きされないようにすることができます。 通常、プレフィックスは異なるアプリケーションまたはサービスで使用され、ラベルは異なる環境で使用されます。 

プレフィックスによる同期: 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          prefix: 'Prefix::'
```

ラベルによる同期: 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          label: 'Label'

```

## <a name="use-a-dynamic-label-on-sync"></a>同期時に動的ラベルを使用する
次のアクションでは、同期のたびに動的ラベルが挿入されます。これにより、各同期を一意に識別でき、コードの変更を構成の変更にマップできるようになります。

このワークフローの最初のセクションは、`appsettings.json` を含む "*プッシュ*" が *main* ブランチに対して "*行われたとき*" にアクションがトリガーされることを指定しています。 2 番目のセクションでは、コミット ハッシュに基づいて構成更新用の一意のラベルを作成するジョブを実行します。 その後ジョブは、新しい値とこの更新用の一意のラベルを使用して、App Configuration インスタンスを更新します。

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-azure-key-vault-with-github-action"></a>Azure Key Vault で GitHub アクションを使用する
AppConfiguration で Azure Key Vault を使用する開発者は、通常は appsettings.json と secretreferences.json という 2 つの個別のファイルを使用する必要があります。 secretreferences.json には、キー コンテナー シークレットの URL が含まれます。

{ "mySecret": "{\"uri\":\"https://myKeyVault.vault.azure.net/secrets/mySecret "}" }

appsettings.json に、厳格な同期を実行するように GitHub アクションを構成し、その後、secretreferences.json に、厳格ではない同期を構成します。 次のサンプルでは、いずれかのファイルが更新されると、同期がトリガーされます。

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'
      - 'secretreferences.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          strict: true
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'secretreferences.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          contentType: 'application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8'

```

## <a name="use-max-depth-to-limit-github-action"></a>最大深度を使用して GitHub アクションを制限する
入れ子になった JSON 属性の既定の動作では、オブジェクト全体がフラット化されます。  次の JSON は、このキーと値のペアを定義しています。

| Key | 値 |
| --- | --- |
| Object:Inner:InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

入れ子になったオブジェクトが Configuration インスタンスにプッシュされる値となるよう意図されている場合は、*depth* 値を使用して、適切な深さでフラット化を停止できます。 

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

depth を 2 に指定した場合、上記の例では次のキーと値のペアが返されます。

| Key | 値 |
| --- | --- |
| Object:Inner | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>アクションの入力について
入力パラメーターは、実行時にアクションによって使用されるデータを指定します。  App Configuration Sync で受け入れられる入力パラメーターと、それぞれの予期される値を次の表に示します。  GitHub Actions でのアクション入力の詳細については、GitHub の[ドキュメント](https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions#inputs)を参照してください。

> [!Note]
> 入力 ID では大文字と小文字は区別されません。


| 入力名 | 必須 | 値 |
|----|----|----|
| configurationFile | はい | リポジトリ内の構成ファイルへの相対パス。  glob パターンがサポートされており、複数のファイルを含めることができます。 |
| format | はい | 構成ファイルのファイル形式。  有効な形式:JSON、YAML、properties。 |
| connectionString | はい | App Configuration インスタンスの接続文字列。 接続文字列は、GitHub リポジトリにシークレットとして格納する必要があります。また、ワークフローではシークレット名のみを使用する必要があります。 |
| separator | はい | 構成ファイルをキーと値のペアにフラット化するときに使用される区切り記号。  有効な値: , ; : - _ __ / |
| prefix | いいえ | キーの先頭に追加されるプレフィックス。 |
| label | いいえ | キーと値のペアを設定するときに使用されるラベル。 指定されない場合、null ラベルが使用されます。 |
| strict | いいえ | 厳格モードを有効にするかどうかを決めるブール値。 既定値は false です。 |
| depth | いいえ | 構成ファイルをフラット化するための最大深度。  depth は正の数値である必要があります。  既定では、最大深度はありません。 |
| tags | いいえ | キーと値のペアに設定されているタグを指定します。  予期される形式は、次の形状の JSON オブジェクトの文字列フィールド形式です:  { [propertyName: string]: string; } 各プロパティ名と値はタグになります。 |

## <a name="next-steps"></a>次のステップ

この記事では、GitHub の App Configuration Sync アクションと、それを使用して App Configuration インスタンスの更新を自動化する方法について説明しました。 Azure App Configuration がキーと値のペアの変更に対してどのように反応するかについては、続けて次の[記事](./concept-app-configuration-event.md)をお読みください。
