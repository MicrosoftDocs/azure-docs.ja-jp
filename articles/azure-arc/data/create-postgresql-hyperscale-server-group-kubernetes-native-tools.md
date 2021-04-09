---
title: Kubernetes ツールを使用して PostgreSQL Hyperscale サーバー グループを作成する
description: Kubernetes ツールを使用して PostgreSQL Hyperscale サーバー グループを作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: fd1b74d33793c06e586a92cc8b2e8d2d36f4827a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519964"
---
# <a name="create-a-postgresql-hyperscale-server-group-using-kubernetes-tools"></a>Kubernetes ツールを使用して PostgreSQL Hyperscale サーバー グループを作成する

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>前提条件

[Azure Arc データ コントローラー](./create-data-controller.md)が既に作成されている必要があります。

Kubernetes ツールを使用して PostgreSQL Hyperscale サーバー グループを作成するには、Kubernetes ツールがインストールされている必要があります。  この記事の例では `kubectl` を使用しますが、Kubernetes ダッシュボード、`oc`、`helm` などの Kubernetes の他のツールや Kubernetes yaml/json を使い慣れている場合は、それらのツールでも同様のアプローチを使用できます。

[kubectl ツールをインストールする](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>概要

PostgreSQL Hyperscale サーバー グループを作成するには、postgres 管理者のログインとパスワードを安全に格納する Kubernetes シークレットを作成し、postgresql-12 または postgresql-11 カスタム リソース定義に基づいて、PostgreSQL Hyperscale サーバー グループのカスタム リソースを作成する必要があります。

## <a name="create-a-yaml-file"></a>yaml ファイルを作成する

[テンプレート yaml](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/postgresql.yaml) ファイルを出発点として使用して、PostgreSQL Hyperscale サーバー グループのカスタム yaml ファイルを独自に作成することができます。  このファイルをローカル コンピューターにダウンロードし、テキスト エディターで開きます。  yaml ファイルの構文の強調表示とリンティングがサポートされている [VS Code](https://code.visualstudio.com/download) などのテキスト エディターを使用すると便利です。

次に、yaml ファイルの例を示します。

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
kind: Secret
metadata:
  name: pg1-login-secret
type: Opaque
---
apiVersion: arcdata.microsoft.com/v1alpha1
kind: postgresql-12
metadata:
  generation: 1
  name: pg1
spec:
  engine:
    extensions:
    - name: citus
  scale:
    shards: 3
  scheduling:
    default:
      resources:
        limits:
          cpu: "4"
          memory: 4Gi
        requests:
          cpu: "1"
          memory: 2Gi
  service:
    type: LoadBalancer
  storage:
    backups:
      className: default
      size: 5Gi
    data:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>ログインとパスワードのカスタマイズ。
Kubernetes シークレットは base64 でエンコードされた文字列として格納されます。1 つはユーザー名、1 つはパスワード用です。  管理者のログインとパスワードを base64 でエンコードし、それをプレースホルダーの場所 `data.password` と `data.username` に配置する必要があります。  テンプレートで使用されている `<` と `>` の記号は含めないでください。

オンライン ツールを使用して、希望するユーザー名とパスワードを base64 でエンコードできます。また、プラットフォームに応じて組み込みの CLI ツールを使用することもできます。

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

### <a name="customizing-the-name"></a>名前のカスタマイズ

このテンプレートには、name 属性の値として "pg1" が指定されています。  これを変更できますが、DNS 名前付け標準に従う文字を使用する必要があります。  また、シークレットの名前も一致するように変更する必要があります。  たとえば、PostgreSQL Hyperscale サーバー グループの名前を "pg2" に変更した場合は、シークレットの名前を "pg1-login-secret" から "pg2-login-secret" に変更する必要があります。

### <a name="customizing-the-engine-version"></a>エンジン バージョンのカスタマイズ

`kind` 属性を編集することによって、エンジン バージョンを postgresql-11 または postgresql-12 のいずれかに変更できます。

### <a name="customizing-the-resource-requirements"></a>リソース要件のカスタマイズ

必要に応じて、RAM とコアの制限および要求のリソース要件を変更できます。  

> [!NOTE]
> 詳細については、[Kubernetes リソースのガバナンス](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)に関する記事を参照してください。

リソースの制限と要求の要件:
- コア数の制限値は、課金のために **必要** です。
- それ以外のリソースの要求と制限は省略可能です。
- コア数の制限と要求を指定する場合は、正の整数値にする必要があります。
- コア数の要求を指定する場合は、少なくとも 1 コアを指定する必要があります。
- メモリ値の形式は、Kubernetes の表記に従います。  

### <a name="customizing-service-type"></a>サービスの種類のカスタマイズ

必要に応じて、サービスの種類を NodePort に変更できます。  ランダムなポート番号が割り当てられます。

### <a name="customizing-storage"></a>ストレージのカスタマイズ

ストレージのストレージ クラスを環境に合わせてカスタマイズできます。  使用可能なストレージ クラスがわからない場合は、コマンド `kubectl get storageclass` を実行して表示できます。  テンプレートの既定値は "default" です。  これは、既定値 "_として設定された_" ストレージ クラスがあるということではなく、"default" という "_名前が付けられた_" ストレージ クラスがあることを意味します。  必要に応じて、ストレージのサイズを変更することもできます。  詳細については、「[ストレージの構成](./storage-configuration.md)」を参照してください。

## <a name="creating-the-postgresql-hyperscale-server-group"></a>PostgreSQL Hyperscale サーバー グループの作成

ここまで PostgreSQL Hyperscale サーバー グループの yaml ファイルをカスタマイズしたので、次のコマンドを実行して PostgreSQL Hyperscale サーバー グループを作成できます。

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\postgres.yaml
```


## <a name="monitoring-the-creation-status"></a>作成状態の監視

PostgreSQL Hyperscale サーバー グループの作成が完了するまで数分間かかります。 次のコマンドを使用して、別のターミナル ウィンドウで進行状況を監視できます。

> [!NOTE]
>  下のコマンド例では、"pg1" という名前の PostgreSQL Hyperscale サーバー グループと、"arc" という名前の Kubernetes 名前空間を作成していることを前提としています。  異なる名前空間や PostgreSQL Hyperscale サーバー グループ名を使用した場合は、"arc" と "pg1" を実際の名前に置き換えることができます。

```console
kubectl get postgresql-12/pg1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

次のようなコマンドを実行して、特定のポッドの作成状態を確認することもできます。  これは特に、何らの問題をトラブルシューティングするのに役立ちます。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/pg1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>作成の問題のトラブルシューティング

作成で問題が発生した場合は、「[トラブルシューティング ガイド](troubleshoot-guide.md)」を参照してください。