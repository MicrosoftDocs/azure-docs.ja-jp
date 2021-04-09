---
title: Azure CLI のサンプル - Azure SignalR Service
description: 実際のサンプルに従って、Azure SignalR サービスの Azure CLI について学習します。 多くの Azure サービスを使用して SignalR サービスを作成する方法についても説明します。
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 99ebe9c078ef4775e8f0df0545fb0af4b3d1057b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86515242"
---
# <a name="azure-cli-reference"></a>Azure CLI リファレンス

次の表は、Azure CLI を使った Azure SignalR Service 用 Bash スクリプトへのリンクをまとめたものです。

| スクリプト | 説明 |
|-|-|
|**作成**||
| [Create a new SignalR Service and resource group (新しい SignalR Service とリソース グループの作成)](scripts/signalr-cli-create-service.md) | ランダムな名前を使って、新しいリソース グループ内に新しい Azure SignalR Service リソースを作成します。  |
|**統合**||
| [Create a new SignalR Service and Web App configured to use SignalR (新しい SignalR Service と、SignalR を使うよう構成された Web アプリの作成)](scripts/signalr-cli-create-with-app-service.md) | ランダムな名前を使って、新しいリソース グループ内に新しい Azure SignalR Service リソースを作成します。 また、SignalR サービスを使用する ASP.NET Core Web アプリをホストするための、新しい Web アプリと App Service プランを追加します。 この Web アプリは、新しい SignalR サービス リソースに接続するためのアプリ設定を使って構成されます。 |
| [Create a new SignalR Service and Web App configured to use SignalR, and GitHub OAuth (新しい SignalR Service と、SignalR を使うよう構成された Web アプリの作成)](scripts/signalr-cli-create-with-app-service-github-oauth.md) | ランダムな名前を使って、新しいリソース グループ内に新しい Azure SignalR Service リソースを作成します。 また、SignalR サービスを使用する ASP.NET Core Web アプリをホストするための、新しい Azure Web アプリとホスティング プランを追加します。 この Web アプリは、新しい SignalR サービス リソースへの接続文字列と、[GitHub 認証](https://developer.github.com/v3/guides/basics-of-authentication/) ([認証チュートリアル](signalr-concept-authenticate-oauth.md)で説明されています) をサポートするためのクライアント シークレットを使ったアプリ設定によって構成されます。 また、この Web アプリはローカル Git リポジトリのデプロイ ソースを使うように構成されます。 |
| | |
