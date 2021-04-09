---
title: Azure Arc で Azure SQL Managed Instance を作成する
description: Azure Arc で Azure SQL Managed Instance を作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0d1d6c7349adc65270e13d09d771963cb9cacea3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93280403"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>Azure Arc で Azure SQL Managed Instance を作成する

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="login-to-the-azure-arc-data-controller"></a>Azure Arc データ コントローラーにログインする

Azure Arc データ コントローラーにまだログインしていない場合は、インスタンスを作成する前にログインします。

```console
azdata login
```

その後、ユーザー名、パスワード、システム名前空間を入力するように求められます。  

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-an-azure-sql-managed-instance"></a>Azure SQL マネージド インスタンスの作成

SQL Managed Instance で使用できる作成オプションを表示するには、次のコマンドを使用します。
```console
azdata arc sql mi create --help
```

SQL Managed Instance を作成するには、次のコマンドを使用します。

```console
azdata arc sql mi create -n <instanceName> --storage-class-data <storage class> --storage-class-logs <storage class>
```

例:

```console
azdata arc sql mi create -n sqldemo --storage-class-data managed-premium --storage-class-logs managed-premium
```
> [!NOTE]
>  名前は、13 文字未満の長さで入力し、[DNS 名前付け規則](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names)に準拠している必要があります。
>
>  メモリの割り当てと仮想コアの割り当てを指定する場合は、この数式を使用して正常に作成されたことを確認します。1 つの仮想コアごとに、SQL Managed Instance ポッドが実行される Kubernetes ノードで使用可能な RAM の容量が少なくとも 4 GB 必要です。
>
>  SQL インスタンスの作成時に、Azure でプロビジョニングを行う場合は、名前に大文字を使用しないでください。
>
>  Kubernetes クラスターで使用可能なストレージ クラスを一覧表示するには、`kubectl get storageclass` を実行します。 


> [!NOTE]
> SQL インスタンスの作成を自動化し、管理者パスワードを求める対話型プロンプトが表示されないようにするには、`azdata arc sql mi create` コマンドを実行する前に、`AZDATA_USERNAME` および `AZDATA_PASSWORD` 環境変数を目的のユーザー名とパスワードに設定します。
> 
>  同じターミナル セッションで AZDATA_USERNAME と AZDATA_PASSWORD を使用してデータ コントローラーを作成した場合、その AZDATA_USERNAME と AZDATA_PASSWORD の値は SQL Managed Instance の作成にも使用されます。

> [!NOTE]
> Azure SQL Managed Instance を作成しても、リソースは Azure に登録されません。 リソースを登録する手順については、次の記事を参照してください。 
> - [Kibana と Grafana を使用してログとメトリックを表示する](monitor-grafana-kibana.md)
> - [課金データを Azure にアップロードして Azure portal に表示する](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>Azure Arc でインスタンスを表示する

インスタンスを表示するには、次のコマンドを使用します。

```console
azdata arc sql mi list
```

出力は次のようになります。

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

AKS、`kubeadm`、OpenShift などを使用している場合は、ここから外部 IP とポート番号をコピーし、Azure Data Studio や SQL Server Management Studio などの SQL Server/Azure SQL インスタンスに接続するためのお気に入りのツールを使用して接続できます。 ただし、クイックスタート VM を使用している場合は、「[Azure Arc 対応 SQL Managed Instance に接続する](connect-managed-instance.md)」という記事を参照して、特別な指示を確認してください。


## <a name="next-steps"></a>次のステップ
- [Azure Arc 対応 SQL Managed Instance に接続する](connect-managed-instance.md)
- [インスタンスを Azure に登録し、インスタンスに関するメトリックとログをアップロードする](upload-metrics-and-logs-to-azure-monitor.md)
- [Azure Data Studio を使用して Azure SQL Managed Instance をデプロイする](create-sql-managed-instance-azure-data-studio.md)
