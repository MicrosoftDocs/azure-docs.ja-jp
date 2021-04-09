---
title: CLI (azdata または kubectl) を使用した Azure Database for PostgreSQL Hyperscale サーバー グループのスケールアップとスケールダウン
description: CLI (azdata または kubectl) を使用した Azure Database for PostgreSQL Hyperscale サーバー グループのスケールアップとスケールダウン
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4461fb6904d51ee8d740b633a2d0028658ac2ced
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687551"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-azdata-or-kubectl"></a>CLI (azdata または kubectl) を使用した Azure Database for PostgreSQL Hyperscale サーバー グループのスケールアップとスケールダウン



場合によっては、サーバー グループの特性または定義の変更が必要になることがあります。 次に例を示します。

- 各コーディネーター ノードまたはワーカー ノードが使用する仮想コアの数をスケールアップまたはスケールダウンします
- 各コーディネーター ノードまたはワーカー ノードが使用するメモリをスケールアップまたはスケールダウンします

このガイドでは、仮想コアやメモリをスケーリングする方法について説明します。

サーバー グループの仮想コアまたはメモリの設定をスケールアップまたはスケールダウンすると、各仮想コアとメモリの設定に対して最小値または最大値を設定できます。 特定の数の仮想コアまたは特定の量のメモリを使用するようにサーバー グループを構成する場合は、最小設定の値を最大設定の値にします。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>サーバー グループの現在の定義を表示する

サーバー グループの現在の定義を表示し、現在の仮想コアとメモリの設定を確認するには、次のコマンドのいずれかを実行します。

### <a name="cli-with-azdata"></a>azdata を使用した CLI

```console
azdata arc postgres server show -n <server group name>
```
### <a name="cli-with-kubectl"></a>kubectl を使用した CLI

```console
kubectl describe postgresql-12/<server group name> [-n <namespace name>]
```
> [!NOTE]
> PostgreSQL バージョン 11 のサーバー グループを作成した場合は、代わりに `kubectl describe postgresql-11/<server group name>` を実行します。

サーバー グループの構成が返されます。 既定の設定でサーバー グループを作成した場合は、次のように定義が表示されます。

```console
"scheduling": {
      "default": {
        "resources": {
          "requests": {
            "memory": "256Mi"
          }
        }
      }
    },
```

## <a name="interpret-the-definition-of-the-server-group"></a>サーバー グループの定義を解釈する

サーバー グループの定義では、ノードあたりの最小/最大仮想コアとノードあたりの最小/最大メモリの設定を含むセクションが "**スケジュール**" セクションです。 このセクションでは、最大設定が "**制限**" と呼ばれるサブセクションに保存されます。最小設定は "**要求**" と呼ばれるサブセクションに保存されます。

最小設定を最大設定とは異なる値にすると、必要に応じて、サーバー グループに要求されたリソースが割り当てられることが保証されます。 設定した制限を超えることはありません。

サーバー グループで実際に使用されるリソース (仮想コアとメモリ) は最大設定までであり、クラスターで使用可能なワークロードとリソースによって異なります。 設定を最大値で制限しない場合、サーバー グループは、スケジュールされている Kubernetes ノードに対してKubernetes クラスターが割り当てているすべてのリソースを使用できます。

これらの仮想コアとメモリの設定は、各 PostgreSQL Hyperscale ノード (コーディネーター ノードとワーカー ノード) に適用されます。 コーディネーター ノードとワーカー ノードの定義を個別に設定することはまだサポートされていません。

既定の構成では、最小メモリのみが 256 Mi に設定されています。これは、PostgreSQL Hyperscale を実行するために推奨される最小メモリ量です。

> [!NOTE]
> 最小値を設定しても、サーバー グループで必ずしもその最小値が使用されるとは限りません。 つまり、サーバー グループに必要な場合は、少なくともこの最小値が割り当てられることが保証されます。 たとえば、`--minCpu 2` の設定を考えてみましょう。 サーバー グループが常に 2 つ以上の仮想コアを使用することを意味するわけではありません。 これは、その必要がない場合にはサーバー グループは 2 つ未満の仮想コアを使用して起動し、後で必要になったときに少なくとも 2 つの仮想コアが割り当てられることが保証されることを意味します。 これは、Kubernetes クラスターが、必要に応じて 2 つの仮想コアをサーバー グループに割り当てることができるように、他のワークロードにリソースを割り当てることを含意します。

>[!NOTE]
>システムの構成を変更する前に、[こちら](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)で Kubernetes リソース モデルについてよく理解していることを確認してください

## <a name="scale-up-the-server-group"></a>サーバー グループをスケールアップする

設定を行う場合は、Kubernetes クラスターに設定した構成内で考慮する必要があります。 Kubernetes クラスターが満たすことができない値を設定していないことを確認します。 これにより、エラーや予期しない動作が発生する可能性があります。 例として、構成を変更した後にサーバー グループの状態が "_更新中_" のままになっている場合は、以下のパラメーターを Kubernetes クラスターが満たすことができない値に設定している可能性があります。 その場合は、変更を元に戻すか、_troubleshooting_section を参照してください。

例として、サーバー グループの定義を次のようにスケールアップするとします。

- 最小仮想コア = 2
- 最大仮想コア = 4
- 最小メモリ = 512 Mb
- 最大メモリ = 1 Gb

次のいずれかの方法を使用します。

### <a name="cli-with-azdata"></a>azdata を使用した CLI

```console
azdata arc postgres server edit -n <name of your server group> --cores-request <# core-request>  --cores-limit <# core-limit>  --memory-request <# memory-request>Mi  --memory-limit <# memory-limit>Mi
```

> [!CAUTION]
> 以下は、このコマンドの使用方法を解説するための例です。 編集コマンドを実行する前に、Kubernetes クラスターで適用できる値にパラメーターを必ず設定してください。

```console
azdata arc postgres server edit -n <name of your server group> --cores-request 2  --cores-limit 4  --memory-request 512Mi  --memory-limit 1024Mi
```

コマンドが正常に実行されると、次のように表示されます。

```console
<name of your server group> is Ready
```

> [!NOTE]
> これらのパラメーターの詳細については、`azdata arc postgres server edit --help` を実行してください。

### <a name="cli-with-kubectl"></a>kubectl を使用した CLI

```console
kubectl edit postgresql-12/<server group name> [-n <namespace name>]
```

これにより、vi エディターが表示されます。ここで、構成の参照や変更を行うことができます。 次のコードを使用して、目的の設定を仕様のフィールド名にマップします。

> [!CAUTION]
> 以下は、この構成の編集方法を解説するための例です。 構成を更新する前に、Kubernetes クラスターで適用できる値にパラメーターを必ず設定してください。

次に例を示します。
- 最小仮想コア = 2 -> scheduling\default\resources\requests\cpu
- 最大仮想コア = 4 -> scheduling\default\resources\limits\cpu
- 最小メモリ = 512 Mb -> scheduling\default\resources\requests\cpu
- 最大メモリ = 1 Gb -> scheduling\default\resources\limits\cpu

vi エディターに慣れていない場合は、[ここで](https://www.computerhope.com/unix/uvi.htm)必要なコマンドの説明を参照してください。
- 編集モード: `i`
- 矢印で移動
- _編集の停止: `esc`
- _保存せずに終了: `:qa!`
- _保存して終了: `:qw!`


## <a name="show-the-scaled-up-definition-of-the-server-group"></a>サーバー グループのスケールアップした定義を表示する

次のコマンドをもう一度実行して、サーバー グループの定義を表示し、必要に応じて設定されていることを確認します。

### <a name="cli-with-azdata"></a>azdata を使用した CLI

```console
azdata arc postgres server show -n <the name of your server group>
```
### <a name="cli-with-kubectl"></a>kubectl を使用した CLI

```console
kubectl describe postgresql-12/<server group name>  [-n <namespace name>]
```
> [!NOTE]
> PostgreSQL バージョン 11 のサーバー グループを作成した場合は、代わりに `kubectl describe postgresql-11/<server group name>` を実行します。


サーバー グループの新しい定義が表示されます。

```console
"scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "2",
            "memory": "512Mi"
          }
        }
      }
    },
```

## <a name="scale-down-the-server-group"></a>サーバー グループをスケールダウンする

サーバー グループをスケールダウンするには、同じコマンドを実行しますが、スケールダウンする設定の値を小さく設定します。 要求または制限を削除するには、その値を空の文字列として指定します。

## <a name="reset-to-default-values"></a>既定値にリセットする
コア/メモリ制限/要求のパラメーターを既定値にリセットするには、それらのパラメーターを編集して、実際の値ではなく空の文字列を渡します。 たとえば、コア制限 (cl) パラメーターをリセットする場合は、次のコマンドを実行します。
- Linux クライアントの場合:

```console
    azdata arc postgres server edit -n <servergroup name> -cl ""
```

- Windows クライアントの場合: 
 
```console
    azdata arc postgres server edit -n <servergroup name> -cl '""'
```


## <a name="next-steps"></a>次のステップ

- [Azure Database for PostgreSQL Hyperscale サーバー グループのスケールアウト](scale-out-postgresql-hyperscale-server-group.md)
- [ストレージの構成と Kubernetes ストレージの概念](storage-configuration.md)
- [Kubernetes リソース モデル](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
