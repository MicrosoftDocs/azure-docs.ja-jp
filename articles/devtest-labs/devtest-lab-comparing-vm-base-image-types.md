---
title: DevTest Labs のカスタム イメージと数式の比較 | Microsoft Docs
description: 環境に最適なものを判断できるように、VM ベースとして使用するカスタム イメージと数式の違いについて説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: df7a8e6209f8033eb5a29c65079e9c2f4cbbe544
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87287525"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>DevTest ラボのカスタム イメージと数式の比較
[カスタム イメージ](devtest-lab-create-template.md)と[数式](devtest-lab-manage-formulas.md)の両方を、[新しく作成した VM](devtest-lab-add-vm.md) のベースとして使用することができます。 ただし、カスタム イメージが単に VHD に基づくイメージであるのに対して、数式は VM サイズ、仮想ネットワーク、サブネット、アーティファクトなどの構成済み設定 *に加え*、VHD に基づくイメージであるという重要な相違点があります。 これらの構成済みの設定は、VM の作成時にオーバーライド可能な既定値で設定されます。 この記事では、カスタム イメージを使用する場合と数式を使用する場合の利点 (長所) と欠点 (短所) についていくつか説明します。

## <a name="custom-image-pros-and-cons"></a>カスタム イメージの長所と短所
カスタム イメージでは、必要な環境から VM を作成するための静的で不変の方法が提供されます。 

**長所**

* イメージからの VM のスピンアップ後は何も変わらないため、カスタム イメージからすばやく VM をプロビジョニングできます。 つまり、カスタム イメージは設定のない単なるイメージであるため、適用する設定はありません。 
* 1 つのカスタム イメージから作成される VM は同じです。

**短所**

* カスタム イメージの一部の要素を更新する必要がある場合、イメージを再作成する必要があります。  

## <a name="formula-pros-and-cons"></a>数式の長所と短所
数式では、必要な構成/設定から VM を作成するための動的な方法が提供されます。

**長所**

* 環境の変更は、アーティファクトを介してすぐにキャプチャできます。 たとえば、リリース パイプラインから最新ビットの VM をインストールする必要がある場合、またはリポジトリから最新コードを登録する場合は、最新のビットをデプロイするか、ターゲットのベース イメージと共に数式に最新のコードを登録するアーティファクトを単に指定できます。 VM を作成するためにこの数式が使用される場合は常に、最新のビット/コードが VM にデプロイ/登録されます。 
* 数式では、VM サイズや仮想ネットワークの設定など、カスタム イメージで指定できない既定の設定を定義できます。 
* 数式で保存されている設定は既定値として表示されますが、VM の作成時に変更できます。 

**短所**

* 数式から VM を作成すると、カスタム イメージから VM を作成する場合よりも時間がかかることがあります。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>関連するブログ記事
* [Custom images or formulas? (カスタム イメージか数式か?)](./devtest-lab-faq.md#blog-post)

## <a name="next-steps"></a>次のステップ
- [DevTest Labs に関する FAQ](devtest-lab-faq.md)
