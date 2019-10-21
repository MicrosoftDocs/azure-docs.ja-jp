---
title: az spring cloud
description: Azure CLI を使用して Azure Spring Cloud を管理します
author: jpconnock
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: jeconnoc
ms.openlocfilehash: c0694bf53f4a0644c8da2b50660dbfd6a5b339c7
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038710"
---
# <a name="az-spring-cloud"></a>az spring-cloud

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Azure CLI を使用して Azure Spring Cloud を管理する

>[!Note]
> Azure Spring Cloud は現在プレビューの段階です。  これらのコマンドは、今後のリリースで変更または削除される可能性があります。

| az spring-cloud |  |
|------|------:|
| [az spring-cloud create](#az-spring-cloud-create) | Azure Spring Cloud のインスタンスを作成します。 |
| [az spring-cloud delete](#az-spring-cloud-delete) | Azure Spring Cloud のインスタンスを削除します。 |
| [az spring-cloud list](#az-spring-cloud-list) | 指定したリソース グループ内のすべての Azure Spring Cloud インスタンスを一覧表示します。それ以外の場合は、サブスクリプション ID を一覧表示します。 |
| [az spring-cloud show](#az-spring-cloud-show) | Azure Spring Cloud の詳細を表示します。 |

| az spring-cloud app | Azure Spring Cloud でアプリを管理するコマンド。  |
| ---- | ----: |
| [az spring-cloud app create](#az-spring-cloud-app-create) | Azure Spring Cloud で既定のデプロイを使用して新しいアプリを作成します。 |
| [az spring-cloud app delete](#az-spring-cloud-app-delete) | Azure Spring Cloud のアプリを削除します。 |
| [az spring-cloud app deploy](#az-spring-cloud-app-deploy) | ソース コードまたはビルド済みバイナリからアプリにデプロイし、関連する構成を更新します。 |
| [az spring-cloud app list](#az-spring-cloud-app-list) | Azure Spring Cloud のすべてのアプリを一覧表示します。 |
| [az spring-cloud app restart](#az-spring-cloud-app-restart) | 運用環境のデプロイの既定値を使用して、アプリのインスタンスを再起動します。 |
| [az spring-cloud app scale](#az-spring-cloud-app-scale) | アプリまたはそのデプロイを手動でスケーリングします。 |
| [az spring-cloud app set-deployment](#az-spring-cloud-app-set-deployment) | アプリの運用環境のデプロイを設定します。 |
| [az spring-cloud app show](#az-spring-cloud-app-show) | Azure Spring Cloud のアプリの詳細を表示します。 |
| [az spring-cloud app show-deploy-log](#az-spring-cloud-app-show-deploy-log) | ソースからの最新のデプロイのビルド ログを表示します。 既定値は運用環境のデプロイです。 |
| [az spring-cloud app start](#az-spring-cloud-app-start) | 運用環境のデプロイの既定値を使用して、アプリのインスタンスを起動します。 |
| [az spring-cloud app stop](#az-spring-cloud-app-stop) | 運用環境のデプロイの既定値を使用して、アプリのインスタンスを停止します。 |
| [az spring-cloud app update](#az-spring-cloud-app-update) | 指定したアプリの構成を更新します。 |

| az spring-cloud app binding | Azure Data Services でバインドを管理するコマンド。  これらの設定を有効にするには、アプリを再起動する必要があります。 |
| --- | ---: |
| [az spring-cloud app binding list](#az-spring-cloud-app-binding-list) | アプリ内のすべてのサービス バインドを一覧表示します。 |
| [az spring-cloud app binding remove](#az-spring-cloud-app-binding-remove) | アプリからサービス バインドを削除します。 |
| [az spring-cloud app binding show](#az-spring-cloud-app-binding-show) | サービス バインドの詳細を表示します。 |
| [az spring-cloud app binding cosmos add](#az-spring-cloud-app-binding-cosmos-add) | Azure CosmosDB とアプリをバインドします。 |
| [az spring-cloud app binding cosmos update](#az-spring-cloud-app-binding-cosmos-update) | Azure CosmosDB のサービス バインドを更新します。 |
| [az spring-cloud app binding mysql add](#az-spring-cloud-app-binding-mysql-add) | Azure Database for MySQL とアプリをバインドします。 |
| [az spring-cloud app binding mysql update](#az-spring-cloud-app-binding-mysql-update) | Azure Database for MySQL のサービス バインドを更新します。 |
| [az spring-cloud app binding redis add](#az-spring-cloud-app-binding-redis-add) | Azure Cache for Redis とアプリをバインドします。 |
| [az spring-cloud app binding redis update](#az-spring-cloud-app-binding-redis-update) | Azure Cache for Redis のサービス バインドを更新します。 |

| az spring-cloud app deployment | Azure Spring Cloud でアプリのデプロイ ライフ サイクルを管理するコマンド。 |
| --- | ---: |
| [az spring-cloud app deployment create](#az-spring-cloud-app-deployment-create) | アプリのステージング環境のデプロイを作成します。 |
| [az spring-cloud app deployment delete](#az-spring-cloud-app-deployment-delete) | アプリのデプロイを削除します。 |
| [az spring-cloud app deployment list](#az-spring-cloud-app-deployment-list) | アプリのすべてのデプロイを一覧表示します。 |
| [az spring-cloud app deployment show](#az-spring-cloud-app-deployment-show) | デプロイの詳細を表示します。 |

| az spring-cloud config-server | Azure Spring Cloud の構成サーバーを管理するコマンド。 |
| --- | ---: |
| [az spring-cloud config-server clear](#az-spring-cloud-config-server-clear) | 構成サーバーのすべての設定を消去します。 |
| [az spring-cloud config-server set](#az-spring-cloud-config-server-set) | YAML ファイルから構成サーバーを定義します。 |
| [az spring-cloud config-server show](#az-spring-cloud-config-server-show) | 構成サーバーの構成を表示します。 |
| [az spring-cloud config server git set](#az-spring-cloud-config-server-git-set) | 構成サーバーの Git プロパティを定義します。  前の値は上書きされます。 |
| [az spring-cloud config server git repo add](#az-spring-cloud-config-server-git-repo-add) | 新しい Git リポジトリの構成を構成サーバーに追加します。 |
| [az spring-cloud config server git repo list](#az-spring-cloud-config-server-git-repo-list) | 構成サーバーのすべての Git リポジトリの構成を一覧表示します。 |
| [az spring-cloud config server git repo remove](#az-spring-cloud-config-server-git-repo-remove) | 指定した Git リポジトリを構成サーバーから削除します。 |

| az spring-cloud test-endpoint | Azure Spring Cloud でエンドポイントのテストを管理するコマンド |
| --- | ---: |
| [az spring-cloud test-endpoint disable](#az-spring-cloud-test-endpoint-disable) | テスト エンドポイントを無効にします。 |
| [az spring-cloud test-endpoint enable](#az-spring-cloud-test-endpoint-enable) | テスト エンドポイントを有効にします。 |
| [az spring-cloud test-endpoint list](#az-spring-cloud-test-endpoint-list) | テスト エンドポイント キーを一覧表示します。 |
| [az spring-cloud test-endpoint renew-key](#az-spring-cloud-test-endpoint-renew-key) | テスト エンドポイント キーを再生成します。 |

## <a name="az-spring-cloud-create"></a>az spring-cloud create

Azure Spring Cloud で既定のデプロイを使用して新しいアプリを作成します。

```cli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| 必須のパラメーター | |
| --- | ---: |
| --name -n | この Azure Spring Cloud インスタンスの名前。 |
| --resource-group -g | このアプリのリソース グループを指定します。  `az configure --defaults group=<name>` を使用して、既定のグループを構成します。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --location -l | このアプリのサーバーの場所を指定します。  有効な場所を探すには `az account list-locations` を使用します |
| --no-wait | 実行時間の長い操作の完了を待機しません。

### <a name="examples"></a>例

WestUS に新しい Azure Spring Cloud を作成します

```cli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az spring-cloud delete

Azure Spring Cloud のインスタンスを削除します。

```cli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| 必須のパラメーター | |
| --- | ---: |
| --name -n | 削除する Azure Spring Cloud インスタンスの名前。 |
| --resource-group -g | Azure Spring Cloud が属するリソース グループの名前。 |

| 省略可能なパラメーター | |
| --- | ---: |
| -no-wait | 実行時間の長い操作の完了を待機しません。 |

### <a name="example"></a>例

"MyResourceGroup" から "MyService" という名前の Azure Spring Cloud インスタンスを削除します。

```cli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>az spring-cloud list

指定したリソース グループに関連付けられているすべての Azure Spring Cloud インスタンスを一覧表示します。 リソース グループを指定しないと、サブスクリプション ID の一覧が表示されます。

```cli
az spring-cloud list --resource-group -g
```

| 必須のパラメーター | |
| --- | ---: |
| --resource-group -g | リソース グループの名前。 |

## <a name="az-spring-cloud-show"></a>az spring-cloud show

指定した Azure Spring Cloud インスタンスの詳細を表示します。

```cli
az spring-cloud show --name -n
                     -- resource-group -g
```

| 必須のパラメーター | |
| --- | ---: |
| --name -n | Azure Spring Cloud インスタンスの名前。 |
| --resource-group -g | Azure Spring Cloud インスタンスが属するリソース グループの名前。

## <a name="az-spring-cloud-app-create"></a>az spring-cloud app create

Azure Spring Cloud に新しいアプリを作成します。

```cli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| 必須のパラメーター | |
| --- | ---: |
| --name -n | アプリの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --cpu | インスタンスあたりの仮想コアの数。  既定値は1. |
| --enable-persistent-storage | ブール値。  true の場合、既定のパスに 50 GB のディスクをマウントします。 |
| --instance-count | インスタンスの数。  既定値は1. |
| --is-public | ブール値。  true の場合、パブリック ドメインを割り当てます。 |
| --memory | インスタンスあたりのメモリの GB 数。  既定値は1. |

### <a name="examples"></a>例

既定の構成でアプリを作成します。

```cli
az spring-cloud app create -n MyApp -s MyService
```

3 つのインスタンスで、パブリックにアクセス可能なアプリを作成します。  各インスタンスには、3 GB のメモリと 2 つの CPU コアがあります。

```cli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az spring-cloud app delete

Azure Spring Cloud のアプリを削除します。

```cli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| 必須のパラメーター | |
| --- | ---: |
| --name -n | アプリの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

## <a name="az-spring-cloud-app-deploy"></a>az spring-cloud app deploy

ソース コードまたはビルド済みバイナリから Azure Spring Cloud にアプリをデプロイし、関連する構成を更新します。

```cli
az spring cloud app deploy  --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --deployment -d
                            --env
                            --instance-count
                            --jar-path
                            --jvm-options
                            --memory
                            --no-wait
                            --runtime-version
                            --target-module
                            --version
```

| 必須のパラメーター | |
| --- | ---: |
| --name -n | アプリの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --cpu | インスタンスあたりの仮想 CPI コアの数。 |
| --deployment -d | 既存のアプリのデプロイの名前。  指定しないと、既定で運用環境のデプロイになります。 |
| --env | スペースで区切られた "key[=value]" 形式の環境変数。 |
| --instance-count | インスタンスの数。 |
| --jar-path | 指定した場合、指定したパスから jar をデプロイします。 それ以外の場合は、現在のフォルダーを tar としてデプロイします。 |
| --jvm-options | JVM オプションが含まれる文字列。  シェル解析エラーを回避するため、" " ではなく "=" を使用します。 例: `--jvm-options='-Xms1024m -Xmx2048m`。 |
| --memory | インスタンスあたりのメモリの GB 数。 |
| --no-wait | 実行時間の長い操作の完了を待機しません。 |
| --runtime-version | アプリで使用される言語のランタイム バージョン。  使用できる値: `Java_11`、`Java_8`。 |
| --target-module | デプロイする子モジュール。  ソース コードから複数の jar パッケージがビルドされる場合に必要です。 |
| --version | デプロイのバージョン。  設定しないと、変更されません。 |

### <a name="examples"></a>例

ソース コードをアプリにデプロイします。 これにより、現在のディレクトリがパックされ、Pivotal Build Service を使用してバイナリがビルドされた後、アプリにデプロイされます。

```cli
az spring-cloud app deploy -n MyApp -s MyService
```

JVM オプションと環境変数を使用して、ビルド済み jar をアプリにデプロイします。

```cli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

アプリの特定のデプロイにソース コードをデプロイします。

```cli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>az spring-cloud app list

Azure Spring Cloud インスタンスのすべてのアプリを一覧表示します。

```cli
az spring-cloud app list --resource-group -g
                         --service -s
```

|必須のパラメーター | |
| --- | ---: |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

## <a name="az-spring-cloud-app-restart"></a>az spring-cloud app restart

アプリのインスタンスを再起動します。  既定値は運用環境のデプロイです。

```cli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| 必須のパラメーター | |
| --- | ---: |
| --name -n | アプリの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --deployment -d | アプリの既存のデプロイの名前。  指定しないと、既定で運用環境のデプロイになります。 |
| --no-wait | 実行時間の長い操作の完了を待機しません。 |

## <a name="az-spring-cloud-app-scale"></a>az spring-cloud app scale

アプリまたはそのデプロイを手動でスケーリングします。

```cli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| 必須のパラメーター | |
| --- | ---: |
| --name -n | アプリの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --cpu | アプリ インスタンスあたりの仮想 CPU コアの数。 |
| --deployment -d | アプリの既存のデプロイの名前。  指定しないと、既定で運用環境のデプロイになります。 |
| --instance-count | このアプリのインスタンスの数。 |
| --memory | アプリ インスタンスあたりのメモリの GB 数。 |
| --no-wait | 実行時間の長い操作の完了を待機しません。 |

### <a name="examples"></a>例

インスタンスあたり 4 CPU コアと 8 GB メモリに、アプリをスケールアップします。

```cli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

アプリのデプロイを 5 インスタンスにスケールアウトします。

```cli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az spring-cloud app set-deployment

アプリの運用環境のデプロイの構成オプションを設定します。

```cli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| 必須のパラメーター | |
| --- | ---: |
| --deployment -d | アプリの既存のデプロイの名前。 |
| --name -n | アプリの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --no-wait | 実行時間の長い操作の完了を待機しません。 |

### <a name="examples"></a>例

アプリのステージング環境のデプロイを運用環境にスワップします。

```cli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>az spring-cloud app show

Azure Spring Cloud のアプリの詳細を表示します。

```cli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| 必須のパラメーター | |
| --- | ---: |
| --name -n | アプリの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az spring-cloud app show-deploy-log

ソース コードからの最後のデプロイのビルド ログを表示します。  既定値は運用環境です。

```cli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| 必須のパラメーター | |
| --- | ---: |
| --name -n | アプリの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --deployment -d | アプリの既存のデプロイの名前。  既定値は運用環境です。 |

## <a name="az-spring-cloud-app-start"></a>az spring-cloud app start

アプリのインスタンスを起動します。  既定値は運用環境です。

```cli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| 必須のパラメーター | |
| --- | ---: |
| --name -n | アプリの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --deployment -d | アプリの既存のデプロイの名前。  既定値は運用環境です。 |
| --no-wait | 実行時間の長い操作の完了を待機しません。 |

## <a name="az-spring-cloud-app-stop"></a>az spring-cloud app stop

アプリのインスタンスを停止します。  既定値は運用環境です。

```cli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| 必須のパラメーター | |
| --- | ---: |
| --name -n | アプリの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --deployment -d | アプリの既存のデプロイの名前。  既定値は運用環境です。 |
| --no-wait | 実行時間の長い操作の完了を待機しません。 |

## <a name="az-spring-cloud-app-update"></a>az spring-cloud app update

アプリの格納されている構成を更新します。

```cli
az spring-cloud app update --name -n
                           --resource-group -g
                           --service -s
                           --deployment -d
                           --enable-persistent-storage
                           --env
                           --is-public
                           --jvm-options
                           --no-wait
                           --runtime-version
```

| 必須のパラメーター | |
| --- | ---: |
| --name -n | アプリの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --deployment -d | アプリの既存のデプロイの名前。  既定値は運用環境です。 |
| --enable-persistent-storage | ブール値。  true の場合、既定のパスに 50 GB のディスクをマウントします。 |
| --env | スペースで区切られた "key[=value]" 形式の環境変数。 |
| --is-public | ブール値。  true の場合、アプリにパブリック ドメインを割り当てます。 |
| --jvm-options | JVM オプションが含まれる文字列。  シェル解析エラーを回避するため、" " ではなく "=" を使用します。 例: `--jvm-options='-Xms1024m -Xmx2048m`。 |
| --no-wait | 実行時間の長い操作の完了を待機しません。 |
| --runtime-version | アプリで使用される言語のランタイム バージョン。  使用できる値: `Java_11`、`Java_8`。 |

### <a name="example"></a>例

アプリの環境変数を追加します。

```cli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>az spring-cloud app binding list

アプリ内のすべてのサービス バインドを一覧表示します。

```cli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| 必須のパラメーター | |
| --- | ---: |
| --app | アプリの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

## <a name="az-spring-cloud-app-binding-remove"></a>az spring-cloud app binding remove

アプリからサービス バインドを削除します。

```cli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 必須のパラメーター | |
| --- | ---: |
| --app | アプリの名前。 |
| --name | 削除するサービス バインドの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

## <a name="az-spring-cloud-app-binding-show"></a>az spring-cloud app binding show

サービス バインドの詳細を表示します。

```cli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 必須のパラメーター | |
| --- | ---: |
| --app | アプリの名前。 |
| --name | サービス バインドの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az spring-cloud app binding cosmos add

Azure Cosmos DB とアプリをバインドします。

```cli
az spring-cloud app binding cosmos add --api-type
                                       --app
                                       --name -n
                                       --resource-group -g
                                       --resource-id
                                       --service -s
                                       --collection-name
                                       --database-name
                                       --key-space
```

```cli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 必須のパラメーター | |
| --- | ---: |
| --api-type | 次のいずれかの値を使用して、API の種類を指定します: cassandra、gremlin、mongo、sql、table。 |
| --app | アプリの名前。 |
| --name | サービス バインドの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

|省略可能なパラメーター | |
| --- | ---: |
| --collection-name | コレクションの名前。  Gremlin を使用するときに必要です。 |
| --database-name | データベースの名前。  Mongo、SQL、Gremlin を使用するときに必要です。 |
| --key-space | Cassandra のキー スペース。  Cassandra を使用するときに必要です。 |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az spring-cloud app binding cosmos update

```cli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| 必須のパラメーター | |
| --- | ---: |
| --app | アプリの名前。 |
| --name | サービス バインドの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

|省略可能なパラメーター | |
| --- | ---: |
| --collection-name | コレクションの名前。  Gremlin を使用するときに必要です。 |
| --database-name | データベースの名前。  Mongo、SQL、Gremlin を使用するときに必要です。 |
| --key-space | Cassandra のキー スペース。  Cassandra を使用するときに必要です。 |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az spring-cloud app binding mysql add

```cli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| 必須のパラメーター | |
| --- | ---: |
| --app | アプリの名前。 |
| --database-name | データベースの名前。 |
| --key | サービスの API キー。 |
| --name | サービス バインドの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --resource-id | バインドするサービスの Azure リソース ID。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |
| --username | データベース アクセスのユーザー名。 |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az spring-cloud app binding mysql update

Azure Database for MySQL へのアプリのサービス バインド接続を更新します。

```cli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| 必須のパラメーター | |
| --- | ---: |
| --app | アプリの名前。 |
| --name | サービス バインドの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --database-name | データベースの名前。 |
| --key | サービスの API キー。 |
| --username | データベース アクセスのユーザー名。 |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az spring-cloud app binding redis add

Azure Cache for Redis とアプリをバインドします。

```cli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| 必須のパラメーター | |
| --- | ---: |
| --app | アプリの名前。 |
| --name | サービス バインドの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --resource-id | バインドするサービスの Azure リソース ID。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --disable-ssl | SSL を無効にします。 |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az spring-cloud app binding redis update

Azure Cache for Redis に対するサービス バインドを更新します。

| 必須のパラメーター | |
| --- | ---: |
| --app | アプリの名前。 |
| --name | サービス バインドの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --disable-ssl | SSL を無効にします。 |

## <a name="az-spring-cloud-app-deployment-create"></a>az spring-cloud app deployment create

アプリのステージング環境のデプロイを作成します。

コードをデプロイしたり、既存のデプロイの設定を更新したりするには、`az spring-cloud app deploy --deployment <staging-deployment>` または "az spring-cloud app update --deployment <staging deployment>" を使用します。

| 必須のパラメーター | |
| --- | ---: |
| --app | アプリの名前。 |
| --name | サービス バインドの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --cpu | インスタンスあたりの仮想 CPU コアの数。  既定値は1 |
| --env | スペースで区切られた "key[=value]" 形式の環境変数。 |
| --instance-count | インスタンスの数。 既定値は1. |
| --jar-path | 指定した場合、jar をデプロイします。  それ以外の場合は、現在のフォルダーを tar としてデプロイします。 |
| --jvm-options | JVM オプションが含まれる文字列。  シェル解析エラーを回避するため、" " ではなく "=" を使用します。 例: `--jvm-options='-Xms1024m -Xmx2048m`。 |
| --memory | インスタンスあたりのメモリの GB 数。 |
| --no-wait | 実行時間の長い操作の完了を待機しません。 |
| --runtime-version | アプリで使用される言語のランタイム バージョン。  使用できる値: `Java_11`、`Java_8`。 |
| --skip-clone-settings | 現在の運用環境のデプロイの設定を複製して、ステージング環境のデプロイを作成します。 |
| --target-module | デプロイする子モジュール。  ソース コードから複数の jar パッケージがビルドされる場合に必要です。 |
| --version | デプロイのバージョン。  設定しないと、変更されません。 |

### <a name="examples"></a>例

アプリの新しいデプロイにソース コードをデプロイします。  これにより、現在のディレクトリがパックされ、Pivotal Build System を使用してバイナリがビルドされた後、デプロイされます。

```cli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

JVM オプションと環境変数を使用して、ビルド済み jar をアプリにデプロイします。

```cli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>az spring-cloud app deployment delete

アプリのデプロイを削除します。

```cli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| 必須のパラメーター | |
| --- | ---: |
| --app | アプリの名前。 |
| --name | デプロイの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

## <a name="az-spring-cloud-app-deployment-list"></a>az spring-cloud app deployment list

アプリのすべてのデプロイを一覧表示します。

```cli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| 必須のパラメーター | |
| --- | ---: |
| --app | アプリの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

## <a name="az-spring-cloud-app-deployment-show"></a>az spring-cloud app deployment show

デプロイの詳細を表示します。

```cli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| 必須のパラメーター | |
| --- | ---: |
| --app | アプリの名前。 |
| --name | デプロイの名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --service -s | Azure Spring Cloud の名前。  `az configure --defaults spring-cloud=<name>` を使用すると、既定のサービスを構成できます。 |

## <a name="az-spring-cloud-config-server-clear"></a>az spring-cloud config-server clear

構成サーバーのすべての構成設定を消去します。

```cli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| 必須のパラメーター | |
| --- | ---: |
| --name | Azure Spring Cloud の名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |

## <a name="az-spring-cloud-config-server-set"></a>az spring-cloud config-server set

YAML ファイルを使用して、構成サーバーの構成設定を設定します。

```cli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| 必須のパラメーター | |
| --- | ---: |
| --config-file | 構成サーバーの構成に対する YAML マニフェストへのファイル パス。 |
| --name | Azure Spring Cloud の名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --no-wait | 実行時間の長い操作の完了を待機しません。

## <a name="az-spring-cloud-config-server-show"></a>az spring-cloud config-server show

構成サーバーの設定を表示します。

```cli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| 必須のパラメーター | |
| --- | ---: |
| --name | Azure Spring Cloud の名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |

## <a name="az-spring-cloud-config-server-git-set"></a>az spring-cloud config-server git set

構成サーバーの Git プロパティを設定します。  これにより、既存のすべての Git プロパティが上書きされます。

```cli
az spring-cloud config-server git set --name -n
                                      --resource-group g
                                      --uri
                                      --defer
                                      --host-key
                                      --host-key-algorithm
                                      --label
                                      --password
                                      --private-key
                                      --search-paths
                                      --strict-host-key-checking
                                      --username
```

| 必須のパラメーター | |
| --- | ---: |
| --name | Azure Spring Cloud の名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --uri | 追加される構成の URI。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --defer | オブジェクトを Azure に送信するのではなく、ローカル キャッシュに一時的に格納します。  表示/クリアするには `az cache` を使用します。 |
| --host-key | 追加される構成のホスト キー。 |
| --host-key-algorithm | 追加される構成のホスト キー アルゴリズム。 |
| --label | 追加される構成のラベル。 |
| --password | 追加される構成のパスワード。 |
| --private-key | 追加される構成の秘密キー。 |
| --search-paths | 追加される構成の検索パス。複数のパスにはコンマ区切り記号を使用します。 |
| --strict-host-key-checking | 追加される構成の厳密なホスト キー チェックを有効にします。 |
| --username | 追加される構成のユーザー名。 |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az spring-cloud config-server git repo add

```cli
az spring-cloud config-server git repo add --name -n
                                           --repo-name
                                           --resource-group -g
                                           --uri
                                           --defer
                                           --host-key
                                           --host-key-algorithm
                                           --label
                                           --password
                                           --pattern
                                           --private-key
                                           --search-paths
                                           --strict-host-key-checking
                                           --username
```

| 必須のパラメーター | |
| --- | ---: |
| --name | Azure Spring Cloud の名前。 |
| --repo-name | リポジトリの URI。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --uri | 追加される構成の URI。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --defer | オブジェクトを Azure に送信するのではなく、ローカル キャッシュに一時的に格納します。  表示/クリアするには `az cache` を使用します。 |
| --host-key | 追加される構成のホスト キー。 |
| --host-key-algorithm | 追加される構成のホスト キー アルゴリズム。 |
| --label | 追加される構成のラベル。 |
| --password | 追加される構成のパスワード。 |
| --pattern | リポジトリのパターン。  複数のパスにはコンマ区切り記号を使用します。|
| --private-key | 追加される構成の秘密キー。 |
| --search-paths | 追加される構成の検索パス。複数のパスにはコンマ区切り記号を使用します。 |
| --strict-host-key-checking | 追加される構成の厳密なホスト キー チェックを有効にします。 |
| --username | 追加される構成のユーザー名。 |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az spring-cloud config-server git repo list

構成サーバーで定義されているすべての Git リポジトリを一覧表示します

```cli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| 必須のパラメーター | |
| --- | ---: |
| --name | Azure Spring Cloud の名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --defer | オブジェクトを Azure に送信するのではなく、ローカル キャッシュに一時的に格納します。  表示/クリアするには `az cache` を使用します。 |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az spring-cloud config-server git repo remove

構成サーバーから既存の Git リポジトリ構成を削除します。

```cli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| 必須のパラメーター | |
| --- | ---: |
| --name | Azure Spring Cloud の名前。 |
| --repo-name | リポジトリの URI。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --defer | オブジェクトを Azure に送信するのではなく、ローカル キャッシュに一時的に格納します。  表示/クリアするには `az cache` を使用します。 |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az spring-cloud test-endpoint disable

Azure Spring Cloud のテスト エンドポイントを無効にします

```cli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| 必須のパラメーター | |
| --- | ---: |
| --name | Azure Spring Cloud の名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az spring-cloud test-endpoint enable

Azure Spring Cloud のテスト エンドポイントを有効にします。 

```cli 
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| 必須のパラメーター | |
| --- | ---: |
| --name | Azure Spring Cloud の名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |

## <a name="az-spring-cloud-test-endpoint-list"></a>az spring-cloud test-endpoint list 

Azure Spring Cloud で使用可能なテスト エンドポイント キーを一覧表示します。

```cli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| 必須のパラメーター | |
| --- | ---: |
| --name | Azure Spring Cloud の名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |

| 省略可能なパラメーター | |
| --- | ---: |
| --app | アプリの名前。 |
| --deployment -d | アプリの既存のデプロイの名前。  指定しない場合、既定値は運用環境です。 |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az spring-cloud test-endpoint renew-key

Azure Spring Cloud のテスト エンドポイント キーを再生成します。

```cli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| 必須のパラメーター | |
| --- | ---: |
| --name | Azure Spring Cloud の名前。 |
| --resource-group -g | リソース グループの名前。  `az configure --defaults group=<name>` を使用して、既定のグループを構成できます。 |
| --type | テスト エンドポイント キーの種類。  使用できる値は以下の通りです。Primary、Secondary。 |
