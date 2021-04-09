---
title: Azure の Red Hat Enterprise Linux イメージの概要
description: Microsoft Azure の Red Hat Enterprise Linux イメージについて説明します
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 02/10/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: 42e0788a25efa5124f24a77b48469d6ed8265dfc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694684"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux イメージの概要

この記事では、名前付けと保有期間に関するポリシーと、Azure Marketplace で入手できる Red Hat Enterprise Linux (RHEL) イメージについて説明します。

すべてのバージョンの RHEL の Red Hat サポート ポリシーの詳細については、「[Red Hat Enterprise Linux life cycle (Red Hat Enterprise Linux のライフ サイクル)](https://access.redhat.com/support/policy/updates/errata)」を参照してください。 料金の詳細については、「[Azure 料金計算ツール](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)」を参照してください。

>[!IMPORTANT]
> Azure Marketplace で現在入手できる RHEL イメージは、BYOS (Bring-Your-Own-Subscription) または従量課金制ライセンス モデルをサポートしています。 BYOS と従量課金制ライセンスの動的切り替えは、[Azure Hybrid Use Benefit](../../linux/azure-hybrid-benefit-linux.md) を使用して実行できます。

>[!NOTE]
> Azure Marketplace の RHEL イメージに関連する問題については、サポート チケットを Microsoft に提出してください。

## <a name="view-images-available-in-azure"></a>入手できるイメージを Azure で表示する

Azure Marketplace で "Red Hat" を検索するときや、Azure portal の UI でリソースを作成するときは、入手できるすべての RHEL イメージのサブセットのみが表示されます。 Azure CLI、PowerShell、API を使用して、入手できる VM イメージの完全なセットをいつでも取得することができます。

入手できる Red Hat イメージの完全なセットを Azure で表示するには、次のコマンドを実行します。

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>命名規則

Azure の VM イメージは、発行元、プラン、SKU、バージョンごとに整理されます。 発行元:プラン:SKU:バージョンの組み合わせはイメージ URN であり、使用されるイメージを一意に識別します。

たとえば、`RedHat:RHEL:8-LVM:8.1.20200318` は、2020 年 3 月 18 日にビルドされた、RHEL 8.1 LVM パーティション分割イメージを表します。

RHEL 8.1 VM を作成する方法の例を以下に示します。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>"最新" のモニカー

Azure REST API では、特定のバージョンではなく、バージョンで "最新" のモニカーを使用できます。 "最新" のものを使用することで、特定の発行元、プラン、SKU で利用可能な最新のイメージがプロビジョニングされます。

たとえば、`RedHat:RHEL:8-LVM:latest` は、利用可能な最新の RHEL 8 ファミリの LVM パーティション分割イメージを表します。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> 通常、最新のものを判断するためのバージョン比較では、[CompareTo メソッド](/dotnet/api/system.version.compareto#system_version_compareto_system_version_)の規則に従います。
このイメージ バージョン比較を行うには、値を文字列ではなく[バージョン](/dotnet/api/system.version.-ctor) オブジェクトとして比較します。

## <a name="rhel-6-image-types"></a>RHEL 6 イメージの種類

>[!NOTE]
> 2020 年 12 月 30 日時点で、RHEL 6.10 はサポート終了 (EOL) になりました。 継続的なサポートのためには、延長ライフサイクル サポート フェーズの一環として ELS を有効にしてください。[Red Hat ELS のドキュメント](./redhat-extended-lifecycle-support.md)を参照してください。

RHEL 6.x イメージについて、イメージの種類を次の表に示します。

|Publisher | プラン | SKU 値 | Version | 詳細
|----------|-------|-----------|---------|--------
|RedHat | RHEL | マイナー バージョン (例: 6.9) | RHEL マイナー バージョンと発行日の連結された値 (例: 6.9.2018010506) | すべての標準 RHEL 6.x イメージは、この規則に従います。
|RedHat | rhel-byos | rhel-raw69 | RHEL マイナー バージョンと発行日の連結された値 (例: 6.9.20181023) | このイメージは、RHEL 6.9 BYOS イメージです。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL マイナー バージョンと発行日の連結された値 (例: 6.8.2017053118) | このイメージは RHEL 6.8 for SAP アプリケーション イメージです。 SAP アプリケーション リポジトリとベース RHEL リポジトリにアクセスする権利があります。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL マイナー バージョンと発行日の連結された値 (例: 6.7.2017053121) | このイメージは SAP HANA イメージ用の RHEL 6.7 です。 SAP HANA リポジトリとベース RHEL リポジトリにアクセスする権利があります。

## <a name="rhel-7-image-types"></a>RHEL 7 イメージの種類

RHEL 7.x イメージの場合、いくつかの異なる種類のイメージがあります。 次の表は、提供されているさまざまなイメージのセットを示しています。 完全な一覧を表示するには、Azure CLI コマンド `az vm image list --publisher redhat --all` を使用します。

>[!NOTE]
> 特に指定がない限り、すべてのイメージは LVM パーティション分割され、通常の RHEL リポジトリに接続されます。 つまり、リポジトリは Extended Update Support (EUS) ではなく、Update Services for SAP (E4S) でもありません。 今後は、LVM パーティション分割されたイメージのみを発行するように移行しますが、この決定についてフィードバックをお寄せください。 Extended Update Support と Update Services for SAP の詳細については、「[Red Hat Enterprise Linux life cycle (Red Hat Enterprise Linux のライフ サイクル)](https://access.redhat.com/support/policy/updates/errata)」を参照してください。

|Publisher | プラン | SKU 値 | Version | 詳細
|----------|-------|------------|---------|--------
|RedHat | RHEL | マイナー バージョン (例: 7.6) | RHEL マイナー バージョンと発行日の連結された値 (例: 7.6.2019102813) | 2019 年 4 月より前に発行されたイメージは、標準 RHEL リポジトリに接続されています。 2019 年 4 月以降に発行されたイメージは Red Hat の EUS リポジトリに接続されているため、特定のマイナー バージョンのバージョン固定が可能になります。 通常のリポジトリが必要なお客様は、SKU 値に 7-LVM または 7-RAW が指定されたイメージを使用する必要があります (詳細については後述します)。 RHEL 7.7 以降のイメージは、LVM パーティション分割されています。 このカテゴリ内の他のすべてのイメージは、未加工でパーティション分割されています。
|RedHat | RHEL | 7-RAW | RHEL マイナー バージョンと発行日の連結された値 (例: 7.6.2019102813) | これらのイメージは未加工でパーティション分割されています (たとえば、論理ボリュームは追加されていません)。
|RedHat | RHEL | 7-RAW-CI | RHEL マイナー バージョンと発行日の連結された値 (例: 7.6.2019072418) | これらのイメージは未加工でパーティション分割されています (たとえば、論理ボリュームは追加されていません)。また、プロビジョニングに cloud-init が使用されます。
|RedHat | RHEL | 7-LVM | RHEL マイナー バージョンと発行日の連結された値 (例: 7.6.2019062414) | これらのイメージは、LVM パーティション分割されています。
|RedHat | rhel-byos | rhel-{lvm,raw} | RHEL マイナー バージョンと発行日の連結された値 (例: 7.7.20190819) | これらのイメージは RHEL 7 BYOS イメージです。 これらはどのリポジトリにも接続されていないため、RHEL の Premium 料金はかかりません。 RHEL BYOS イメージに関心がある場合は、[アクセス権を要求してください](https://aka.ms/rhel-byos)。 SKU 値は末尾がマイナー バージョンであり、イメージが未加工であるか、または LVM パーティション分割されているかを示します。 たとえば、SKU 値 rhel-lvm77 は、LVM でパーティション分割された RHEL 7.7 イメージを示します。
|RedHat | RHEL | RHEL-SAP | RHEL マイナー バージョンと発行日の連結された値 (例: 7.6.2019071300) | これらのイメージは RHEL for SAP イメージです。 RHEL E4S リポジトリに加えて、SAP HANA リポジトリとアプリケーション リポジトリへのアクセス権を持ちます。 課金には、基本コンピューティング料金に加えて RHEL Premium と SAP Premium が含まれます。
|RedHat | RHEL | RHEL-SAP-HA | RHEL マイナー バージョンと発行日の連結された値 (例: 7.6.2019062320) | これらのイメージは、高可用性および更新サービスを備えた RHEL for SAP イメージです。 RHEL E4S リポジトリに加えて、SAP HANA リポジトリとアプリケーション リポジトリ、および高可用性リポジトリへのアクセス権を持ちます。 請求には、基本コンピューティング料金に加えて RHEL Premium、SAP Premium、および高可用性 Premium が含まれます。
|RedHat | RHEL | RHEL-HA | RHEL マイナー バージョンと発行日の連結された値 (例: 7.6.2019062019) | これらのイメージは、高可用性アドオンへのアクセス権も持つ RHEL イメージです。 高可用性アドオン Premium があるため、RHEL と基本コンピューティング料金とは別に若干の追加料金が発生します。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL マイナー バージョンと発行日の連結された値 (例: 7.3.2017053118) | SAP アプリケーション リポジトリと SAP HANA リポジトリが SAP リポジトリに結合されているため、これらのイメージは最新ではありません。 これらのイメージは、RHEL for SAP Applications イメージです。 SAP アプリケーション リポジトリとベース RHEL リポジトリにアクセスする権利があります。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL マイナー バージョンと発行日の連結された値 (例: 7.3.2018051421) | SAP アプリケーション リポジトリと SAP HANA リポジトリが SAP リポジトリに結合されているため、これらのイメージは最新ではありません。 これらのイメージは RHEL for SAP HANA イメージです。 SAP HANA リポジトリとベース RHEL リポジトリにアクセスする権利があります。

## <a name="rhel-8-image-types"></a>RHEL 8 イメージの種類

>[!NOTE]
> Red Hat では、Grubby を使用し、RHEL 8+ のカーネル コマンド ライン パラメーターを構成することを推奨しています。 詳細は[こちら](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel)をご覧ください。

次に、RHEL 8 イメージの種類の詳細を示します。

|Publisher | プラン | SKU 値 | Version | 詳細
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | RHEL マイナー バージョンと発行日の連結された値 (例: 8.0.20191023) | これらのイメージは、標準の Red Hat リポジトリに接続されている、LVM パーティション分割 RHEL 8 イメージです。
|RedHat | RHEL | 8-gen2 | RHEL マイナー バージョンと発行日の連結された値 (例: 8.0.20191024) | これらのイメージは、標準の Red Hat リポジトリに接続されている、LVM パーティション分割 Hyper-V 第 2 世代 RHEL 8 イメージです。 Azure の第 2 世代 VM の詳細については、「[Azure での第 2 世代 VM のサポート](../../generation-2.md)」を参照してください。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL マイナー バージョンと発行日の連結された値 (例: 8.1.2021012201) | これらのイメージは、RHEL for SAP Applications イメージです。 SAP アプリケーション リポジトリとベース RHEL リポジトリにアクセスする権利があります。
|RedHat | RHEL | RHEL-SAP-HA | RHEL マイナー バージョンと発行日の連結された値 (例: 8.1.2021010602) | これらのイメージは、高可用性および更新サービスを備えた RHEL for SAP イメージです。 RHEL E4S リポジトリに加えて、SAP ソリューションとアプリケーションのリポジトリ、および高可用性リポジトリへのアクセス権を持ちます。 請求には、基本コンピューティング料金に加えて RHEL Premium、SAP Premium、および高可用性 Premium が含まれます。

## <a name="rhel-extended-support-add-ons"></a>RHEL Extended Support アドオン

### <a name="extended-life-cycle-support"></a>延長ライフサイクル サポート

延長ライフサイクル サポート (ELS) アドオンは、サポート終了 (EOL) に達したリリースに対して重大かつ重要なセキュリティ修正プログラムを有効にするオプションのサブスクリプションです。 RHEL EUS の詳細については、[Red Hat のドキュメント](https://access.redhat.com/support/policy/updates/errata#Extended_Life_Cycle_Support)を参照してください。

現在、ELS は RHEL 6.10 でのみご利用いただけます。 従量課金制のイメージの場合は、[Red Hat ELS のドキュメント](./redhat-extended-lifecycle-support.md)に記載されている手順に従って、ELS を有効にすることができます。

以前のバージョンで実行している場合は、ELS を有効にする前に、RHEL 6.10 にアップグレードする必要があります。

### <a name="extended-update-support"></a>Extended Update Support

2019 年 4 月時点では、既定で EUS リポジトリに接続されている RHEL イメージを入手できます。 RHEL EUS の詳細については、[Red Hat のドキュメント](https://access.redhat.com/articles/rhel-eus)を参照してください。

EUS リポジトリへの切り替えは可能であり、サポートされています。 VM を EUS に切り替える方法と、EUS サポートのサポート終了日の詳細については、「[RHEL EUS およびバージョン固定の RHEL VM](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)」を参照してください。

>[!NOTE]
> RHEL Extras では EUS はサポートされていません。 つまり、通常は RHEL Extras チャンネルから入手できるパッケージをインストールした場合、EUS ではインストールできません。 Red Hat Extras 製品のライフ サイクルの詳細については、「[Red Hat Enterprise Linux Extras life cycle (Red Hat Enterprise Linux Extras のライフサイクル)](https://access.redhat.com/support/policy/updates/extras/)」を参照してください。

#### <a name="differentiate-between-regular-and-eus-images"></a>標準イメージと EUS イメージの区別

EUS リポジトリに接続されているイメージを使用するお客様は、SKU に RHEL マイナー バージョン番号を含む RHEL イメージを使用する必要があります。

たとえば、次の 2 つの RHEL 7.4 イメージが利用可能と表示されるとします。

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

この場合、既定で `RedHat:RHEL:7.6:7.6.2019102813` は EUS リポジトリに接続されます。 SKU 値は 7.4 です。 また、`RedHat:RHEL:7-LVM:7.6.2019062414` は既定で非 EUS リポジトリに接続されます。 SKU 値は 7-LVM です。

標準の (非 EUS) リポジトリを使用するには、SKU にマイナー バージョン番号を含まないイメージを使用します。

#### <a name="rhel-images-with-eus"></a>EUS を含む RHEL イメージ

次の表の情報は、EUS リポジトリに接続されている RHEL イメージに適用されます。

>[!NOTE]
> この記事の執筆時点で EUS をサポートしているのは、RHEL 7.4 以降のマイナー バージョンのみです。 EUS は、RHEL 7.3 以前ではサポートされなくなりました。
>
> RHEL EUS の提供状況の詳細については、「[Red Hat Enterprise Linux life cycle (Red Hat Enterprise Linux のライフ サイクル)](https://access.redhat.com/support/policy/updates/errata)」を参照してください。

マイナー バージョン |EUS イメージの例              |EUS の状態                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | 2019 年 4 月以降に発行されたイメージは、既定では EUS です。|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | 2019 年 6 月以降に発行されたイメージは、既定では EUS です。 |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | 2019 年 5 月以降に発行されたイメージは、既定では EUS です。 |
RHEL 8.0      |該当なし                            | Red Hat からリリースされた EUS はありません。                               |

### <a name="update-services-for-sap"></a>Update Services for SAP

最新の RHEL for SAP イメージは、SAP 用更新サービス ソリューション サブスクリプション (E4S) に接続されます。 E4S の詳細については、Red Hat の[ドキュメント](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)を参照してください。

#### <a name="rhel-images-with-e4s"></a>E4S を含む RHEL イメージ

2019 年 12 月以降に作成された次のプランのイメージは、E4S リポジトリに接続されます。

* RHEL-SAP (RHEL for SAP)
* RHEL-SAP-HA (高可用性および更新サービスを備えた RHEL for SAP)

## <a name="other-available-offers-and-skus"></a>その他の利用可能なプランと SKU

利用可能なオファーと SKU の詳細な一覧には、前の表に記載されている以外の追加のイメージが含まれている可能性があります。 たとえば `RedHat:rhel-ocp-marketplace:rhel74:7.4.1` です。 これらのオファーは、特定のマーケットプレース ソリューション用にサポートを提供するために使用される場合があります。 または、プレビューとテストの目的で発行される可能性があります。 これらは、警告なく随時変更または削除される可能性があります。 Microsoft または Red Hat によって公式に文書化されていない限り、これらを使用しないでください。

## <a name="publishing-policy"></a>発行ポリシー

Microsoft および Red Hat では、特定の共通脆弱性識別子 (CVE) に対処するため、または不定期の構成変更や更新プログラムのために、新しいマイナー バージョンのリリース時にイメージを更新します。 できるだけ早く (CVE の修正プログラムがリリースされてから、または利用可能になってから 3 営業日内に) 更新されたイメージを提供できるよう努めています。

特定のイメージ ファミリでのみ、現在のマイナー リリースを更新します。 新しいマイナー バージョンをリリースする場合、以前のマイナー バージョンの更新を停止します。 たとえば、RHEL 7.6 をリリースする場合、RHEL 7.5 イメージは更新されなくなります。

>[!NOTE]
> RHEL 従量課金制イメージからプロビジョニングされたアクティブな Azure VM は、Azure RHUI に接続されます。また、Red Hat からリリースされ、Azure RHUI にレプリケートされるとすぐに更新プログラムと修正プログラムを受信できます。 このタイミングは、通常、Red Hat による公式リリースから 24 時間以内です。 このような VM では、更新プログラムを取得するために新しく発行されたイメージは必要ありません。 更新をいつ開始するかは、お客様が完全に制御できます。

## <a name="image-retention-policy"></a>イメージの保持期間ポリシー

現在のポリシーは、以前に発行されたすべてのイメージを保持するというものです。 Microsoft には、何らかの問題を発生させる原因になるイメージを削除する権利があります。 たとえば、今後のプラットフォームまたはコンポーネントの更新により構成が不適切となるイメージは削除される可能性があります。 削除される可能性のあるイメージでは、現在の Azure Marketplace ポリシーに従い、イメージが削除される最大 30 日前に通知を提供します。

## <a name="next-steps"></a>次のステップ

* Azure の RHEL イメージの詳細な一覧については、「[Azure で利用可能な Red Hat Enterprise Linux (RHEL) イメージ](./redhat-imagelist.md)」を参照してください。
* Azure Red Hat Update Infrastructure の詳細については、「[Azure のオンデマンド Red Hat Enterprise Linux VM 用 Red Hat Update Infrastructure](./redhat-rhui.md)」を参照してください。
* RHEL BYOS オファーの詳細については、「[Azure での Red Hat Enterprise Linux のサブスクリプション持ち込み Gold Image](./byos.md)」を参照してください。
* すべてのバージョンの RHEL の Red Hat サポート ポリシーの詳細については、「[Red Hat Enterprise Linux life cycle (Red Hat Enterprise Linux のライフ サイクル)](https://access.redhat.com/support/policy/updates/errata)」を参照してください。
