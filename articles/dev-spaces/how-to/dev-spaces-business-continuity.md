---
title: 事業継続とディザスター リカバリー
services: azure-dev-spaces
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 01/28/2019
ms.topic: conceptual
description: Azure Dev Spaces と Azure Kubernetes Services を使用して、ビジネス継続性の提供とディザスター リカバリーの準備を行う方法について説明します
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 77425d294dcea0546259b152b793cec291940c53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929601"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Azure Dev Spaces での事業継続とディザスター リカバリー

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) のディザスター リカバリー ガイダンスを確認する

Azure Dev Spaces は Azure Kubernetes Service (AKS) の機能です。 AKS でのディザスター リカバリーのガイドラインを理解し、お客様が Dev Spaces に使用する AKS クラスターにそれらが当てはまるかどうかを検討する必要があります。 詳細については、「[Azure Kubernetes Service (AKS) での事業継続とディザスター リカバリーに関するベスト プラクティス](../../aks/operator-best-practices-multi-region.md)」を参照してください。

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>異なるリージョンの AKS クラスターで Dev Spaces を有効にする

異なるリージョンの AKS クラスターで Dev Spaces を有効にすると、1 つの Azure リージョンで障害が発生した直後に Dev Spaces の使用を再開できます。

AKS の複数リージョンのデプロイの概要については、「[複数リージョンへのデプロイを計画する](../../aks/operator-best-practices-multi-region.md#plan-for-multiregion-deployment)」を参照してください。

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Azure CLI を使用して Dev Spaces を有効にする

コマンド ラインで Dev Spaces を有効にすることもできます。

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>各クラスターにチームのベースラインをデプロイする

Dev Spaces を使用する場合は、通常、お客様の Kubernetes クラスター上の親開発空間にアプリケーション全体をデプロイします。 既定では、`default` 空間が使用されます。 初期デプロイには、すべてのサービスと、それらのサービスが依存する外部リソース (データベースやキューなど) が含まれます。 これは "*ベースライン*" と呼ばれます。 親開発空間にベースラインを設定したら、子開発空間内で個々のサービスを反復処理し、デバッグします。

サービスのベースライン セットの最新バージョンを、複数のリージョンのクラスターにデプロイする必要があります。 この方法でベースライン サービスを更新することで、1 つの Azure リージョンで障害が発生した場合でも、引き続き Dev Spaces を使用できます。 たとえば、CI/CD パイプラインを介してお客様のベースラインをデプロイする場合、異なるリージョンの複数のクラスターにデプロイされるようにパイプラインを変更します。

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Dev Spaces に使用する適切な AKS クラスターを選択する

チームのベースラインを実行するバックアップ クラスターを適切に構成できたら、いつでもバックアップ クラスターにすばやく切り替えることができます。 その後、子の Dev Spaces で操作している個々のサービスを再稼働できます。

次の CLI コマンドを使用して、別のクラスターを選択します。

```azurecli
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

次のコマンドを使用して、新しいクラスターで使用可能な開発空間の一覧を表示できます。

```cmd
azds space list
```

次のコマンドを使用して、作業する新しい開発空間を作成したり、既存の開発空間を選択したりできます。

```cmd
azds space select -n <space name>
```

これらのコマンドを実行した後、選択したクラスターと開発空間が、後続の CLI 操作と、Azure Dev Spaces 用の Visual Studio Code 拡張機能を使用したプロジェクトのデバッグに使用されます。

Visual Studio を使用している場合は、次の手順を通じて、既存のプロジェクトで使用されるクラスターを切り替えることができます。

1. Visual Studio でプロジェクトを開きます。
1. ソリューション エクスプローラーでプロジェクト名を右クリックし、 **[プロパティ]** をクリックします。
1. 左側のウィンドウで、 **[デバッグ]** をクリックします。
1. デバッグ プロパティ ページで、 **[プロファイル]** ドロップダウン リストをクリックし、 **[Azure Dev Spaces]** を選択します。
1. **[変更]** ボタンをクリックします。
1. 表示されたダイアログで、お客様が使用したい AKS クラスターを選択します。 必要に応じて、 **[スペース]** ドロップダウン リストから適切なオプションを選択して、作業する別の開発空間を選択するか、新しい開発空間を作成します。

適切なクラスターと空間を選択したら、F5 キーを押して Dev Spaces でサービスを稼働できます。

元のクラスターを使用するように構成されている他のすべてのプロジェクトに対して、これらの手順を繰り返します。

## <a name="access-a-service-on-a-backup-cluster"></a>バックアップ クラスター上のサービスにアクセスする

パブリック DNS 名を使用するようにお客様のサービスが構成されている場合、それをバックアップ クラスター上で稼働させると、異なる URL がサービスに割り当てられます。 パブリック DNS 名は、常に `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io` の形式になります。 別のクラスターに切り替えると、クラスターの GUID と、場合によってはリージョンが変更されます。

Dev Spaces では常に、`azds up` の実行時にサービスの正しい URL が表示されます。これは Visual Studio の出力ウィンドウの **[Azure Dev Spaces]** にも表示されます。

`azds list-uris` コマンドを実行して URL を調べることもできます。
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

サービスにアクセスするときにこの URL を使用します。
