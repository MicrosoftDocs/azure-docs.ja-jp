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
ms.openlocfilehash: c5b07c1059e146aaf3c3c2a26038514eb614a3cc
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505921"
---
# <a name="client-libraries-for-azure-key-vault"></a>Azure Key Vault 用クライアント ライブラリ

Azure Key Vault 用クライアント ライブラリを使用すると、.NET、Python、Java、Javascript など、さまざまな言語から Key Vault の機能にプログラムでアクセスできます。

## <a name="client-libraries-per-language-and-object"></a>言語およびオブジェクトごとのクライアント ライブラリ

各 SDK には、次の表に示すように、キー コンテナー、シークレット、キー、および証明書用に個別のクライアント ライブラリがあります。

| 言語 | シークレット | [キー] | 証明書 | Key Vault (管理プレーン) |
|--|--|--|--|--|
| .NET | - [API リファレンス](/dotnet/api/azure.security.keyvault.secrets)<br>- [NuGet パッケージ](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [クイックスタート](../secrets/quick-create-net.md) | - [API リファレンス](/dotnet/api/azure.security.keyvault.keys)<br>- [NuGet パッケージ](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys)<br>- [クイックスタート](../keys/quick-create-net.md) | - [API リファレンス](/dotnet/api/azure.security.keyvault.certificates)<br>- [NuGet パッケージ](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates)<br>- [クイックスタート](../certificates/quick-create-net.md) | - [API リファレンス](/dotnet/api/microsoft.azure.management.keyvault)<br>- [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [API リファレンス](/python/api/overview/azure/keyvault-secrets-readme)<br>- [PyPi パッケージ](https://pypi.org/project/azure-keyvault-secrets/)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [クイックスタート](../secrets/quick-create-python.md) |- [API リファレンス](/python/api/overview/azure/keyvault-keys-readme)<br>- [PyPi パッケージ](https://pypi.org/project/azure-keyvault-keys/)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [クイックスタート](../keys/quick-create-python.md) | - [API リファレンス](/python/api/overview/azure/keyvault-certificates-readme)<br>- [PyPi パッケージ](https://pypi.org/project/azure-keyvault-certificates/)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [クイックスタート](../certificates/quick-create-python.md) | - [API リファレンス](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br> - [PyPi パッケージ](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [API リファレンス](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [クイックスタート](../secrets/quick-create-java.md) |- [API リファレンス](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)<br>- [クイックスタート](../keys/quick-create-java.md) | - [API リファレンス](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)<br>- [クイックスタート](../certificates/quick-create-java.md) |- [API リファレンス](/java/api/com.microsoft.azure.management.keyvault)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/mgmt-v2016_10_01)|
| Node.js | - [API リファレンス](/javascript/api/@azure/keyvault-secrets/)<br>- [npm パッケージ](https://www.npmjs.com/package/@azure/keyvault-secrets)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [クイックスタート](../secrets/quick-create-node.md) |- [API リファレンス](/javascript/api/@azure/keyvault-keys/)<br>- [npm パッケージ](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)<br>- [クイックスタート](../keys/quick-create-node.md)| - [API リファレンス](/javascript/api/@azure/keyvault-certificates/)<br>- [npm パッケージ](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates)<br>- [クイックスタート](../certificates/quick-create-node.md) |  - [API リファレンス](/javascript/api/@azure/arm-keyvault/)<br>- [npm パッケージ](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>次の手順

- 「[Azure Key Vault 開発者ガイド](developers-guide.md)」を参照する
- [Key Vault に対する認証](authentication.md)の詳細を参照する
- [キー コンテナーへのアクセスをセキュリティで保護する方法](secure-your-key-vault.md)の詳細を参照する
