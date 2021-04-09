---
title: Azure ExpressRoute CrossConnnections API の開発と統合
description: この記事では、ExpressRoute パートナ向けに expressRouteCrossConnections リソースの種類に関する詳しい概要を示します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: duau
ms.openlocfilehash: b80392231dba26a10141dcd8247b092e8171894c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011984"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>ExpressRoute CrossConnnections API の開発と統合

ExpressRoute Partner Resource Manager API を利用すると、ExpressRoute パートナーは顧客の ExpressRoute 回路のレイヤー 2 とレイヤー 3 構成を管理することができます。 ExpressRoute Partner Resource Manager API では、新しいリソースの種類 **expressRouteCrossConnections** が導入されています。 パートナーはこのリソースを使用して、顧客の ExpressRoute 回路を管理します。

## <a name="workflow"></a>ワークフロー

expressRouteCrossConnections リソースは、ExpressRoute 回路に対するシャドウ リソースです。 Azure の顧客が ExpressRoute 回路を作成し、特定の ExpressRoute パートナーを選択した場合、Microsoft ではパートナーの Azure ExpressRoute 管理サブスクリプションに expressRouteCrossConnections リソースを作成します。 その際に、Microsoft では expressRouteCrossConnections リソースが作成されるリソース グループを定義します。 リソース グループの名前付けの標準は、**CrossConnection-* PeeringLocation* ** です。PeeringLocation = ExpressRoute の場所となります。 たとえば、顧客がデンバーに ExpressRoute 回線を作成した場合、次のリソース グループ内でパートナーの Azure サブスクリプションに CrossConnection が作成されます。**CrossConnnection-Denver**。

ExpressRoute パートナーは、expressRouteCrossConnections リソースに対して REST 操作を発行することで、レイヤー 2 とレイヤー 3 構成を管理します。

## <a name="benefits"></a>メリット

expressRouteCrossConnections リソースへの移行のメリット:

* ExpressRoute パートナー向けの今後の機能強化はすべて、ExpressRouteCrossConnection リソースで利用可能になる予定です。

* パートナーは、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) を expressRouteCrossConnection リソースに適用できます。 これらの制御では、expressRouteCrossConnection リソースと add/update/delete ピアリングの構成を変更できるユーザー アカウントへのアクセス許可を定義できます。

* expressRouteCrossConnection リソースでは、ExpressRoute 接続のトラブルシューティングに役立つ可能性のある API が公開されています。 これには、ARP テーブル、BGP ルート テーブルの概要、および BGP ルート テーブルの詳細が含まれます。 この機能は、従来のデプロイ API ではサポートされていません。

* パートナーは、*RouteFilter* リソースを使用して、Microsoft ピアリング上で公開されているコミュニティを検索することもできます。

## <a name="api-development-and-integration-steps"></a>API の開発と統合の手順

Partner API に対して開発を行うには、ExpressRoute パートナーはテスト顧客とテスト パートナーの設定を利用します。 テスト顧客の設定は、ダミーのデバイスとポートにマップする ExpressRoute 回路をテスト ピアリングの場所に作成するために使用されます。 テスト パートナーの設定は、テスト ピアリングの場所に作成された ExpressRoute 回路を管理するために使用されます。

### <a name="1-enlist-subscriptions"></a>1.サブスクリプションを登録する

テスト パートナーとテスト顧客の設定を要求するには、ExpressRoute の技術担当者に連絡して、2 つの従量課金制 Azure サブスクリプションを登録します。
* **ExpressRoute_API_Dev_Provider_Sub:** このサブスクリプションは、ダミーのデバイスとポート上でテスト ピアリングの場所に作成された ExpressRoute 回路を管理するために使用されます。

* **ExpressRoute_API_Dev_Customer_Sub:** このサブスクリプションは、ダミーのデバイスとポートにマップされた ExpressRoute 回路をテスト ピアリングの場所に作成するために使用されます。

テスト ピアリングの場所: ダミーのデバイスとポートは既定で、運用環境の顧客には公開されません。 テスト設定にマップする ExpressRoute 回路を作成するためには、サブスクリプション機能フラグを有効にする必要があります。

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2.Dev_Provider サブスクリプションを登録して expressRouteCrossConnections API にアクセスする

expressRouteCrossConnections API にアクセスするためには、パートナー サブスクリプションが **Microsoft.Network Resource Provider** に登録されている必要があります。 「[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)」の記事にある手順に従って、登録プロセスを完了します。

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3.Azure Resource Manager REST API の呼び出し用に認証を設定する

ほとんどの Azure サービスでは、サービス API を呼び出す前に、有効な資格情報を使用して Resource Manager で認証を行うためのクライアント コードが必要になります。 認証は、Azure AD によってさまざまなアクターを連携させて行われ、認証の証拠としてクライアントにアクセス トークンが提供されます。

認証プロセスには、2 つの主な手順があります。

1. [クライアントを登録する](/rest/api/azure/#register-your-client-application-with-azure-ad)。
2. [アクセス要求を作成する](/rest/api/azure/#create-the-request)。

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4.クライアント アプリケーションに対するネットワーク共同作成者のアクセス許可を提供する

認証が正常に構成されたら、Dev_Provider_Sub 下で、クライアント アプリケーションへのネットワーク共同作成者のアクセス権を付与する必要があります。 アクセス許可を付与するには、[Azure portal](https://ms.portal.azure.com/#home) にサインインして、次の手順を完了します。

1. [サブスクリプション] に移動して、Dev_Provider_Sub を選択する
2. [アクセス制御 (IAM)] に移動する
3. ロールの割り当てを追加する
4. [ネットワーク共同作成者] ロールを選択する
5. アクセスを [Azure AD のユーザー、グループ、サービス プリンシパル] に割り当てる
6. クライアント アプリケーションを選択する
7. 変更を保存する

### <a name="5-develop"></a>5.開発

[expressRouteCrossConnections API](/rest/api/expressroute/expressroutecrossconnections) に対する開発を行います。

## <a name="rest-api"></a>REST API

REST API のドキュメントについては、[ExpressRoute CrossConnections REST API](/rest/api/expressroute/expressroutecrossconnections) に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

すべての ExpressRoute REST API に関する詳細については、「[ExpressRoute REST API](/rest/api/expressroute/)」を参照してください。