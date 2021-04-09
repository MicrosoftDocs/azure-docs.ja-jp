---
title: Kubernetes ツールを使用して SQL マネージド インスタンスを作成する
description: Kubernetes ツールを使用して SQL マネージド インスタンスを作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: d23df80a3f80ed96779297bac12ef0ed8d2927d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687925"
---
# <a name="create-azure-sql-managed-instance-using-kubernetes-tools"></a>Kubernetes ツールを使用して Azure SQL マネージド インスタンスを作成する

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>前提条件

[Azure Arc データ コントローラー](./create-data-controller.md)が既に作成されている必要があります。

Kubernetes ツールを使用して SQL マネージド インスタンスを作成するには、Kubernetes ツールがインストールされている必要があります。  この記事の例では `kubectl` を使用しますが、Kubernetes ダッシュボード、`oc`、`helm` などの Kubernetes の他のツールや Kubernetes yaml/json を使い慣れている場合は、それらのツールでも同様のアプローチを使用できます。

[kubectl ツールをインストールする](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>概要

SQL マネージド インスタンスを作成するには、システム管理者のログインとパスワードを安全に格納するための Kubernetes シークレットと、sqlmanagedinstance カスタム リソース定義に基づいて SQL マネージド インスタンスのカスタム リソースを、作成する必要があります。

## <a name="create-a-yaml-file"></a>yaml ファイルを作成する

[テンプレート yaml](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/sqlmi.yaml) ファイルを出発点として使用して、独自のカスタム SQL マネージド インスタンスの yaml ファイルを作成することができます。  このファイルをローカル コンピューターにダウンロードし、テキスト エディターで開きます。  yaml ファイルの構文の強調表示とリンティングがサポートされている [VS Code](https://code.visualstudio.com/download) などのテキスト エディターを使用すると便利です。

次に、yaml ファイルの例を示します。

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
  username: <your base64 encoded user name. 'sa' is not allowed>
kind: Secret
metadata:
  name: sql1-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
  name: sql1
spec:
  limits:
    memory: 4Gi
    vcores: "4"
  requests:
    memory: 2Gi
    vcores: "1"
  service:
    type: LoadBalancer
  storage:
    data:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>ログインとパスワードのカスタマイズ

Kubernetes シークレットは base64 でエンコードされた文字列として格納されます。1 つはユーザー名、1 つはパスワード用です。  システム管理者のログインとパスワードを base64 でエンコードし、プレースホルダーの場所 `data.password` と `data.username` に配置する必要があります。  テンプレートで使用されている `<` と `>` の記号は含めないでください。

> [!NOTE]
> 最適なセキュリティを実現するため、ログインに値 "sa" を使用することはできません。
> [パスワードの複雑性のポリシー](/sql/relational-databases/security/password-policy#password-complexity)に関する記事に従ってください。

オンライン ツールを使用して、希望するユーザー名とパスワードを base64 でエンコードできます。また、プラットフォームに応じて組み込みの CLI ツールを使用することもできます。

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

### <a name="customizing-the-name"></a>名前のカスタマイズ

テンプレートにおいては、name 属性の値が "sql1" になっています。  これを変更できますが、DNS 名前付け標準に従う文字を使用する必要があります。  また、シークレットの名前も一致するように変更する必要があります。  たとえば、SQL マネージド インスタンスの名前を "sql2" に変更した場合は、シークレットの名前を "sql1-login-secret" から "sql2-login-secret" に変更する必要があります。

### <a name="customizing-the-resource-requirements"></a>リソース要件のカスタマイズ

必要に応じて、RAM とコアの制限および要求のリソース要件を変更できます。  

> [!NOTE]
> 詳細については、[Kubernetes リソースのガバナンス](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)に関する記事を参照してください。

リソースの制限と要求の要件:
- コア数の制限値は、課金のために **必要** です。
- それ以外のリソースの要求と制限は省略可能です。
- コア数の制限と要求を指定する場合は、正の整数値にする必要があります。
- コア数の要求を指定する場合は、2 コア以上を指定する必要があります。
- メモリ値の形式は、Kubernetes の表記に従います。  
- メモリ要求を指定する場合は、2 Gi 以上にする必要があります。
- 一般的なガイドラインとして、運用環境のユース ケースの場合は、1 コアごとに 4 GB の RAM が必要です。

### <a name="customizing-service-type"></a>サービスの種類のカスタマイズ

必要に応じて、サービスの種類を NodePort に変更できます。  ランダムなポート番号が割り当てられます。

### <a name="customizing-storage"></a>ストレージのカスタマイズ

ストレージのストレージ クラスを環境に合わせてカスタマイズできます。  使用可能なストレージ クラスがわからない場合は、コマンド `kubectl get storageclass` を実行して表示できます。  テンプレートの既定値は "default" です。  これは、既定値 "_として設定された_" ストレージ クラスがあるということではなく、"default" という "_名前が付けられた_" ストレージ クラスがあることを意味します。  必要に応じて、ストレージのサイズを変更することもできます。  詳細については、「[ストレージの構成](./storage-configuration.md)」を参照してください。

## <a name="creating-the-sql-managed-instance"></a>SQL マネージド インスタンスの作成

SQL マネージド インスタンスの yaml ファイルをカスタマイズしたので、次のコマンドを実行して SQL マネージド インスタンスを作成できます。

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\sqlmi.yaml
```


## <a name="monitoring-the-creation-status"></a>作成状態の監視

SQL マネージド インスタンスの作成が完了するまでに数分かかります。 次のコマンドを使用して、別のターミナル ウィンドウで進行状況を監視できます。

> [!NOTE]
>  次のコマンド例では、"sql1" という名前の SQL マネージド インスタンスと、"arc" という名前の Kubernetes 名前空間が作成されていることを前提としています。  名前空間または SQL マネージド インスタンスに異なる名前を使用している場合は、"arc" と "sqlmi" をその名前に置き換えることができます。

```console
kubectl get sqlmi/sql1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

次のようなコマンドを実行して、特定のポッドの作成状態を確認することもできます。  これは特に、何らの問題をトラブルシューティングするのに役立ちます。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/sql1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>作成の問題のトラブルシューティング

作成で問題が発生した場合は、「[トラブルシューティング ガイド](troubleshoot-guide.md)」を参照してください。

## <a name="next-steps"></a>次の手順

[Azure Arc 対応 SQL Managed Instance に接続する](connect-managed-instance.md)
