---
title: Azure Service Fabric Mesh アプリケーションのシークレットの格納と使用
description: Service Fabric Mesh では、Azure リソースとしてシークレットがサポートされています。 ここでは、Service Fabric Mesh アプリケーションを使用してシークレットを格納および管理する方法について説明します。
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 85b4bb6ee4d76a7115c9ebdd1466049afe6683c6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625534"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric Mesh アプリケーションのシークレット

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)に関する記事を参照してください。

Service Fabric Mesh では、Azure リソースとしてシークレットがサポートされています。 Service Fabric Mesh のシークレットには、格納や送信を安全に行う必要がある、機密性の高いテキスト情報を指定できます (ストレージ接続文字列やパスワードなど)。

![Mesh シークレットの概要][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Mesh シークレットのリソース
Mesh アプリケーションのシークレットは、次の要素で構成されます。
* **シークレット** リソース: シークレットのテキストを格納するコンテナーです。 **シークレット** リソース内に含まれている機密情報は、安全に格納、送信されます。
* 1 つ以上の **シークレット/値** リソース: **シークレット** リソース コンテナーに格納されます。 各 **シークレット/値** リソースは、バージョン番号によって区別されます。

## <a name="next-steps"></a>次のステップ 
Service Fabric Mesh のシークレットについて詳しくは、以下の記事をご覧ください。
- [Azure Service Fabric Mesh アプリケーションのシークレットを管理する](service-fabric-mesh-howto-manage-secrets.md)
- [Service Fabric リソース モデルの概要](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
