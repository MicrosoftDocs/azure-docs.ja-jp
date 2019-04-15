---
title: Azure Functions のネットワーク オプション
description: Azure Functions で利用可能なすべてのネットワーク オプションの概要
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: conceptual
ms.date: 1/14/2019
ms.author: alkarche
ms.openlocfilehash: 8bbc44e7af68f005f30fff143741bc4bfe0adcf2
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896740"
---
# <a name="azure-functions-networking-options"></a>Azure Functions のネットワーク オプション

このドキュメントでは、Azure Functions のホスティング オプションで利用できる一連のネットワーク機能について説明します。 次のネットワーク オプションはすべて、インターネットのルーティング可能アドレスを使用せずにリソースにアクセスする機能、または Function App へのインターネット アクセスを制限する機能を提供します。 すべてのホスティング モデルでは、さまざまなレベルのネットワークの分離を使用できます。正しいものを選択することで、ネットワークの分離の要件を満たすことができます。

Function App はいくつかの方法でホストできます。

* さまざまなレベルの VNET 接続性とスケーリングのオプションが設定された、マルチテナント インフラストラクチャ上で実行される一連のプラン オプションがあります。
    1. 従量課金プラン。負荷に応じて動的にスケーリングし、最小限のネットワークの分離オプションを提供します。
    1. Premium プラン。このプランでも動的なスケーリングが行われますが、より包括的なネットワークの分離が提供されます。
    1. App Service プラン。固定されたスケールで動作し、Premium プランと同様のネットワークの分離を提供します。
* 関数は、ユーザーの関数を VNet にデプロイし、かつ完全なネットワーク制御と分離を提供する App Service Environment (ASE) 上でも実行できます。

## <a name="networking-feature-matrix"></a>ネットワーク機能のマトリックス

|                |[従量課金プラン](functions-scale.md#consumption-plan)|⚠ [Premium プラン](functions-scale.md##premium-plan-public-preview)|[App Service プラン](functions-scale.md#app-service-plan)|[App Service 環境](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[**受信 IP の制限**](#inbound-ip-restrictions)|✅はい|✅はい|✅はい|✅はい|
|[**VNET 統合**](#vnet-integration)|❌いいえ|⚠ はい|✅はい|✅はい|
|[**プレビューの VNET 統合 (Express Route およびサービス エンドポイント)**](#preview-vnet-integration)|❌いいえ|⚠ はい|⚠ はい|✅はい|
|[**ハイブリッド接続と**](#hybrid-connections)|❌いいえ|❌いいえ|✅はい|✅はい|
|[**プライベート サイトへのアクセス**](#private-site-access)|❌いいえ| ❌いいえ|❌いいえ|✅はい|

⚠ プレビュー機能 (実稼働用ではありません)

## <a name="inbound-ip-restrictions"></a>受信 IP の制限

IP 制限を使用すると、アプリへのアクセスを許可されている IP アドレスの優先度順の許可/拒否リストを定義できます。 許可リストには、IPv4 アドレスと IPv6 アドレスを含めることができます。 1 つまたは複数のエントリがある場合、リストの最後にあるものはすべて暗黙的に拒否されます。 IP 制限機能は、すべての関数ホスティング オプションで有効です。

> ![重要] Azure portal エディターを使用できるようにするには、実行中の関数アプリに Azure portal が直接アクセスできる必要があり、かつ Azure portal にアクセスするために使用しているデバイスの IP がホワイトリストに登録されている必要があります。 ネットワーク制限が設定されていても、**[プラットフォーム機能]** タブのどの機能にもアクセスできます。

[詳しくは、こちらをご覧ください](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)

## <a name="vnet-integration"></a>VNET 統合

VNET 統合により、関数アプリは VNET 内のリソースにアクセスできます。 VNET 統合は、Premium プランと App Service プランの両方で使用できます。 アプリが App Service Environment 内にある場合は、既に VNet 内にそのアプリが存在しているので、同じ VNet 内のリソースに到達するために VNet 統合機能を使用する必要はありません。

VNet 統合により、関数アプリは仮想ネットワーク内のリソースにアクセスできるようになりますが、仮想ネットワークからその関数アプリへの[プライベート サイト アクセス](#private-site-access)は付与されません。

VNet 統合は、多くの場合、アプリから、VNet で実行されているデータベースや Web サービスにアクセスできるようにするために使用されます。 VNet 統合を使用すれば、VM 上のアプリケーション用にパブリック エンドポイントを公開せずに済み、インターネット以外のルーティング可能なプライベート アドレスを使用できます。

一般提供バージョンの VNET 統合は、関数アプリを仮想ネットワークに接続するために VPN ゲートウェイに依存しています。 App Service プランでホストされている関数で使用できます。 この機能を構成する方法については、[同機能の App Service ドキュメント](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration)を参照してください。

### <a name="preview-vnet-integration"></a>プレビューの VNET 統合

VNet 統合機能には、プレビュー段階の新しいバージョンがあります。 これは、ポイント対サイト VPN に依存しておらず、ExpressRoute またはサービス エンドポイントを越えてのリソースへのアクセスもサポートしています。 この機能は、Premium プラン、および PremiumV2 にスケーリングされた App Service プランで使用できます。

現在プレビュー段階にある新しいバージョンの VNet 統合には、次のような利点があります。

* 新しい VNet 統合機能を使用するためにゲートウェイは必要ありません。
* ExpressRoute で接続された VNet と統合することのほかに、追加構成なしで、ExpressRoute 接続を介してリソースにアクセスできます。
* 実行中の関数から、サービス エンドポイントのセキュリティで保護されたリソースを使用できます。 これを行うには、VNet 統合に使用されるサブネットでサービス エンドポイントを有効にします。
* 新しい VNet 統合機能を使用して、サービス エンドポイントのセキュリティで保護されたリソースを使用するようにトリガーを構成することはできません。 
* 関数アプリと VNet は両方とも同じリージョンに存在する必要があります。
* 新しい機能では、Resource Manager の VNet 内に未使用のサブネットが必要です。
* 新しいバージョンの VNet 統合では、プレビュー期間中、運用環境のワークロードはサポートされません
* 新しい VNet 統合では、ルート テーブルとグローバル ピアリングはまだ使用できません。
* 1 つのアドレスが潜在的な各関数アプリ インスタンスに対して使用されます。 サブネットのサイズは割り当て後に変更できないため、最大スケール サイズを容易にサポートできるサブネットを使用してください。 たとえば、80 個のインスタンスにスケーリングできる Premium プランをサポートするには、126 個のホスト アドレスを提供する `/25` サブネットをお勧めします。

プレビューの VNET 統合の使用について詳しくは、[[関数アプリを Azure 仮想ネットワークに統合する]](functions-create-vnet.md) を参照してください。

## <a name="hybrid-connections"></a>ハイブリッド接続と

[ハイブリッド接続](../service-bus-relay/relay-hybrid-connections-protocol.md)は、他のネットワークのアプリケーション リソースにアクセスするために使用できる Azure Relay の機能です。 アプリからアプリケーション エンドポイントにアクセスできます。 アプリケーションへのアクセスには使用できません。 ハイブリッド接続は、[App Service プラン](functions-scale.md#app-service-plan)および [App Service Environment](../app-service/environment/intro.md) で実行されている関数に使用できます。

関数で使用されるとき、各ハイブリッド接続は、単一の TCP ホストとポートの組み合わせに相互に関連付けられます。 つまり、TCP リッスン ポートにアクセスしている限り、任意のオペレーティング システムの任意のアプリケーションがハイブリッド接続エンドポイントになることができます。 ハイブリッド接続では、アプリケーション プロトコルやアクセス先は認識されません。 それは、ネットワーク アクセスを提供するだけです。

詳細については、関数と Web アプリの両方をサポートする[ハイブリッド接続の App Service ドキュメント](../app-service/app-service-hybrid-connections.md)を参照してください。

## <a name="private-site-access"></a>プライベート サイトへのアクセス

プライベート サイト アクセスとは、Azure 仮想ネットワークなどプライベート ネットワークのみからアプリにアクセスできるようにすることです。 プライベート サイトには、内部ロード バランサー (ILB) を使用して ASE が構成されている場合のみアクセスできます。 ILB ASE の使用について詳しくは、[ILB ASE の作成と使用](../app-service/environment/create-ilb-ase.md)に関する記事を参照してください。

他のホスティング オプションで VNET リソースにアクセスするには多くの方法がありますが、VNET を介して関数のトリガーを許可する唯一の方法は ASE です。
