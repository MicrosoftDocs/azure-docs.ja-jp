---
title: Azure CLI のサンプル - Azure SignalR Service
description: Azure CLI のサンプル - Azure SignalR Service
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 1fbe96d827bcf6bb39a6cf7a4e6811174b862d59
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57992198"
---
# <a name="azure-cli-reference"></a>Azure CLI リファレンス

次の表は、Azure CLI を使った Azure SignalR Service 用 Bash スクリプトへのリンクをまとめたものです。

| | |
|-|-|
|**作成**||
| [Create a new SignalR Service and resource group (新しい SignalR Service とリソース グループの作成)](scripts/signalr-cli-create-service.md) | ランダムな名前を使って、新しいリソース グループ内に新しい Azure SignalR Service リソースを作成します。  |
|**統合**||
| [Create a new SignalR Service and Web App configured to use SignalR (新しい SignalR Service と、SignalR を使うよう構成された Web アプリの作成)](scripts/signalr-cli-create-with-app-service.md) | ランダムな名前を使って、新しいリソース グループ内に新しい Azure SignalR Service リソースを作成します。 また、SignalR サービスを使用する ASP.NET Core Web アプリをホストするための、新しい Web アプリと App Service プランを追加します。 この Web アプリは、新しい SignalR サービス リソースに接続するためのアプリ設定を使って構成されます。 |
| [Create a new SignalR Service and Web App configured to use SignalR, and GitHub OAuth (新しい SignalR Service と、SignalR を使うよう構成された Web アプリの作成)](scripts/signalr-cli-create-with-app-service-github-oauth.md) | ランダムな名前を使って、新しいリソース グループ内に新しい Azure SignalR Service リソースを作成します。 また、SignalR サービスを使用する ASP.NET Core Web アプリをホストするための、新しい Azure Web アプリとホスティング プランを追加します。 この Web アプリは、新しい SignalR サービス リソースへの接続文字列と、[GitHub 認証](https://developer.github.com/v3/guides/basics-of-authentication/) ([認証チュートリアル](signalr-concept-authenticate-oauth.md)で説明されています) をサポートするためのクライアント シークレットを使ったアプリ設定によって構成されます。 また、この Web アプリはローカル Git リポジトリのデプロイ ソースを使うように構成されます。 |
| | |
