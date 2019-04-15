---
title: Azure Container Registry SKU
description: Azure Container Registry で使用可能なさまざまなサービス階層の比較
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: 7fdc1417ac524b422a12a087cf1661040efb9f8a
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521960"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU

Azure Container Registry (ACR) は、SKU と呼ばれる複数のサービス階層で使用できます。 これらの SKU は、Azure におけるプライベート Docker レジストリの容量と使用パターンに合ったさまざまなオプションと予測可能な価格を提供します。

| SKU | 管理者常駐型 | 説明 |
| --- | :-------: | ----------- |
| **Basic** | はい | Azure Container Registry について学習する開発者向けのコスト最適化エントリ ポイント。 Basic レジストリには Standard および Premium と同じプログラム機能があります (Azure Active Directory [認証の統合](container-registry-authentication.md#individual-login-with-azure-ad)、[イメージ削除][container-registry-delete]、および [Web フック][container-registry-webhook]など)。 ただし、含まれているストレージとイメージのスループットは、使用率が低いシナリオに最も適しています。 |
| **Standard** | はい | Standard レジストリは、Basic と同じ機能を提供しますが、含まれているストレージとイメージ スループットが拡大されています。 Standard レジストリは、ほとんどの運用シナリオのニーズを満たすはずです。 |
| **Premium** | はい | Premium レジストリは、含まれているストレージが最も大きく、同時実行操作数も最大であり、大容量シナリオに対応できます。 イメージのスループットの増加に加え、Premium では、複数のリージョン間で 1 つのレジストリを管理するための [geo レプリケーション][container-registry-geo-replication]、イメージ タグに署名するための[コンテンツの信頼 (プレビュー)](container-registry-content-trust.md)、レジストリへのアクセスを制限する[ファイアウォールおよび仮想ネットワーク (プレビュー)](container-registry-vnet.md) などの機能が追加されています。 |
|  クラシック (*2019 年 4 月を過ぎると使用不可*) | いいえ  | この SKU により、Azure での Azure Container Registry サービスの最初のリリースが可能になりました。 クラシック レジストリは、Azure がサブスクリプションに作成するストレージ アカウントによってサポートされ、スループットの向上や geo レプリケーションなどの高いレベルの機能を提供する ACR の機能を制限します。 |

> [!IMPORTANT]
> Classic レジストリ SKU は**非推奨**になっており、**2019 年 4 月**を過ぎると使用できなくなります。 すべての新しいレジストリでは Basic、Standard、または Premium を使用することをお勧めします。 既存のすべての Classic レジストリを、2019 年 4 月より前にアップグレードする必要があります。 アップグレードについては、「[Classic コンテナー レジストリのアップグレード][container-registry-upgrade]」を参照してください。

Basic、Standard、および Premium SKU (総称*管理対象レジストリ*) では、すべて同じプログラム機能が提供されます。 また、これらすべては、Azure によって完全に管理されている[イメージ ストレージ][container-registry-storage]から恩恵を受けられます。 上位の SKU を選択するほど、パフォーマンスとスケールが向上します。 複数のサービス レベルがあることで、最初は Basic を導入し、その後、レジストリの使用量の増加に伴って Standard や Premium に切り替えることができます。

## <a name="sku-feature-matrix"></a>SKU 機能マトリックス

次の表に、Basic、Standard、および Premium サービス階層の機能と制限について説明します。

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>SKU の変更

レジストリの SKU 変更は、Azure CLI または Azure Portal で行うことができます。 変更後の SKU が最大記憶域容量の要件を満たしていれば、管理対象 SKU は自由に移動することができます。 いずれかの管理対象 SKU にクラシックから切り替えると、二度とクラシックに戻すことはできません。あくまで一方向の変換となります。

### <a name="azure-cli"></a>Azure CLI

Azure CLI で SKU を切り替えるには、[az acr update][az-acr-update] コマンドを使用します。 たとえば Premium への切り替えは、次のようにして行います。

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure ポータル

Azure Portal のコンテナー レジストリの **[Overview]\(概要\)** で、**[Update]\(更新\)** を選択し、[SKU] ドロップダウン リストから新しい **SKU** を選択します。

![Azure Portal でのコンテナー レジストリ SKU の更新][update-registry-sku]

ご利用のレジストリがクラシック レジストリである場合、管理対象 SKU を Azure Portal 内で選択することはできません。 あらかじめ管理対象レジストリに[アップグレード][container-registry-upgrade]しておく必要があります。

## <a name="pricing"></a>価格

各 Azure Container Registry SKU の価格については、「[Container Registry の価格][container-registry-pricing]」を参照してください。

データ転送の価格に関する詳細については、「[帯域幅の料金詳細](https://azure.microsoft.com/pricing/details/bandwidth/)」を参照してください。 

## <a name="next-steps"></a>次の手順

**Azure Container Registry ロードマップ**

GitHub の [ACR ロードマップ][acr-roadmap]を参照して、サービスの今後の機能に関する情報を確認してください。

**Azure Container Registry UserVoice**

[ACR UserVoice][container-registry-uservoice] で、新機能に関する提案を送信し、投票してください。

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
