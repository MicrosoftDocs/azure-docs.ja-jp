---
title: Azure Container Registry でレジストリの正常性をチェックする
description: Azure コンテナー レジストリの使用時の一般的な問題 (ローカル Docker の構成やレジストリへの接続性など) を特定するためのクイック診断コマンドを実行する方法について説明します。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3511655d220ee85ce6b5744612e5d6fddafbe877
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309735"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Azure コンテナー レジストリの正常性のチェック

Azure コンテナー レジストリを使用しているときに、問題が発生する場合があります。 たとえば、ローカル環境の Docker に問題があるために、コンテナー イメージをプルできない場合があります。 また、ネットワークの問題により、レジストリに接続できない場合もあります。 

最初の診断手順として、[az acr check-health][az-acr-check-health] を実行します。command to get information about the health of the environment and optionally access to a target registry. This command is available in Azure CLI version 2.0.67 or later. If you need to install or upgrade, see [Install Azure CLI][azure-cli]

## <a name="run-az-acr-check-health"></a>az acr check-health を実行する

以下の例は、`az acr check-health` コマンドを実行するさまざまな方法を示しています。

> [!NOTE]
> このコマンドを Azure Cloud Shell で実行した場合、ローカル環境はチェックされません。 ただし、ターゲット レジストリへのアクセスはチェックできます。

### <a name="check-the-environment-only"></a>環境のみをチェックする

ローカル Docker デーモン、CLI のバージョン、Helm クライアントの構成をチェックするには、パラメーターを追加せずにコマンドを実行します。

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>環境とターゲット レジストリをチェックする

レジストリへのアクセスをチェックし、ローカル環境チェックを実行するには、ターゲット レジストリの名前を渡します。 例:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>エラー報告

このコマンドは標準出力に情報を記録します。 問題が検出されると、エラー コードと説明が示されます。 各コードと考えられる解決策の詳細については、[エラー リファレンス](container-registry-health-error-reference.md)のページをご覧ください。

既定では、コマンドはエラーを検出するたびに停止します。 エラーが検出された場合でも、すべての正常性チェックの出力を提供するようにコマンドを実行することもできます。 次の例に示すように、`--ignore-errors` パラメーターを追加します。

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

サンプル出力:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>次の手順

[az acr check-health][az-acr-check-health] コマンドによって返されるエラー コードの詳細については、「[正常性チェックのエラー リファレンス](container-registry-health-error-reference.md)」をご覧ください。

Azure Container Registry に関するよくあるご質問や他の既知の問題については、[FAQ](container-registry-faq.md) をご覧ください。





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
