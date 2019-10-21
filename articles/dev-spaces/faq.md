---
title: Azure Dev Spaces についてよく寄せられる質問
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: Azure Dev Spaces についての一般的ないくつかの質問にお答えします
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s '
ms.openlocfilehash: de44bf323b300a4467dc8b30555798f557898494
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71305914"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Azure Dev Spaces についてよく寄せられる質問

Azure Dev Spaces についてよく寄せられる質問に回答します。

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>現在はどの Azure リージョンで Azure Dev Spaces が提供されていますか。

利用可能なリージョンの完全な一覧については、[サポートされているリージョンと構成][supported-regions]に関するページを参照してください。

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>パブリック IP アドレスなしで Azure Dev Spaces を使用できますか。

いいえ。パブリック IP を持たない AKS クラスター上に Azure Dev Spaces をプロビジョニングすることはできません。 パブリック IP は、[ルーティングのために Azure Dev Spaces に必要][dev-spaces-routing]です。

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Azure Dev Spaces で独自のイングレスを使用できますか。

はい。Azure Dev Spaces によって作成されるイングレスと共に、独自のイングレスを構成できます。 たとえば、[traefik][ingress-traefik] を使用できます。

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Azure Dev Spaces で HTTPS を使用できますか。

はい。[traefik][ingress-https-traefik] を使用して、HTTPS で独自のイングレスを構成できます。

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>kubenet ではなく CNI を使用するクラスター上で Azure Dev Spaces を使用できますか。 

はい。ネットワークに CNI を使用する AKS クラスター上で、Azure Dev Spaces を使用できます。 たとえば、ネットワークに CNI を使用する[既存の Windows コンテナー][windows-containers]がある AKS クラスター上で、Azure Dev Spaces を使用できます。

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Azure Dev Spaces で Windows コンテナーを使用できますか。

現時点では、Azure Dev Spaces は Linux ポッドおよびノード上でのみ実行することが想定されていますが、[既存の Windows コンテナー][windows-containers]を持つ AKS クラスター上で Azure Dev Spaces を実行できます。


[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md