---
title: Azure Service Fabric Mesh CLI の設定 | Microsoft Docs
description: Azure Service Fabric Mesh CLI を設定する方法について説明します。
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: c716ae0a2bb30e7e8eb249a1d230097efc0d3795
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521018"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric Mesh CLI の設定
Service Fabric Mesh コマンド ライン インターフェイス (CLI) は、ローカルおよび Azure Service Fabric Mesh でリソースをデプロイして管理するために必要です。 

使用できる3 種類の CLI を次の表にまとめています。 

| CLI モジュール | ターゲット環境 |  説明 | 
|---|---|---|
| az mesh | Azure Service Fabric Mesh | プライマリ CLI。Azure Service Fabric Mesh 環境でアプリケーションをデプロイしてリソースを管理するために使用できます。 
| sfctl | ローカル クラスター | Service Fabric CLI。ローカル クラスターに対する Service Fabric リソースのデプロイメントとテストに使用できます。  
| Maven CLI | ローカル クラスターおよび Azure Service Fabric Mesh | `az mesh` と `sfctl` のラッパー。Java 開発者は、ローカルおよび Azure の開発エクスペリエンスで使い慣れたコマンド ライン エクスペリエンスを使用できます。  

プレビューでは、Azure Service Fabric Mesh CLI は Azure CLI の拡張機能として記述されています。 これは、Azure Cloud Shell または Azure CLI のローカル インストールでインストールできます。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Azure Service Fabric Mesh CLI をインストールする
1. Azure CLI バージョン 2.0.43 以降をインストールする必要があります。 バージョンを確認するには、`az --version` を実行します。 最新バージョンの CLI をインストールするか、最新バージョンにアップグレードする場合は、「[Azure CLI のインストール][azure-cli-install]」を参照してください。

2. 次のコマンドを使用して、Azure Service Fabric Mesh CLI 拡張モジュールをインストールします。 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. 既存の Azure Service Fabric Mesh CLI モジュールを更新するには、次のコマンドを使用します。

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>Service Fabric CLI (sfctl) をインストールする 

[Service Fabric CLI のセットアップ](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)に関するページの手順に従います。 **sfctl** モジュールは、ローカル マシン上の Service Fabric クラスターに対して、リソース モデルに基づいてアプリケーションのデプロイメントに使用できます。 

## <a name="install-the-maven-cli"></a>Maven CLI をインストールする 

Maven CLI を使用するには、コンピューターに次をインストールする必要があります。 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Azure Mesh CLI (az mesh) - Azure Service Fabric Mesh 対象 
* SFCTL (sfctl) - ローカル クラスター対象 

Service Fabric 用の Maven CLI はまだプレビュー段階です。 

Maven プラグインを Maven Java アプリで使用するには、次のスニペットを pom.xml ファイルに追加します。

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

使用方法について詳しくは、[Maven CLI リファレンス](service-fabric-mesh-reference-maven.md)のセクションをご覧ください。

## <a name="next-steps"></a>次の手順

[Windows 開発環境](service-fabric-mesh-howto-setup-developer-environment-sdk.md)を設定することもできます。

[よく寄せられる質問と問題](service-fabric-mesh-faq.md)に対する回答を確認します。

[azure-cli-install]: /cli/azure/install-azure-cli
