---
title: Azure Blockchain Workbench プレビューの概要
description: Azure Blockchain Workbench プレビューとその機能の概要。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 097185502321c8810214ed737047bdf596d18bdb
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844106"
---
# <a name="what-is-azure-blockchain-workbench"></a>Azure Blockchain Workbench とは

Azure Blockchain Workbench プレビューは、ブロックチェーン アプリケーションを作成してデプロイし、ビジネス プロセスやデータを他の組織と効果的に共有できるよう設計された、Azure サービスと機能のコレクションです。 Azure Blockchain Workbench は、ブロックチェーン アプリケーションを構築し、開発者がビジネス ロジックやスマート コントラクトの作成に集中できるようにするための、インフラストラクチャ スキャフォールディングを提供します。 また、Azure のサービスや機能を統合することでブロックチェーン アプリケーションの作成を容易にし、共通の開発タスクを自動化しやすくします。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="create-blockchain-applications"></a>ブロックチェーン アプリケーションの作成

Blockchain Workbench では、構成とスマート コントラクト コードを使用してブロックチェーン アプリケーションを定義できます。 開発者は、ブロックチェーン アプリケーションの開発をすばやく開始できるので、スキャフォールディングの構築やサポート サービスの設定に手間取ることなく、コントラクトの定義やビジネス ロジックの作成に集中できます。

## <a name="manage-applications-and-users"></a>アプリケーションとユーザーの管理

Azure Blockchain Workbench では、ブロックチェーン アプリケーションとユーザーを管理するための Web アプリケーションと REST API が提供されます。 Blockchain Workbench の管理者は、アプリケーションへのアクセスを管理し、ユーザーをアプリケーション ロールに割り当てることができます。 Azure AD のユーザーは、アプリケーション内のメンバーに自動的にマップされます。

## <a name="integrate-blockchain-with-applications"></a>ブロックチェーンとアプリケーションの統合

開発者は、Blockchain Workbench の REST API とメッセージ ベース API を使用して、ブロックチェーンを既存のシステムと統合できます。 これらの API は、複数の分散型台帳テクノロジー、ストレージ、およびデータベース ソリューションを置き換えたり、使用したりするためのインターフェイスを提供します。

Blockchain Workbench では、メッセージ ベース API に送信されたメッセージを変換して、そのブロックチェーンのネイティブ API で受け付けられる形式のトランザクションを作成することができます。  また Workbench では、トランザクションに署名をし、それを適切なブロックチェーンにルーティングできます。 

Workbench は、Service Bus と Event Grid にイベントを自動的に配信し、下流のコンシューマーにメッセージを送信します。 開発者は、これらのメッセージング システムのいずれかを統合してトランザクションを実行し、結果を確認できます。

## <a name="deploy-a-blockchain-network"></a>ブロックチェーン ネットワークのデプロイ

Azure Blockchain Workbench では、Azure Resource Manager ソリューション テンプレートを使用し、コンソーシアムのブロックチェーン ネットワーク設定を事前構成済みのソリューションとして簡略化できます。 このテンプレートにより、コンソーシアムの実行に必要なすべてのコンポーネントを簡単にデプロイできます。 現在、Blockchain Workbench では Ethereum がサポートされています。

## <a name="use-active-directory"></a>Active Directory を使用する

既存のブロックチェーン プロトコルでは、ブロックチェーンの ID はネットワーク上のアドレスとして表されます。 Azure Blockchain Workbench では、ブロックチェーン ID を Active Directory ID に関連付けることで ID が抽象化されるので、Active Directory ID を使用したエンタープライズ アプリケーションをより簡単に構築することができます。

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>オンチェーン データとオフチェーン ストレージの同期

Azure Blockchain Workbench では、ブロックチェーン上のデータをオフチェーンのストレージに自動的に同期することで、ブロックチェーン イベントを簡単に分析できます。 ブロックチェーンから直接データを抽出するのではなく、SQL Server などのオフチェーン データベース システムからデータを照会できます。 データ分析タスクを実行するエンド ユーザーに、ブロックチェーンの専門知識は必要ありません。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench アーキテクチャ](architecture.md)
