---
title: Azure Arc 対応データ サービス - リリース ノート
description: 最新のリリース ノート
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: 6b4d5c1372a8351f1fe5a6608aff38bf232aabd8
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121951"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>リリースノート - Azure Arc 対応データ サービス (プレビュー)

この記事では、Azure Arc 対応データ サービスで最近リリースされた、または強化された機能と機能強化について説明します。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>2021 年 2 月

### <a name="new-capabilities-and-features"></a>新機能

Azure データ CLI (`azdata`) バージョン番号: 20.3.1。 [https://aka.ms/azdata](https://aka.ms/azdata) でダウンロードします。 `azdata` は、「[Azure Data CLI (`azdata`) のインストール](/sql/azdata/install/deploy-install-azdata)」に従ってインストールできます。

追加の更新プログラムには以下が含まれます。

- Azure Arc 対応 SQL Managed Instance
   - Always On 可用性グループによる高可用性

- Azure Arc 対応 PostgreSQL Hyperscale Azure Data Studio: 
   - 概要ページに、ノードごとのサーバー グループの状態が表示されるようになりました
   - 新しいプロパティ ページを使用してサーバー グループに関する詳細を表示できるようになりました
   - **[ノード パラメーター]** ページから Postgres エンジン パラメーターを構成します

このリリースに関連する問題については、「[既知の問題 - Azure Arc 対応データ サービス (プレビュー)](known-issues.md)」を参照してください

## <a name="january-2021"></a>2021 年 1 月

### <a name="new-capabilities-and-features"></a>新機能

Azure データ CLI (`azdata`) バージョン番号:20.3.0。 [https://aka.ms/azdata](https://aka.ms/azdata) でダウンロードします。 `azdata` は、「[Azure Data CLI (`azdata`) のインストール](/sql/azdata/install/deploy-install-azdata)」に従ってインストールできます。

追加の更新プログラムには以下が含まれます。
- 17 の新しい言語で使用できるローカライズされたポータル
- Kube ネイティブの .yaml ファイルに対する軽微な変更
- 新しいバージョンの Grafana と Kibana
- Azure Data Studio のノートブックで azdata を使用する場合の Python 環境に関する問題が解決されました
- pg_audit 拡張機能を PostgreSQL Hyperscale で使用できるようになりました
- PostgreSQL Hyperscale データベースの完全復元を実行する場合、バックアップ ID は不要になりました
- サーバー グループを構成する PostgreSQL インスタンスごとに状態 (正常性状態) が報告されます

   以前のリリースでは、状態はサーバー グループ レベルで集計され、PostgreSQL ノード レベルでは項目別にまとめられませんでした。

- PostgreSQL のデプロイでは、create コマンドに指定されたボリューム サイズのパラメーターが受け入れられるようになりました
- サーバー グループを編集するときに、エンジンのバージョン パラメーターが受け入れられるようになりました
- Azure Arc 対応 PostgreSQL Hyperscale のポッドの名前付け規則が変更されました

    現在は `ServergroupName{c, w}-n` の形式になっています。 たとえば、3 つのノード (1 つのコーディネーター ノードと 2 つのワーカー ノード) を持つサーバー グループは次のように表されます。
   - `Postgres01c-0` (コーディネーター ノード)
   - `Postgres01w-0` (ワーカー ノード)
   - `Postgres01w-1` (ワーカー ノード)

## <a name="december-2020"></a>2020 年 12 月

### <a name="new-capabilities--features"></a>新機能

Azure データ CLI (`azdata`) バージョン番号:20.2.5。 [https://aka.ms/azdata](https://aka.ms/azdata) でダウンロードします。

Azure Data CLI (`azdata`) で `azdata arc sql endpoint list` および `azdata arc postgres endpoint list` コマンドを使用すると、SQL Managed Instance および PostgreSQL Hyperscale の各エンドポイントが表示されます。

Azure Data Studio を使用して、SQL Managed Instance リソース (CPU コアとメモリ) の要求および制限を編集できます。

Azure Arc 対応 PostgreSQL Hyperscale では、PostgreSQL のバージョン 11 と 12 の両方に対して、完全バックアップの復元に加えて、ポイントインタイム リストアがサポートされるようになりました。 ポイントインタイム リストア機能を使用すると、復元する特定の日付と時刻を指定できます。

Azure Arc 対応 PostgreSQL Hyperscale のポッドの名前付け規則が変更されました。 現在は、ServergroupName{r, s}-_n_ の形式になっています。 たとえば、3 つのノード (1 つのコーディネーター ノードと 2 つのワーカー ノード) を持つサーバー グループは次のように表されます。
- `postgres02r-0` (コーディネーター ノード)
- `postgres02s-0` (ワーカー ノード)
- `postgres02s-1` (ワーカー ノード)

### <a name="breaking-change"></a>互換性に影響する変更点

#### <a name="new-resource-provider"></a>新しいリソース プロバイダー

このリリースでは、`Microsoft.AzureArcData` という名称の更新された[リソース プロバイダー](../../azure-resource-manager/management/azure-services-resource-providers.md)が導入されました。 この機能を使用するには、事前にこのリソース プロバイダーを登録する必要があります。 

リソース プロバイダーを登録するには、次のようにします。 

1. Azure portal で、 **[サブスクリプション]** を選択します 
2. サブスクリプションの選択
3. **[設定]** で、 **[リソース プロバイダー]** を選択します 
4. `Microsoft.AzureArcData` を検索し、 **[登録]** を選択します 

詳細な手順については [「Azure リソースプロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。 また、この変更により、Azure portal にアップロードした既存のすべての Azure リソースが削除されます。 このリソース プロバイダーを使用するには、データ コントローラーを更新し、最新の `azdata` CLI を使用する必要があります。  

### <a name="platform-release-notes"></a>プラットフォームのリリース ノート

#### <a name="direct-connectivity-mode"></a>直接接続モード

このリリースでは、直接接続モードが導入されます。 直接接続モードでは、データ コントローラーによって使用状況の情報を Azure に自動的にアップロードできます。 使用状況のアップロードの一環として、Arc データ コントローラー リソースがポータルに自動的に作成されます (`azdata` アップロードによって既に作成されていない場合)。  

データ コントローラーを作成するときに、直接接続を指定できます。 次の例では、直接接続モード (`connectivity-mode direct`) を使用して `arc` という名前のデータ コントローラーを `azdata arc dc create` によって作成します。 例を実行する前に、`<subscription id>` をお使いのサブスクリプション ID に置き換えてください。

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

### <a name="known-issues"></a>既知の問題

- Azure Kubernetes Service (AKS) では、Kubernetes バージョン 1.19.x はサポートされていません。
- Kubernetes 1.19 では、`containerd` はサポートされていません。
- Azure 内のデータ コントローラー リソースは現在、Azure リソースです。 削除などの更新は、kubernetes クラスターには反映されません。
- インスタンス名は 13 文字より長くすることはできません
- Azure Arc データ コントローラーまたはデータベース インスタンスのインプレース アップグレードはありません。
- Arc 対応データ サービスのコンテナー イメージは署名されていません。  署名されていないコンテナー イメージをプルできるように Kubernetes ノードを構成することが必要な場合があります。  たとえば、Docker をコンテナー ランタイムとして使用している場合、DOCKER_CONTENT_TRUST=0 環境変数を設定して再起動することができます。  他のコンテナー ランタイムには、[OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration) などの同様のオプションがあります。
- Azure Arc 対応の SQL Managed インスタンスまたは PostgreSQL Hyperscale サーバー グループは、Azure portal からは作成できません。
- 現時点では、NFS を使用する場合は、Azure Arc データ コントローラーを作成する前に、デプロイ プロファイル ファイルで `allowRunAsRoot` を `true` に設定する必要があります。
- SQL および PostgreSQL ログイン認証のみ。  Azure Active Directory または Active Directory はサポートされていません。
- OpenShift にデータ コントローラーを作成するには、セキュリティ制約の緩和が必要です。  詳細についてはドキュメントを参照してください。
- Azure Arc データ コントローラーとデータベース インスタンスがある Azure Stack Hub 上で Azure Kubernetes Service (AKS) エンジンを使用している場合、より新しい Kubernetes バージョンへのアップグレードはサポートされていません。 Kubernetes クラスターをアップグレードする前に、Azure Arc データ コントローラーとすべてのデータベース インスタンスをアンインストールしてください。
- [複数の可用性ゾーン](../../aks/availability-zones.md)にまたがる AKS クラスターは現在、Azure Arc 対応データ サービスではサポートされていません。 この問題を回避するには、Azure portal で AKS クラスターを作成するときに、ゾーンを使用できるリージョンを選択した場合は、選択コントロールからすべてのゾーンをクリアします。 次の図を参照してください。

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="各ゾーンのチェックボックスをオフにして、[なし] を指定します。":::

#### <a name="postgresql"></a>PostgreSQL

- Azure Arc 対応 PostgreSQL Hyperscale では、指定した相対時点まで復元できない場合に、不正確なエラー メッセージが返されます。 たとえば、バックアップに含まれる内容よりも古い特定の時点に復元するよう指定した場合、復元は失敗し、次のようなエラー メッセージが表示されます。エラー: (404)。 理由:見つかりません。 HTTP 応答の本文: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}
これが発生した場合は、バックアップが存在する日付範囲内の特定の時点を指定した後、コマンドを再始動します。 この範囲を特定するには、バックアップを一覧表示し、それが取得された日付を確認します。
- ポイントインタイム リストアは、サーバー グループ間でのみサポートされます。 ポイントインタイム リストア操作の対象サーバーに、バックアップ取得元のサーバーを指定することはできません。 これは、別のサーバー グループである必要があります。 ただし、完全復元は同じサーバー グループに対してサポートされています。
- 完全復元を実行する際は、backup-id が必要です。 既定では、backup-id を指定していない場合は、最新のバックアップが使用されます。 これは、このリリースでは機能しません。

## <a name="october-2020"></a>2020 年 10 月 

Azure データ CLI (`azdata`) バージョン番号:20.2.3。 [https://aka.ms/azdata](https://aka.ms/azdata) でダウンロードします。

### <a name="breaking-changes"></a>重大な変更

このリリースでは、次のような破壊的変更が導入されました。 

* PostgreSQL カスタム リソース定義 (CRD) で、用語 `shards` が `workers` に名称変更されています。 この用語 (`workers`) は、コマンド ライン パラメーター名と一致します。

* `azdata arc postgres server delete` では、postgres インスタンスを削除する前に確認を求めるメッセージが表示されます。  プロンプトをスキップするには `--force` を使用します。

### <a name="additional-changes"></a>追加の変更

* `--volume-claim mounts` という新しい省略可能パラメーターが `azdata arc postgres server create` に追加されました。 値は、ボリューム要求マウントのコンマ区切りの一覧です。 ボリューム要求マウントは、ボリュームの種類と PVC 名のペアです。 現在サポートされているボリュームの種類は `backup` のみです。  PostgreSQL では、ボリュームの種類が `backup` の場合、PVC は `/mnt/db-backups` にマウントされます。  これにより、PostgresSQL インスタンス間でバックアップを共有でき、1 つの PostgresSQL インスタンスのバックアップを別のインスタンスで復元できるようになります。

* PostgresSQL カスタム リソース定義の新しい短い名前: 
  * `pg11` 
  * `pg12`
* テレメトリのアップロードでは、次のいずれかがユーザーに提供されます。
   * Azure にアップロードされたポイントの数、または 
   * Azure にデータが読み込まれていない場合は、再試行するためのプロンプト。
* `azdata arc dc debug copy-logs` では、`/var/opt/controller/log` フォルダーからの読み取りと、Linux での PostgreSQL エンジン ログの収集も行われるようになりました。
*   PostgreSQL Hyperscale を使用してバックアップを作成および復元するときに、作業インジケーターを表示します。
* `azdata arc postrgres backup list` にバックアップ サイズの情報が含まれるようになりました。
* SQL Managed Instance 管理者名プロパティが Azure portal の概要ブレードの右側の列に追加されました。
* Azure Data Studio では、PostgreSQL Hyperscale のワーカー ノードの数、仮想コア、メモリ設定の構成がサポートされます。 
* プレビューで、Postgres バージョン 11 と 12 のバックアップ/復元がサポートされます。

## <a name="september-2020"></a>2020 年 9 月

Azure Arc 対応データ サービスは、パブリック プレビュー用にリリースされています。 Arc 対応データ サービスを使用すると、どこからでもデータ サービスを管理できます。

- SQL Managed Instance
- PostgreSQL Hyperscale

手順については「[Azure Arc 対応データ サービスとは](overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

> **試してみたい場合**  
> AKS、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) をすぐに開始できます。

- [クライアント ツールをインストールする](install-client-tools.md)
- [Azure Arc データ コントローラーを作成する](create-data-controller.md) (先にクライアント ツールをインストールする必要があります)
- [Azure Arc で Azure SQL マネージド インスタンスを作成する](create-sql-managed-instance.md) (先に Azure Arc データ コントローラーを作成する必要があります)
- [Azure Arc に Azure Database for PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md) (先に Azure Arc データ コントローラーを作成する必要があります)
- [Azure サービスのリソース プロバイダー](../../azure-resource-manager/management/azure-services-resource-providers.md)
