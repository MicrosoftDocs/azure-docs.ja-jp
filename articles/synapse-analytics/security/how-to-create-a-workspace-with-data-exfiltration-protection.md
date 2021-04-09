---
title: データ流出の防止が有効になっているワークスペースを作成する
description: この記事では、Azure Synapse Analytics でデータ流出の防止機能を備えたワークスペースを作成する方法について説明します
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 59c2f5e5738b29aa11e9227b157f8b11d53f2b25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598084"
---
# <a name="create-a-workspace-with-data-exfiltration-protection-enabled"></a>データ流出の防止が有効になっているワークスペースを作成する
この記事では、データ流出の防止が有効になっているワークスペースを作成する方法と、このワークスペースに対して承認された Azure AD テナントを管理する方法について説明します。

>[!Note]
>ワークスペースの作成後に、マネージド仮想ネットワークとデータ流出の防止のためのワークスペース構成を変更することはできません。

## <a name="prerequisites"></a>[前提条件]
- Azure 内でワークスペース リソースを作成するためのアクセス許可。
- マネージド プライベート エンドポイントを作成するための Synapse ワークスペースのアクセス許可。
- ネットワーク リソース プロバイダー用に登録されているサブスクリプション。 [詳細情報。](../../azure-resource-manager/management/resource-providers-and-types.md)

次の手順に従ってください: 「[クイックスタート: Synapse ワークスペースを作成する](../quickstart-create-workspace.md)」。これにより、ワークスペースの作成を開始します。 ワークスペースを作成する前に、下の情報を使用して、データ流出の防止をワークスペースに追加します。

## <a name="add-data-exfiltration-protection-when-creating-your-workspace"></a>ワークスペースの作成時にデータ流出の防止を追加する
1. [ネットワーク] タブで、[Enable managed virtual network]\(マネージド仮想ネットワークの有効化\) チェックボックスをオンにします。
1. [Allow outbound data traffic only to approved targets]\(承認されたターゲットへの送信データ トラフィックのみを許可する\) オプションで [はい] を選択します。
1. このワークスペースに対して承認された Azure AD テナントを選択します。
1. 構成を確認して、ワークスペースを作成します。
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-creation-data-exfiltration-protection.png" alt-text="[マネージド仮想ネットワークの有効化] オプションが選択されている [Synapse ワークスペースを作成する] が表示されているスクリーンショット。":::

## <a name="manage-approved-azure-active-directory-tenants-for-the-workspace"></a>ワークスペースに対して承認された Azure Active Directory テナントを管理する
1. ワークスペースの Azure portal から、[Approved Azure AD tenants]\(承認された Azure AD テナント\) に移動します。 ワークスペースに対して承認された Azure AD テナントの一覧がこちらに表示されます。 ワークスペースのテナントは既定で含まれているため、一覧にありません。
1. 承認されたリストに新しいテナントを追加するには、[+ 追加] を使用します。
1. Azure AD テナントを承認されたリストから削除するには、テナントを選択し、[削除]、[保存] の順に選択します。
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-manage-aad-tenant-list.png" alt-text="データ流出の防止機能を備えたワークスペースを作成する":::


## <a name="connecting-to-azure-resources-in-approved-azure-ad-tenants"></a>承認された Azure AD テナント内の Azure リソースへの接続

マネージド プライベート エンドポイントを作成して、ワークスペースに対して承認されている Azure AD テナント内に存在する Azure リソースに接続することができます。 [マネージド プライベート エンドポイントの作成](./how-to-create-managed-private-endpoints.md)についてのガイドに記載されている手順に従います。

>[!IMPORTANT]
>ワークスペースのテナント以外のテナント内のリソースには、SQL プールからそれらへの接続をブロックするファイアウォール規則を設定しないでください。 Spark クラスターなどワークスペースのマネージド仮想ネットワーク内のリソースは、マネージド プライベート リンクを介してファイアウォールで保護されたリソースに接続できます。

## <a name="known-limitations"></a>既知の制限事項
ユーザーは、PyPI などのパブリック リポジトリから Python パッケージをインストールするための、環境構成ファイルを提供できます。 データ流失が防止されたワークスペースでは、アウトバウンド リポジトリへの接続がブロックされます。 そのため、PyPI などのパブリック リポジトリからインストールされた Python ライブラリはサポートされません。 

別の方法として、ユーザーは、ワークスペース パッケージをアップロードするか、自分のプライマリ Azure Data Lake Storage アカウント内にプライベート チャネルを作成することができます。 詳細については、[Azure Synapse Analytics でのパッケージ管理](./spark/../../spark/apache-spark-azure-portal-add-libraries.md)に関するページを参照してください。 
  
## <a name="next-steps"></a>次の手順

[Synapse ワークスペースでのデータ流出の防止](./workspace-data-exfiltration-protection.md)の詳細を学習する

[マネージド ワークスペースの Virtual Network](./synapse-workspace-managed-vnet.md) の詳細を学習する

[マネージド プライベート エンドポイント](./synapse-workspace-managed-private-endpoints.md)の詳細を学習する

[データ ソースへのマネージド プライベート エンドポイントを作成する](./how-to-create-managed-private-endpoints.md)
