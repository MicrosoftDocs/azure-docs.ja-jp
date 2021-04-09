---
title: Azure Instance Metadata Service を使用してロード バランサーの情報を取得する
titleSuffix: Azure Load Balancer
description: Azure Instance Metadata Service を使用してロード バランサーの情報を取得する方法についての学習を開始します。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 0d7d08eb5e77e744fb6ce0abefc550bc79de4c8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519084"
---
# <a name="retrieve-load-balancer-information-by-using-the-azure-instance-metadata-service"></a>Azure Instance Metadata Service を使用してロード バランサーの情報を取得する

IMDS (Azure Instance Metadata Service) によって、現在実行中の仮想マシン インスタンスに関する情報が提供されます。 このサービスは、既知のルーティング不可能な IP アドレス (169.254.169.254) で使用できる REST API です。 

仮想マシンまたは仮想マシン セットのインスタンスを Azure Standard Load Balancer の背後に配置する場合は、IMDS を使用して、ロード バランサーとインスタンスに関連するメタデータを取得します。

メタデータには、仮想マシンまたは仮想マシン スケール セットに関する次の情報が含まれています。

* Standard SKU のパブリック IP。
* ネットワーク インターフェイスの各プライベート IP のロード バランサーのインバウンド規則構成。
* ネットワーク インターフェイスの各プライベート IP のロード バランサーのアウトバウンド規則構成。

## <a name="access-the-load-balancer-metadata-using-the-imds"></a>IMDS を使用したロード バランサーのメタデータへのアクセス

ロード バランサーのメタデータにアクセスする方法の詳細については、[Azure Instance Metadata Service を使用したロード バランサー情報へのアクセス](howto-load-balancer-imds.md)に関するページを参照してください。

## <a name="troubleshoot-common-error-codes"></a>一般的なエラー コードのトラブルシューティング

一般的なエラー コードとその軽減方法の詳細については、[IMDS を使用した場合の一般的なエラー コードのトラブルシューティング](troubleshoot-load-balancer-imds.md)に関するページを参照してください。 

## <a name="support"></a>サポート

複数回試行してもメタデータの応答を取得できない場合は、Azure portal でサポート イシューを作成してください。

## <a name="next-steps"></a>次のステップ
[Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md) についてさらに詳しく学習する

[Standard ロード バランサーをデプロイする](quickstart-load-balancer-standard-public-portal.md)

