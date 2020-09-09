---
title: Azure Key Vault 用クライアント ライブラリ | Microsoft Docs
description: Azure Key Vault 用クライアント ライブラリ
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 34321ac9baf3d3cb43dace33b574cb582c2d72a0
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378952"
---
# <a name="client-libraries-for-azure-key-vault"></a>Azure Key Vault 用クライアント ライブラリ

Azure Key Vault 用クライアント ライブラリを使用すると、.NET、Python、Java、Javascript など、さまざまな言語から Key Vault の機能にプログラムでアクセスできます。

## <a name="client-libraries-per-language-and-object"></a>言語およびオブジェクトごとのクライアント ライブラリ

各 SDK には、次の表に示すように、シークレット、キー、および証明書用に個別のクライアント ライブラリがあります。

| 言語 | シークレット | [キー] | 証明書 |
|--|--|--|--|
| .NET | - [API リファレンス](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [NuGet パッケージ](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [クイックスタート](../secrets/quick-create-net.md) | - [API リファレンス](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [NuGet パッケージ](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [API リファレンス](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [NuGet パッケージ](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) |
| Python| - [API リファレンス](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [PyPi パッケージ](https://pypi.org/project/azure-keyvault-secrets/)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [クイックスタート](../secrets/quick-create-python.md) |- [API リファレンス](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [PyPi パッケージ](https://pypi.org/project/azure-keyvault-keys/)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [クイックスタート](../keys/quick-create-python.md) | - [API リファレンス](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [PyPi パッケージ](https://pypi.org/project/azure-keyvault-certificates/)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [クイックスタート](../certificates/quick-create-python.md) |
| Java | - [API リファレンス](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [クイックスタート](../secrets/quick-create-java.md) |- [API リファレンス](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [API リファレンス](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |
| Node.js | - [API リファレンス](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [npm パッケージ](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [クイックスタート](../secrets/quick-create-node.md) |- [API リファレンス](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [npm パッケージ](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [API リファレンス](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [npm パッケージ](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |

## <a name="next-steps"></a>次の手順

- 「[Azure Key Vault 開発者ガイド](developers-guide.md)」を参照する
- 「[Key Vault に対する認証](authentication.md)」の詳細を参照する
