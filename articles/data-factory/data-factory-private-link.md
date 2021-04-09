---
title: Azure Data Factory 用の Azure Private Link
description: Azure Data Factory 内での Azure Private Link の機能について説明します。
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 9e4d686f582a202dbc543620c7bf73dc4e7adb22
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389180"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Data Factory 用の Azure Private Link

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure Private Link を使用すると、プライベート エンドポイント経由で Azure のさまざまなサービスとしてのプラットフォーム (PaaS) のデプロイに接続できます。 プライベート エンドポイントは、特定の仮想ネットワークおよびサブネット内のプライベート IP アドレスです。 Private Link 機能をサポートしている PaaS デプロイの一覧については、「[Private Link のドキュメント](../private-link/index.yml)」を参照してください。 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>カスタマー ネットワークと Azure Data Factory 間の通信をセキュリティで保護する 
クラウド内のネットワークを論理的に表すように Azure 仮想ネットワークをセットアップできます。 これにより、次のような利点があります。
* パブリック ネットワークでの攻撃から Azure リソースを保護することができます。
* ネットワークと Data Factory が相互に安全に通信できるようになります。 

また、インターネット プロトコル セキュリティ (IPsec) VPN (サイト間) 接続または Azure ExpressRoute (プライベート ピアリング) 接続をセットアップすることによって、オンプレミス ネットワークをご利用の仮想ネットワークに接続することもできます。 

オンプレミスのマシンまたは仮想ネットワーク内の仮想マシンに、セルフホステッド統合ランタイムをインストールすることもできます。 これにより、次のことが可能になります。
* クラウドのデータ ストアとプライベート ネットワーク内のデータ ストアの間でコピー アクティビティを実行する。
* オンプレミス ネットワークまたは Azure Virtual Network 内のコンピューティング リソースに対して変換アクティビティをディスパッチする。 

次の表に示すように、Azure Data Factory とお客様の仮想ネットワークの間にはいくつかの通信チャネルが必要です。

| Domain | Port | 説明 |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | コントロール プレーン。Data Factory の作成と監視に必要です。 |
| `*.{region}.datafactory.azure.net` | 443 | セルフホステッド統合ランタイムが Data Factory サービスに接続するために必要です。 |
| `*.servicebus.windows.net` | 443 | セルフホステッド統合ランタイムがインタラクティブな作成を行うために必要です。 |
| `download.microsoft.com` | 443 | セルフホステッド統合ランタイムが更新プログラムをダウンロードするために必要です。 |

Azure Data Factory で Private Link がサポートされていると、次のことを行うことができます。
* 仮想ネットワーク内にプライベート エンドポイントを作成する。
* 特定の Data Factory インスタンスへのプライベート接続を有効にする。 

Azure Data Factory サービスへの通信は、Private Link を経由し、セキュリティで保護されたプライベート接続が提供されます。 

![Azure Data Factory アーキテクチャの Private Link の図。](./media/data-factory-private-link/private-link-architecture.png)

上記の各通信チャネルに対して Private Link サービスを有効にすると、次の機能が提供されます。
- **サポート対象**:
   - 送信方向の通信をすべてブロックした場合でも、ご利用の仮想ネットワーク内でデータ ファクトリの作成と監視を行うことができます。
   - セルフホステッド統合ランタイムと Azure Data Factory サービス間のコマンド通信をプライベート ネットワーク環境内で安全に実行できます。 セルフホステッド統合ランタイムと Azure Data Factory サービス間のトラフィックは Private Link を経由します。 
- **現在、サポートされていません**:
   - テスト接続、フォルダー リストやテーブル リストの参照、スキーマの取得、データのプレビューなど、セルフホステッド統合ランタイムを使用したインタラクティブな作成が Private Link を経由します。
   - 自動更新を有効にした場合、セルフホステッド統合ランタイムの新しいバージョンを Microsoft ダウンロード センターから自動的にダウンロードできます。

   > [!NOTE]
   > 現在サポートされていない機能については、引き続き、前述の仮想ネットワーク内のドメインとポートまたは企業ファイアウォールを構成する必要があります。 

   > [!NOTE]
   > プライベート エンドポイント経由の Azure Data Factory への接続は、データ ファクトリのセルフホステッド統合ランタイムにのみ適用されます。 Synapse ではサポートされません。

> [!WARNING]
> リンクされたサービスを作成する場合は、必ず資格情報を Azure Key Vault 内に格納してください。 そうしないと、Azure Data Factory 内で Private Link サービスを有効にしても、資格情報が機能しません。

## <a name="dns-changes-for-private-endpoints"></a>プライベート エンドポイントの DNS の変更
プライベート エンドポイントを作成すると、Data Factory の DNS CNAME リソース レコードは、プレフィックス "privatelink" を持つサブドメイン内のエイリアスに更新されます。 既定では、"privatelink" サブドメインに対応する[プライベート DNS ゾーン](../dns/private-dns-overview.md)も作成されます。これには、プライベート エンドポイントの DNS A リソース レコードが含まれます。

プライベート エンドポイントを持つ VNet の外部からデータ ファクトリのエンドポイント URL を解決すると、データ ファクトリ サービスのパブリック エンドポイントに解決されます。 プライベート エンドポイントをホストしている VNet から解決されると、ストレージ エンドポイント URL はプライベート エンドポイントの IP アドレスに解決されます。

上の図の例のように、プライベート エンドポイントをホストしている VNet の外部から解決されると、Data Factory の 'DataFactoryA' の DNS リソース レコードは次のようになります。

| 名前 | Type | 値 |
| ---------- | -------- | --------------- |
| DataFactoryA.{region}.datafactory.azure.net | CNAME   | DataFactoryA.{region}.privatelink.datafactory.azure.net |
| DataFactoryA.{region}.privatelink.datafactory.azure.net | CNAME   | <データ ファクトリ サービスのパブリック エンドポイント> |
| <データ ファクトリ サービスのパブリック エンドポイント>  | A | <データ ファクトリ サービスのパブリック IP アドレス> |

DataFactoryA の DNS リソース レコードは、プライベート エンドポイントをホストしている VNet 内で解決されると、次のようになります。

| 名前 | Type | 値 |
| ---------- | -------- | --------------- |
| DataFactoryA.{region}.datafactory.azure.net | CNAME   | DataFactoryA.{region}.privatelink.datafactory.azure.net |
| DataFactoryA.{region}.privatelink.datafactory.azure.net   | A | <プライベート エンドポイントの IP アドレス> |

ネットワーク上でカスタム DNS サーバーを使用している場合、クライアントが Data Factory エンドポイントの FQDN をプライベート エンドポイントの IP アドレスに解決できる必要があります。 プライベート リンク サブドメインを VNet のプライベート DNS ゾーンに委任するように DNS サーバーを構成するか、プライベート エンドポイントの IP アドレスを使用して "DataFactoryA.{region}.privatelink.datafactory.azure.net" の A レコードを構成する必要があります。

プライベート エンドポイントをサポートするように独自の DNS サーバーを構成する方法の詳細については、次の記事を参照してください。
- [Azure 仮想ネットワーク内のリソースの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-overview.md#dns-configuration)


## <a name="set-up-private-link-for-azure-data-factory"></a>Azure Data Factory 用の Private Link をセットアップする
プライベート エンドポイントは、[Azure portal](../private-link/create-private-endpoint-portal.md) を使用して作成できます。

パブリック エンドポイントまたはプライベート エンドポイントを使用して、セルフホステッド統合ランタイムを Azure Data Factory に接続するかどうかを選択できます。 

![セルフホステッド統合ランタイムのパブリック アクセスをブロックしているスクリーンショット。](./media/data-factory-private-link/disable-public-access-shir.png)


また、次に示すように、Azure portal でご利用の Azure Data Factory に移動し、プライベート エンドポイントを作成することもできます。

![プライベート エンドポイントを作成するための [プライベート エンドポイント接続] ペインのスクリーンショット。](./media/data-factory-private-link/create-private-endpoint.png)

**[リソース]** の手順で、 **[リソースの種類]** として **[Microsoft.Datafactory/factories]** を選択します。 また、セルフホステッド統合ランタイムと Azure Data Factory サービス間のコマンド通信用にプライベート エンドポイントを作成する場合は、 **[ターゲット サブリソース]** として **[datafactory]** を選択します。

![リソースを選択するための [プライベート エンドポイント接続] ペインのスクリーンショット。](./media/data-factory-private-link/private-endpoint-resource.png)

> [!NOTE]
> パブリック ネットワーク アクセスの無効化は、Azure Integration Runtime および SQL Server Integration Services (SSIS) Integration Runtime ではなく、セルフホステッド統合ランタイムにのみ適用されます。

仮想ネットワーク内でデータ ファクトリを作成および監視するためのプライベート エンドポイントを作成する場合は、 **[Target sub-resource]\(ターゲット サブリソース\)** として **[ポータル]** を選択します。

> [!NOTE]
> ポータルのプライベート エンドポイントを作成した後も、ユーザーはパブリック ネットワークを介して Azure Data Factory ポータルにアクセスできます。

## <a name="next-steps"></a>次のステップ

- [Azure Data Factory UIを使用してData Factoryを作成する](quickstart-create-data-factory-portal.md)
- [Azure Data Factory の概要](introduction.md)
- [Azure Data Factory でのビジュアルの作成](author-visually.md)