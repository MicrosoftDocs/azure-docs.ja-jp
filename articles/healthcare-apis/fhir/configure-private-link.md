---
title: Azure API for FHIR 用のプライベート リンク
description: この記事では、Azure API for FHIR サービス用のプライベート エンドポイントを設定する方法について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/03/2021
ms.author: zxue
ms.openlocfilehash: c73e11d8f89e50c77b5a140a5f77c749f8a092b6
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019261"
---
# <a name="configure-private-link"></a>プライベート リンクを構成する

プライベート リンクを使用すると、プライベート エンドポイント経由で Azure API for FHIR にアクセスできます。プライベート エンドポイントは、仮想ネットワークのプライベート IP アドレスを使用してプライベートかつ安全に接続するネットワーク インターフェイスです。 プライベート リンクを使用すると、パブリック DNS を経由せずに、Vnet から Microsoft のサービスにファースト パーティ サービスとして安全にアクセスできます。 この記事では、Azure API for FHIR のプライベート エンドポイントを作成、テスト、および管理する方法について説明します。

>[!Note]
>プライベートリンクが有効になっている場合、1つのリソースグループまたはサブスクリプションから別のリソースグループまたはサブスクリプションに、プライベートリンクと Azure API のどちらも移動できません。 移動するには、まずプライベートリンクを削除してから、Azure API for FHIR を移動し、移動が完了したら新しいプライベートリンクを作成します。 プライベートリンクを削除する前に、潜在的なセキュリティの問題を評価します。
>
>FHIR の Azure API で監査ログと/メトリックのエクスポートが有効になっている場合は、ポータルから診断設定を使用してエクスポート設定を更新します。

## <a name="prerequisites"></a>前提条件

プライベート エンドポイントを作成する前に、最初に作成しておく必要がある Azure リソースがいくつかあります。

- リソース グループ – 仮想ネットワークとプライベート エンドポイントを含める Azure リソース グループ。
- Azure API for FHIR – プライベート エンドポイントの背後に配置する FHIR リソース。
- 仮想ネットワーク – クライアント サービスとプライベート エンドポイントが接続される VNet。

詳細については、「[Private Link のドキュメント](../../private-link/index.yml)」を参照してください。

## <a name="disable-public-network-access"></a>パブリック ネットワーク アクセスの無効化

FHIR リソースのプライベート エンドポイントを作成しても、そのエンドポイントへのパブリック トラフィックは自動的には無効になりません。 それを行うには、FHIR リソースを更新して、新しい "パブリック アクセス" プロパティを "有効" から "無効" に設定する必要があります。 パブリック ネットワーク アクセスを無効にすると、適切に構成されたプライベート エンドポイントからのものではない FHIR サービスへのすべての要求が拒否されますので注意してください。 プライベート エンドポイントからのトラフィックのみが許可されます。

![パブリック ネットワーク アクセスの無効化](media/private-link/private-link-disable.png)

## <a name="create-private-endpoint"></a>プライベート エンドポイントの作成

プライベート エンドポイントを作成するために、FHIR リソースに対する RBAC アクセス許可を持つ開発者は、Azure portal、[Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)、または [Azure CLI](../../private-link/create-private-endpoint-cli.md) を使用できます。 この記事では、Azure portal を使用する手順について説明します。 プライベート DNS ゾーンの作成と構成を自動化するため、Azure portal を使用することをお勧めします。 詳細については、「[Private Link クイック スタート ガイド](../../private-link/create-private-endpoint-portal.md)」を参照してください。

プライベート エンドポイントを作成するには、2 つの方法があります。 自動承認フローを使用すると、FHIR リソースに対する RBAC アクセス許可を持つユーザーは、承認を受ける必要なく、プライベート エンドポイントを作成できます。 手動承認フローを使用すると、FHIR リソースに対するアクセス許可を持たないユーザーは、プライベート エンドポイントを FHIR リソースの所有者に承認してもらうよう要求できます。

### <a name="auto-approval"></a>自動承認

新しいプライベート エンドポイントのリージョンが Virtual Network のリージョンと同じであることを確認します。 FHIR リソースのリージョンは異なっていてもかまいません。

![Azure portal の [基本] タブ](media/private-link/private-link-portal2.png)

[リソースの種類] で、「Microsoft.HealthcareApis/services」を検索して選択します。 [リソース] で、[FHIR リソース] を選択します。 [ターゲット サブリソース] で、[fhir] を選択します。

![Azure portal の [リソース] タブ](media/private-link/private-link-portal1.png)

既存のプライベート DNS ゾーンが設定されていない場合は、[(New)privatelink.azurehealthcareapis.com] を選択します。 プライベート DNS ゾーンが既に構成されている場合は、一覧からそれを選択できます。 「privatelink.azurehealthcareapis.com」の形式である必要があります。

![Azure Portal の [構成] タブ](media/private-link/private-link-portal3.png)

デプロイが完了したら、[プライベート エンドポイント接続] タブに戻ります。ここでは、接続状態が "承認済み" と表示されます。

### <a name="manual-approval"></a>手動承認

手動承認の場合は、[リソース] の下の 2 つ目のオプションである [リソース ID またはエイリアスを使って Azure リソースに接続します] を選択します。 [ターゲット サブリソース] で、自動承認と同様に「fhir」と入力します。

![手動承認](media/private-link/private-link-manual.png)

デプロイが完了したら、[プライベート エンドポイント接続] タブに戻ります。ここでは、接続の承認、拒否、または削除を行うことができます。

![オプション](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>プライベート エンドポイントをテストする

パブリック ネットワーク アクセスを無効にした後、FHIR サーバーでパブリック トラフィックが受信されていないことを確認するには、お使いのコンピューターからサーバーの /metadata エンドポイントにアクセスしてみてください。 "403 Forbidden" (403 許可されていません) という応答を受け取るはずです。 パブリック ネットワーク アクセス フラグを更新した後、パブリック トラフィックがブロックされるまでに最大 5 分かかることがあります。

プライベート エンドポイントからサーバーへトラフィックが送信されることを確認するには、次のようにします。

1. プライベート エンドポイントが構成されている仮想ネットワークとサブネットに接続されている VM を作成します。 VM からのトラフィックでプライベート ネットワークのみが使用されていることを確認するには、NSG ルールを使用して送信インターネット トラフィックを無効にすることができます。
2. RDP で VM に接続します。
3. VM から FHIR サーバーの /metadata エンドポイントにアクセスすると、応答として機能ステートメントを受け取るはずです。

## <a name="manage-private-endpoint"></a>マネージド プライベート エンドポイント

### <a name="view"></a>表示

プライベート エンドポイントとそれに関連付けられている NIC は、それらが作成されたリソース グループから Azure portal で確認できます。

![リソースでの表示](media/private-link/private-link-view.png)

### <a name="delete"></a>削除

プライベート エンドポイントは、Azure portal の [概要] ブレード (下記) を使用するか、[ネットワーク (プレビュー)] の [プライベート エンドポイント接続] タブにある [削除] オプションを使用する方法でのみ削除できます。[削除] ボタンをクリックすると、プライベート エンドポイントとそれに関連付けられている NIC が削除されます。 FHIR リソースへのすべてのプライベート エンドポイントを削除し、パブリック ネットワーク アクセスが無効になっている場合、FHIR サーバーに対する要求は行われません。

![プライベート エンドポイントの削除](media/private-link/private-link-delete.png)
