---
title: Azure 上の Red Hat ワークロードの概要 | Microsoft Docs
description: Azure で利用できる Red Hat 製品オファリングについて学習します。
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: bd8f2e5b825f88d1b0e04ed231b85296ac47e998
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676050"
---
# <a name="red-hat-workloads-on-azure"></a>Azure 上の Red Hat ワークロード

Azure では、さまざまなオファリングを通じて Red Hat ワークロードがサポートされます。 Red Hat Enterprise Linux (RHEL) イメージは、Red Hat Update Infrastructure (RHUI) と同様、RHEL ワークロードの中核にあります。

## <a name="red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux イメージ

Azure では、Azure 上の RHEL イメージについて幅広いオファリングを提供しています。 これらのイメージは、従量課金制と Bring-Your-Own-Subscription (BYOS) の 2 つの異なるライセンス モデルを通じて利用できます。 Azure 上の新しい RHEL イメージは、新しい RHEL バージョンがリリースされたとき、および必要に応じてライフサイクル中に更新されたときに公開されます。

### <a name="pay-as-you-go-images"></a>従量課金制イメージ

Azure には、さまざまな RHEL 従量課金制イメージが用意されています。 これらのイメージは、RHEL の適切なエンタイトルメントを伴い、更新プログラムのソース (Red Hat Update Infrastructure) にアタッチされています。 これらのイメージでは、RHEL のエンタイトルメントと更新に対してプレミアム料金が請求されます。 RHEL 従量課金制イメージのバリエーションは次のとおりです。

* 標準 RHEL。
* RHEL for SAP。
* 高可用性および更新サービスを備えた RHEL for SAP。

個別の支払いに悩まずに適切な数のサブスクリプションを維持したい場合は、従量課金制イメージを使用することをお勧めします。

### <a name="red-hat-gold-images"></a>Red Hat Gold Image

Azure には、Red Hat Gold Image (`rhel-byos`) も用意されています。 これらのイメージは、既存の Red Hat サブスクリプションを所有していて、Azure でそれらを使用する必要があるお客様に役立ちます。 既存の Red Hat サブスクリプションを Azure で使用するには、それらを Red Hat Cloud Access に対して有効にしておく必要があります。 これらのイメージへのアクセスは、Red Hat サブスクリプションが Cloud Access に対して有効になっていて、資格要件を満たしたときに自動的に付与されます。 これらのイメージを使用することで、お客様は、従量課金制イメージを使用した場合に発生する可能性のある二重請求を回避できます。
* [Azure で Cloud Access 用の Red Hat サブスクリプションを有効にする](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/enabling-and-maintaining-subs_cloud-access)方法を確認する。
* [Azure portal、Azure CLI、または PowerShell コマンドレットで Red Hat Gold Image を見つける](./byos.md)方法を確認する。

> [!NOTE]
> RHEL サブスクリプションに対してユーザーが 2 回支払いを行うと、二重請求が発生します。 このシナリオは通常、お客様が Red Hat Subscription-Manager を使用して RHEL 従量課金制 VM にエンタイトルメントをアタッチしている場合に発生します。 たとえば、Subscription-Manager を使用して SAP パッケージのエンタイトルメントを RHEL 従量課金制イメージにアタッチするお客様は、RHEL に対して 2 回支払いを行うため間接的に二重に課金されます。 つまり、従量課金制のプレミアム料金として 1 回支払い、SAP サブスクリプションを通じて 1 回支払うことになります。 このシナリオは、BYOS イメージ ユーザーには発生しません。

### <a name="generation-2-images"></a>第 2 世代イメージ

第 2 世代仮想マシン (VM) には、第 1 世代 VM と比較して、新しい機能がいくつか備わっています。 詳細については、[第 2 世代のドキュメント](../../generation-2.md)を参照してください。 RHEL イメージの観点からの主な違いは、第 2 世代 VM では BIOS ファームウェア インターフェイスではなく UEFI を使用することです。 また、ブート時にマスター ブート レコード (MBR) ではなく、GUID パーティション テーブル (GPT) を使用します。 特に、GPT を使用することにより、2 TB を超える OS ディスク サイズが可能になります。 また、[Mv2 シリーズの VM](../../mv2-series.md) は、第 2 世代イメージでのみ実行されます。

RHEL 第 2 世代イメージは、Azure Marketplace で入手できます。 Azure CLI を使用したときに表示されるすべてのイメージの一覧で、イメージ SKU の "gen2" を探してください。 第 2 世代の VM をデプロイするには、VM デプロイ プロセスで **[詳細]** タブに移動します。

## <a name="red-hat-update-infrastructure"></a>Red Hat Update Infrastructure

Azure では、従量課金制の RHEL VM に対してのみ Red Hat Update Infrastructure が提供されます。 RHUI は、実質的には Red Hat CDN のミラーですが、Azure の従量課金制 RHEL VM からのみアクセスできます。 デプロイした RHEL イメージに応じて、適切なパッケージにアクセスできます。 たとえば、RHEL for SAP イメージでは、ベース RHEL パッケージに加えて SAP パッケージにもアクセスできます。

### <a name="rhui-update-behavior"></a>RHUI の更新動作

RHUI に接続されている RHEL イメージは、`yum update` の実行時に、既定で RHEL の最新マイナー バージョンに更新されます。 この動作は、`yum update` 操作が実行された場合に RHEL 7.4 VM が RHEL 7.7 にアップグレードされる可能性があることを意味します。 この動作は RHUI の仕様によるものです。 このアップグレード動作を抑制するには、通常の RHEL リポジトリから [Extended Update Support リポジトリ](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)に切り替えます。

## <a name="next-steps"></a>次のステップ

* [Azure 上の RHEL イメージ](./redhat-images.md)の詳細を確認します。
* [Red Hat Update Infrastructure](./redhat-rhui.md) の詳細を確認します。
* [Red Hat Gold Image (`rhel-byos`) プラン](./byos.md)の詳細を確認します。