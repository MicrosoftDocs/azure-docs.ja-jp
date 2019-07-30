---
title: Azure Service Fabric Mesh アプリケーションのシークレットの格納と使用 | Microsoft Docs
description: Service Fabric Mesh シークレットの格納と使用。
services: service-fabric-mesh
keywords: secrets
author: v-steg
ms.author: jeconnoc
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: b5cfa93298222d914069b6ab63deb8ba8a9b59c3
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875342"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric Mesh アプリケーションのシークレット
Service Fabric Mesh では、Azure リソースとしてシークレットがサポートされています。 Service Fabric Mesh のシークレットには、格納や送信を安全に行う必要がある、機密性の高いテキスト情報を指定できます (ストレージ接続文字列やパスワードなど)。

![Mesh シークレットの概要][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Mesh シークレットのリソース
Mesh アプリケーションのシークレットは、次の要素で構成されます。
* **シークレット** リソース: シークレットのテキストを格納するコンテナーです。 **シークレット** リソース内に含まれている機密情報は、安全に格納、送信されます。
* 1 つ以上の**シークレット/値**リソース: **シークレット** リソース コンテナーに格納されます。 各**シークレット/値**リソースは、バージョン番号によって区別されます。

## <a name="next-steps"></a>次の手順 
Service Fabric Mesh のシークレットについて詳しくは、以下の記事をご覧ください。
- [Azure Service Fabric Mesh アプリケーションのシークレットを管理する](service-fabric-mesh-howto-manage-secrets.md)
- [Service Fabric リソース モデルの概要](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
