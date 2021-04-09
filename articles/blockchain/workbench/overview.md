---
title: Azure Blockchain Workbench プレビューの概要
description: Azure Blockchain Workbench プレビューとその機能の概要。
ms.date: 05/22/2020
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: fbd6be3907dbd10b003d065dfb14031a0e378478
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87003151"
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

Blockchain Workbench では、メッセージ ベース API に送信されたメッセージを変換して、そのブロックチェーンのネイティブ API で想定される形式のトランザクションを作成することができます。  また Workbench では、トランザクションに署名をし、それを適切なブロックチェーンにルーティングできます。 

Workbench は、Service Bus と Event Grid にイベントを自動的に配信し、下流のコンシューマーにメッセージを送信します。 開発者は、これらのメッセージング システムのいずれかを統合してトランザクションを実行し、結果を確認できます。

## <a name="deploy-a-blockchain-network"></a>ブロックチェーン ネットワークのデプロイ

Azure Blockchain Workbench では、Azure Resource Manager ソリューション テンプレートを使用し、コンソーシアムのブロックチェーン ネットワーク設定を事前構成済みのソリューションとして簡略化できます。 このテンプレートにより、コンソーシアムの実行に必要なすべてのコンポーネントを簡単にデプロイできます。 現在、Blockchain Workbench では Ethereum がサポートされています。

## <a name="use-active-directory"></a>Active Directory を使用する

既存のブロックチェーン プロトコルでは、ブロックチェーンの ID はネットワーク上のアドレスとして表されます。 Azure Blockchain Workbench では、ブロックチェーン ID を Active Directory ID に関連付けることで ID が抽象化されるので、Active Directory ID を使用したエンタープライズ アプリケーションをより簡単に構築することができます。

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>オンチェーン データとオフチェーン ストレージの同期

Azure Blockchain Workbench では、ブロックチェーン上のデータをオフチェーンのストレージに自動的に同期することで、ブロックチェーン イベントを簡単に分析できます。 ブロックチェーンから直接データを抽出するのではなく、SQL Server などのオフチェーン データベース システムからデータを照会できます。 データ分析タスクを実行するエンド ユーザーに、ブロックチェーンの専門知識は必要ありません。

## <a name="support-and-feedback"></a>サポートとフィードバック

Azure Blockchain の最新情報については、[Azure Blockchain のブログ](https://azure.microsoft.com/blog/topics/blockchain/)をご覧ください。Azure Blockchain エンジニアリング チームからの情報やブロックチェーン サービスの内容に関する最新の情報を把握することができます。

製品に関するフィードバックや新機能のご要望をお寄せいただくには、[ブロックチェーンに関する Azure フィードバック フォーラム](https://aka.ms/blockchainuservoice)で投稿またはアイデアに投票してください。

### <a name="community-support"></a>コミュニティ サポート

Microsoft のエンジニアや Azure Blockchain コミュニティのエキスパートと交流できます。

* [Azure Blockchain Workbench に関する Microsoft Q&A 質問ページ](/answers/topics/azure-blockchain-workbench.html)
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench アーキテクチャ](architecture.md)
