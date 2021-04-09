---
title: Azure Arc 対応 Kubernetes エージェントをアップグレードする
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure Arc 対応 Kubernetes エージェントのアップグレードを制御する
keywords: Kubernetes、Arc、Azure、K8、コンテナー、エージェント、アップグレード
ms.openlocfilehash: d81a00ed4f30f446aeed96d59a455935c652b7d5
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954549"
---
# <a name="upgrading-azure-arc-enabled-kubernetes-agents"></a>Azure Arc 対応 Kubernetes エージェントをアップグレードする

Azure Arc 対応 Kubernetes のエージェントには、自動アップグレード機能と手動アップグレード機能があります。 自動アップグレードを無効にし、代わりに手動アップグレードを使用する場合、Arc エージェントおよび基になる Kubernetes クラスターにバージョン サポート ポリシーが適用されます。

## <a name="toggle-auto-upgrade-on-or-off-when-connecting-cluster-to-azure-arc"></a>クラスターを Azure Arc に接続するときに自動アップグレードのオンとオフを切り替える

Azure Arc 対応 Kubernetes のエージェントには、すぐに使用できる自動アップグレード機能が用意されています。

次のコマンドを使用すると、自動アップグレードを **有効にして** クラスターを Azure Arc に接続できます。

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

自動アップグレードが有効になっている場合、新しいバージョンのエージェントがあるかどうかを、エージェントは Azure に対し 1 時間ごとにポーリングします。 エージェントが使用可能な新しいバージョンを検出すると、Azure Arc エージェントに対し Helm Chart アップグレードがトリガーされます。

自動アップグレードを選択しない場合、クラスターを Azure Arc に接続するときに、`--disable-auto-upgrade` パラメーターを指定します。次のコマンドを使用すると、自動アップグレードを **無効にして** クラスターを Azure Arc に接続できます。

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest --disable-auto-upgrade
```

> [!TIP]
> 自動アップグレードを無効にする予定の場合は、Azure Arc 対応 Kubernetes の「[バージョンのサポート ポリシー](#version-support-policy)」をご覧ください。

## <a name="toggle-auto-upgrade-onoff-after-connecting-cluster-to-azure-arc"></a>クラスターを Azure Arc に接続した後に自動アップグレードのオンとオフを切り替える

クラスターを Azure Arc に接続した後、次に示すように、`az connectedk8s update` コマンドを使用して自動アップグレード機能を切り替えることができます。

```console
az connectedk8s update --name AzureArcTest1 --resource-group AzureArcTest --auto-upgrade false
```

## <a name="manually-upgrade-agents"></a>エージェントを手動でアップグレードする

エージェントの自動アップグレードを無効にしている場合は、次に示すように `az connectedk8s upgrade` コマンドを使用して、これらのエージェントを手動でアップグレードできます。

```console
az connectedk8s upgrade -g AzureArcTest1 -n AzureArcTest --agent-version 1.0.1
```

Azure Arc 対応 Kubernetes エージェントは、`MAJOR.MINOR.PATCH` の標準の[セマンティック バージョン管理スキーム](https://semver.org/)に従ってバージョン管理されています。 

バージョンのそれぞれの数字は、前のバージョンとの一般的な互換性を示します。

* **メジャー バージョン** は、API が更新され互換性がなくなった場合や、下位互換性がなくなると変更されます。
* **マイナー バージョン** は、機能変更に対し、他のマイナー リリースが下位互換となるときに変更されます。
* **修正プログラムのバージョン** は、下位互換性のあるバグ修正が行われたときに変更されます。

## <a name="version-support-policy"></a>バージョンのサポート ポリシー

サポート イシューを作成した場合、Azure Arc 対応 Kubernetes のバージョンのサポート ポリシーは次になります。

* Azure Arc 対応 Kubernetes エージェントのサポート期間は、"N-2" になります (この場合の 'N' はエージェントの最新のマイナー リリースです)。 
  * たとえば、Azure Arc 対応 Kubernetes の 0.28.a が今日導入される場合、Azure Arc でサポートされるバージョンは、0.28.a、0.28.b、0.27.c、0.27.d、0.26.e、0.26.f になります。

* Azure Arc に接続されている Kubernetes クラスターのサポート期間は、"N-2" になります (この場合の 'N' は[アップストリーム Kubernetes](https://github.com/kubernetes/kubernetes/releases) の最新の安定マイナー リリースです)。 
  * たとえば、Kubernetes の 1.20.a が今日導入される場合、サポートされるバージョンは、1.20.a、1.20.b、1.19.c、1.19.d、1.18.e、1.18.f になります。

### <a name="how-often-are-minor-version-releases-of-azure-arc-enabled-kubernetes-available"></a>Azure Arc 対応 Kubernetes のマイナー バージョンはどのくらいの頻度でリリースされますか?

Azure Arc 対応 Kubernetes エージェントの 1 つのマイナー バージョンは、だいたい 1 か月に一度リリースされます。

### <a name="what-happens-if-im-using-an-agent-version-or-a-kubernetes-version-outside-the-official-support-window"></a>公式のサポート期間を過ぎて Kubernetes のエージェント バージョンを使用している場合どうなりますか?

'サポート外' とは、実行しているバージョンが、エージェントとアップストリーム Kubernetes クラスターでサポートされているバージョンの "N-2" に含まれていないことを意味します。 サポート イシューを進めるには、サポートされているバージョンにクラスターとエージェントをアップグレードすることが求められます。

## <a name="next-steps"></a>次のステップ

* クイックスタートを利用して、[Kubernetes クラスターを Azure Arc に接続](./quickstart-connect-cluster.md)します。
* Kubernetes クラスターが既に Azure Arc に接続されていますか? [Arc 対応 Kubernetes クラスターの構成を作成する](./tutorial-use-gitops-connected-cluster.md)。
* [Azure Policy を使用して構成を大規模に適用する](./use-azure-policy.md)方法について学ぶ。