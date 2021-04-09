---
title: Azure Arc 対応データ サービス - 既知の問題
description: 最新の既知の問題
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: 8100d9e12f107e0c4598876c46453b46c6ee4d0e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122002"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>既知の問題 - Azure Arc 対応データ サービス (プレビュー)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>2021 年 2 月

- 接続されたクラスター モードが無効です

## <a name="introduced-prior-to-february-2021"></a>2021 年 2 月より前に確認されたもの

### <a name="data-controller"></a>データ コントローラー

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

### <a name="postgresql"></a>PostgreSQL

- Azure Arc 対応 PostgreSQL Hyperscale では、指定した相対時点まで復元できない場合に、不正確なエラー メッセージが返されます。 たとえば、バックアップに含まれる内容よりも古い特定の時点に復元するよう指定した場合、復元は失敗し、`ERROR: (404). Reason: Not found. HTTP response body: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}` のようなエラー メッセージが表示されます。
これが発生した場合は、バックアップが存在する日付範囲内の特定の時点を指定した後、コマンドを再始動します。 この範囲を特定するには、バックアップを一覧表示し、それが取得された日付を確認します。
- ポイントインタイム リストアは、サーバー グループ間でのみサポートされます。 ポイントインタイム リストア操作の対象サーバーに、バックアップ取得元のサーバーを指定することはできません。 これは、別のサーバー グループである必要があります。 ただし、完全復元は同じサーバー グループに対してサポートされています。
- 完全復元を実行する際は、backup-id が必要です。 既定では、backup-id を指定していない場合は、最新のバックアップが使用されます。 これは、このリリースでは機能しません。

## <a name="next-steps"></a>次のステップ

> **試してみたい場合**  
> AKS、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) をすぐに開始できます。

- [クライアント ツールをインストールする](install-client-tools.md)
- [Azure Arc データ コントローラーを作成する](create-data-controller.md) (先にクライアント ツールをインストールする必要があります)
- [Azure Arc で Azure SQL マネージド インスタンスを作成する](create-sql-managed-instance.md) (先に Azure Arc データ コントローラーを作成する必要があります)
- [Azure Arc に Azure Database for PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md) (先に Azure Arc データ コントローラーを作成する必要があります)
- [Azure サービスのリソース プロバイダー](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [リリースノート - Azure Arc 対応データ サービス (プレビュー)](release-notes.md)
