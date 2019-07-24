---
title: Azure Kubernetes Service でサポートされている Kubernetes のバージョン
description: Azure Kubernetes Service (AKS) の Kubernetes バージョン サポート ポリシーとクラスターのライフサイクルを理解する
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 02/15/2019
ms.author: saudas
ms.openlocfilehash: aafc6c5a240a1dd3e3c75dd33da26bde918b2288
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57403211"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でサポートされている Kubernetes のバージョン

Kubernetes コミュニティでは、おおよそ 3 か月おきにマイナー バージョンをリリースしています。 これらのリリースには、新しい機能と機能強化が含まれます。 修正プログラムのリリースは、より頻繁で (場合によっては毎週)、マイナー バージョンでの重要なバグ修正のみを目的としています。 これらの修正プログラム リリースには、セキュリティの脆弱性や、多くの顧客や Kubernetes に基づく運用環境で実行されている製品に影響を与える主なバグの修正プログラムが含まれます。

新しい Kubernetes のマイナー バージョンは、1 日目の [aks-engine][aks-engine] で入手できます。 AKS Service Level Objective (SLO) は、リリースの安定性によって異なりますが、30 日以内の AKS クラスターのマイナー バージョンをリリースすることを目標としています。

## <a name="kubernetes-version-support-policy"></a>Kubernetes バージョン サポート ポリシー

AKS では、Kubernetes の 4 つのマイナー バージョンがサポートされています。

- アップ ストリーム (n) リリースされている現在のマイナー バージョン
- 3 つの以前のマイナー バージョン。 各サポートされているマイナー バージョンは 2 つの安定性の高い修正プログラムもサポートしています。

たとえば、AKS が *1.12.x* を本日導入した場合、*1.11.a* + *1.11.b*、*1.10.c* + *1.10d*、*1.9.e* + *1.9f* のサポートも提供されます (修正プログラムの文字部分は 2 つの安定した最新ビルド)。

新しいマイナー バージョンが導入されると、サポートされている最も古いマイナー バージョンと修正プログラムのリリースは、提供終了となります。 新しいマイナー バージョンのリリースおよび次期バージョンの提供終了の 15 日前に、[Azure の更新プログラム チャネル][azure-update-channel]を通じて告知が行われます。 上記の例では*1.12.x* がリリースされた場合に廃止されるバージョンは、*1.8.g* + *1.8.h* となります。

ポータルまたは Azure CLI を使用して AKS クラスターをデプロイする場合、クラスターは常に n-1 マイナー バージョンおよび最新修正プログラムに設定されます。 たとえば、AKS が *1.12.x*、*1.11.a* + *1.11.b*、*1.10.c* + *1.10d*、*1.9.e* + *1.9f* をサポートする場合、新しいクラスターの既定のバージョンは *1.11.b* となります。

## <a name="list-currently-supported-versions"></a>現在サポートされているバージョンの一覧表示

ご使用のサブスクリプションとリージョンで現在使用可能なバージョンを確認するには、[az aks get-versions][az-aks-get-versions] コマンドを使用します。 次の例では、*EastUS* リージョンで使用可能な Kubernetes のバージョンが一覧表示されます。

```azurecli-interactive
az aks get-versions --location eastus --output table
```

出力は次の例のようになります。この例では、Kubernetes バージョン *1.12.5* が使用可能な最新バージョンです。

```
KubernetesVersion    Upgrades
-------------------  -----------------------
1.12.5               None available
1.12.4               1.12.5
1.11.7               1.12.4, 1.12.5
1.11.6               1.11.7, 1.12.4, 1.12.5
1.10.12              1.11.6, 1.11.7
1.10.9               1.10.12, 1.11.6, 1.11.7
1.9.11               1.10.9, 1.10.12
1.9.10               1.9.11, 1.10.9, 1.10.12
```

## <a name="faq"></a>FAQ

**顧客がサポートされていないマイナー バージョンの Kubernetes クラスターをアップグレードするとどうなりますか。**

*n-4* バージョンを使用している場合は、SLO から出ます。 N-3 から n-4 へのバージョン アップグレードに成功した場合は、SLO に戻ります。 例: 

- サポートされている AKS バージョンが *1.12.x*、*1.11.a* + *1.11.b*、*1.10.c* + *1.10d*、および *1.9.e* + *1.9f* であり、*1.8.g* または *1.8.h* を使用している場合、SLO から出ます。
- *1.8.g* または *1.8.h* から *1.9.e* または *1.9.f* へのアップグレードに成功した場合、SLO に戻ります。

*n-4* より前のバージョンへのアップグレードはサポートされていません。 このような場合は、顧客に新しい AKS クラスターを作成して、ワークロードを再デプロイすることをお勧めします。

**顧客がサポートされていないマイナー バージョンの Kubernetes クラスターを拡大縮小するとどうなりますか。**

AKS でサポートされていないのマイナー バージョンについては、拡大縮小は問題なく動作します。

**顧客が Kubernetes バージョンをそのまま永久に使用し続けることはできますか。**

はい。 ただし、クラスターが AKS でサポートされているバージョンのいずれかにない場合は、クラスターは AKS SLO 外となります。 Azure は、自動的にクラスターをアップグレードしたり削除したりすることはありません。

**エージェント クラスターがサポートされている AKS バージョンのいずれかにない場合、マスターはどのバージョンをサポートしますか。**

マスターは、サポートされている最新バージョンに自動的に更新されます。

## <a name="next-steps"></a>次の手順

クラスターをアップグレードする方法の詳細については、「[Azure Kubernetes Service (AKS) クラスターのアップグレード][aks-upgrade]」をご覧ください。

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
