---
title: VNET での Azure Spring Cloud の実行に関するお客様の責任
description: この記事では、VNET での Azure Spring Cloud の実行に関するお客様の責任について説明します。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0c73d0394486472c2c3c92450aab6a1a0d329cf7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877657"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>VNET での Azure Spring Cloud の実行に関するお客様の責任
このドキュメントには、仮想ネットワークで Azure Spring Cloud を使用するための仕様が含まれています。

仮想ネットワークにデプロイする場合、Azure Spring Cloud には、仮想ネットワークの外部にあるサービスへの送信依存関係があります。 管理と運用上の目的から、Azure Spring Cloud は特定のポートと完全修飾ドメイン名 (FQDN) にアクセスする必要があります。 これらのエンドポイントは、Azure Spring Cloud 管理プレーンと通信したり、コア Kubernetes クラスター コンポーネントとセキュリティ更新プログラムをダウンロードしてからインストールしたりするために必要です。

既定で、Azure Spring Cloud は、送信 (エグレス) インターネット アクセスが無制限です。 このレベルのネットワーク アクセスでは、実行しているアプリケーションから必要に応じて外部リソースにアクセスできます。 エグレス トラフィックを制限する場合は、メンテナンス タスクに対して、アクセスできるポートとアドレスの数を制限する必要があります。 送信アドレスをセキュリティで保護する最も簡単なソリューションは、ドメイン名に基づいて送信トラフィックを制御できるファイアウォール デバイスを使用することです。 たとえば、Azure Firewall では、送信先の FQDN に基づいて HTTP と HTTPS の送信トラフィックを制限できます。 また、適切なファイアウォール規則とセキュリティ規則を構成し、これらの必要なポートとアドレスを許可することができます。

## <a name="azure-spring-cloud-resource-requirements"></a>Azure Spring Cloud のリソース要件 

Azure Spring Cloud サービスのリソース要件の一覧を次に示します。 一般的な要件として、Azure Spring Cloud によって作成されたリソース グループおよび基になるネットワーク リソースを変更することはできません。
- Azure Spring Cloud によって作成および所有されているリソース グループは変更しないでください。
  - 既定では、これらのリソース グループは、ap-svc-rt_[SERVICE-INSTANCE-NAME] _[REGION]* および ap_[SERVICE-INSTANCE-NAME]_[REGION]* という名前になっています。
- Azure Spring Cloud で使用されるサブネットを変更しないでください。
- 同じサブネット内に複数の Azure Spring Cloud サービス インスタンスを作成しないでください。
- ファイアウォールを使用してトラフィックを制御する場合は、サービス インスタンスを運用、保守、およびサポートする Azure Spring Cloud コンポーネントへの次のエグレス トラフィックをブロック "*しないでください*"。

## <a name="azure-spring-cloud-network-requirements"></a>Azure Spring Cloud のネットワーク要件

  | 送信先エンドポイント | Port | 用途 | 注意 |
  |------|------|------|
  | *:1194 *または* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:1194 | UDP:1194 | 基になる Kubernetes クラスターの管理。 | |
  | *:443 *または* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:443 | TCP:443 | Azure Spring Cloud サービスの管理。 | サービス インスタンス "requiredTraffics" の情報は、リソース ペイロードの "networkProfile" セクションで確認できます。 |
  | *:9000 *または* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:9000 | TCP:9000 | 基になる Kubernetes クラスターの管理。 |
  | *:123 *または* ntp.ubuntu.com:123 | UDP: 123 | Linux ノードでの NTP 時刻の同期。 | |
  | *.azure.io:443 *または* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureContainerRegistry:443 | TCP:443 | Azure Container Registry。 | [仮想ネットワークのサービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md) *Azure Container Registry* を有効にすることで置き換えることができます。 |
  | *.core.windows.net:443 および *.core.windows.net:445 *または* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Storage:443 および Storage:445 | TCP:443、TCP:445 | Azure File Storage | [仮想ネットワークのサービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md) *Azure Storage* を有効にすることで置き換えることができます。 |
  | *.servicebus.windows.net:443 *または* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - EventHub:443 | TCP:443 | Azure Event Hub。 | [仮想ネットワークのサービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md) *Azure Event Hubs* を有効にすることで置き換えることができます。 |
  

## <a name="azure-spring-cloud-fqdn-requirements--application-rules"></a>Azure Spring Cloud の FQDN 要件/アプリケーション ルール

Azure Firewall には、次の構成を簡略化するための完全修飾ドメイン名 (FQDN) タグ **AzureKubernetesService** が用意されています。

  | 送信先 FQDN | Port | 用途 |
  |------|------|------|
  | *.azmk8s.io | HTTPS: 443 | 基になる Kubernetes クラスターの管理。 |
  | <i>mcr.microsoft.com</i> | HTTPS: 443 | Microsoft Container Registry (MCR)。 |
  | *.cdn.mscr.io | HTTPS: 443 | Azure CDN によってサポートされる MCR ストレージ。 |
  | *.data.mcr.microsoft.com | HTTPS: 443 | Azure CDN によってサポートされる MCR ストレージ。 |
  | <i>management.azure.com</i> | HTTPS: 443 | 基になる Kubernetes クラスターの管理。 |
  | <i>login.microsoftonline.com</i> | HTTPS: 443 | Azure Active Directory 認証。 |
  |<i>packages.microsoft.com</i>    | HTTPS: 443 | Microsoft パッケージ リポジトリ。 |
  | <i>acs-mirror.azureedge.net</i> | HTTPS: 443 | kubenet や Azure CNI などの必要なバイナリをインストールするために必要なリポジトリ。 |
  | *mscrl.microsoft.com* | HTTPS:80 | 必要な Microsoft 証明書チェーン パス。 |
  | *crl.microsoft.com* | HTTPS:80 | 必要な Microsoft 証明書チェーン パス。 |
  | *crl3.digicert.com* | HTTPS:80 | サード パーティの SSL 証明書チェーン パス。 |

## <a name="see-also"></a>関連項目
* [プライベート ネットワークのアプリケーションにアクセスする](spring-cloud-access-app-virtual-network.md)
* [Application Gateway と Azure Firewall を使用してアプリを公開する](spring-cloud-expose-apps-gateway-azure-firewall.md)