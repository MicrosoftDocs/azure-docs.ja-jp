---
title: Azure Service Fabric Mesh アプリケーションのシークレットの格納と使用 | Microsoft Docs
description: Service Fabric Mesh シークレットの格納と使用。
services: service-fabric-mesh
keywords: secrets
author: v-steg
ms.author: v-steg
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 4268356db5f46e92862e19d6391cfe5a94511270
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58007481"
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
