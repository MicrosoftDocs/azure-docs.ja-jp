---
title: Apache Kafka on Confluent Cloud の概要 - Azure パートナー ソリューション
description: Azure Marketplace にある Apache Kafka on Confluent Cloud の使用について説明します。
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 01/15/2021
ms.author: tomfitz
ms.openlocfilehash: b4aa00b7a2cc7ca6bc968be70957c127a8e00738
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98253288"
---
# <a name="what-is-apache-kafka-for-confluent-cloud"></a>Apache Kafka on Confluent Cloud とは

Apache Kafka on Confluent Cloud は、サービスとしての Apache Kafka を提供する Azure Marketplace オファリングです。 フル マネージドなので、ユーザーは、クラスターの管理ではなくアプリケーションの構築に集中することができます。

クロスプラットフォーム管理の負担を軽減するために、Microsoft は Confluent Cloud と提携して、Azure から Confluent Cloud への統合プロビジョニング レイヤーを構築しました。 これにより、Azure で Confluent Cloud を使用するための統合されたエクスペリエンスを提供します。 ユーザーは、Confluent Cloud を Azure アプリケーションと簡単に統合して管理することができます。

これまでは、Marketplace で Confluent Cloud オファリングを購入し、Confluent Cloud で個別にアカウントを設定する必要がありました。 構成やリソースを管理するには、Azure と Confluent Cloud のポータル間を行き来する必要がありました。

これからは、**Microsoft.Confluent** という名前のリソース プロバイダーを使用して、Confluent Cloud リソースをプロビジョニングします。 Confluent Cloud の組織リソースは、[Azure portal](https://portal.azure.com/)、[Azure CLI](/cli/azure/)、または [Azure SDK](/azure/#languages-and-tools) を使用して作成および管理します。 Confluent Cloud は、環境、クラスター、トピック、API キー、マネージド コネクタなどの、サービスとしてのソフトウェア (SaaS) アプリケーションを所有し、実行します。

## <a name="capabilities"></a>機能

Confluent Cloud と Azure の緊密な統合により、次の機能を実現しています。

- フル マネージド インフラストラクチャにより、Azure portal から新しい Confluent Cloud の組織リソースをプロビジョニング。
- Azure Active Directory (Azure AD) を使用して、Azure から Confluent Cloud へのシングル サインオン (SSO) を効率化。 Confluent Cloud ポータルでの、別個の認証は必要ありません。
- Azure サブスクリプションの請求を通じて、Confluent Cloud での使用量がまとめて請求。
- Azure portal から Confluent Cloud リソースを管理し、他の Azure リソースと共に **[すべてのリソース]** ページで追跡。

## <a name="confluent-organization"></a>Confluent 組織

Confluent 組織は、Azure と Confluent Cloud リソース間のマッピングを提供するリソースです。 これは、他の Confluent Cloud リソースの親リソースです。

各 Azure サブスクリプションには、複数の Confluent プランを含めることができます。 各 Confluent プランは、Confluent ポータルのユーザー アカウントと組織にマップされます。 各 Confluent 組織内で、複数の環境、クラスター、トピック、コネクタを作成できます。

Azure で Confluent Cloud リソースをプロビジョニングすると、Confluent 組織 ID、既定の環境、およびユーザー アカウントが付与されます。 詳細については、[クイックスタート:Confluent Cloud on Azure の概要](create.md)に関するページを参照してください。

請求については、Marketplace で購入した各 Confluent Cloud のオファーは、固有の Confluent 組織にマップされます。

## <a name="single-sign-on"></a>シングル サインオン

Azure portal にサインインすると、ユーザーの資格情報が Confluent Cloud SaaS ポータルへのサインインにも使用されます。 このエクスペリエンスでは、セキュリティで保護された便利な方法でサインインするため、[Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) および [Azure AD SSO](../../active-directory/manage-apps/what-is-single-sign-on.md) が使用されています。

詳細については、[シングル サインオン](manage.md#single-sign-on)に関するページを参照してください。

## <a name="billing"></a>課金

使用できる課金オプションには、従量課金制の月額プランとコミットメント プランの 2 つがあります。

- **従量課金制の月間プラン** では、Azure の月々の請求額に Confluent クラウドの従量課金料金が追加されます。
- **コミットメント プラン** では、最低利用額を契約することで、確約した Confluent Cloud 使用量に対する割引を受けることができます。

使用する課金オプションは、サービスの作成時に指定します。

## <a name="confluent-links"></a>Confluent リンク

Apache Kafka for Confluent Cloud の使用に関するその他のヘルプについては、[Confluent サイト](https://docs.confluent.io/home/overview.html)への以下のリンクを参照してください。

課金オプションの詳細については、次を参照してください。

* [従量課金制での Azure Marketplace](https://docs.confluent.io/cloud/current/billing/ccloud-azure-payg.html)
* [コミットメントでの Azure Marketplace](https://docs.confluent.io/cloud/current/billing/ccloud-azure-ubb.html)

ソリューションの管理の詳細については、以下を参照してください。

* [Confluent Cloud でクラスターを作成する](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)
* [Confluent クラウドの環境](https://docs.confluent.io/current/cloud/using/environments.html)
* [Confluent Cloud の基礎](https://docs.confluent.io/current/cloud/using/cloud-basics.html)

サポートと使用条件については、以下を参照してください。

* [Confluent サポート](https://support.confluent.io)
* [サービス使用条件](https://www.confluent.io/confluent-cloud-tos)

## <a name="next-steps"></a>次の手順

Apache Kafka for Confluent Cloud のインスタンスを作成するには、[クイックスタート:Confluent Cloud on Azure の概要](create.md)に関するページを参照してください。
