---
title: Azure Relay - Shared Access Signature 承認への移行
description: Azure Active Directory Access Control Service の使用から Shared Access Signature 承認への Azure Relay アプリケーションの移行方法について説明します。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 3b793173270b0ddf25f0e971dbb2fed97cb10a55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87532868"
---
# <a name="azure-relay---migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Azure Relay - Azure Active Directory Access Control Service から Shared Access Signature 承認への移行

Azure Relay アプリケーションではこれまで 2 つの承認モデルが存在していました。リレー サービスにより直接提供される [Shared Access Signature (SAS)](../service-bus-messaging/service-bus-sas.md) トークン モデルと、承認規則の管理が [Azure Active Directory](../active-directory/index.yml) Access Control Service (ACS) によって内部で管理されるフェデレーション モデルです。このモデルでは、ACS から取得したトークンが、目的の機能へのアクセスを承認するためにリレーに渡されます。

長い間、ACS 承認モデルよりも [SAS 承認](../service-bus-messaging/service-bus-authentication-and-authorization.md) のほうが推奨モデルとして優勢で、現在あらゆるドキュメント、ガイダンス、サンプルで SAS のみが扱われています。 また、ACS とペアになるリレーの名前空間を新たに作成することはできません。

SAS は、別のサービスに即時依存することはなく、SAS ルール名とルール キーへのクライアント アクセスを提供することにより、仲介機能なしでクライアントから直接使用できるという利点があります。 また SAS は、クライアントが別のサービスの承認チェックを通過してからトークンの発行を受ける手法にも簡単に統合できます。 後者のアプローチは ACS を使用するパターンと似ていますが、ACS では表現することが困難なアプリケーション固有の条件に基づいてアクセス トークンを発行することが可能です。

ACS に依存しているすべての既存アプリケーションに対して、代わりとして SAS に依存するようにアプリケーションを移行することをお勧めします。

## <a name="migration-scenarios"></a>移行シナリオ

ACS とリレーは、共に認識している *署名キー* を利用して統合されます。 署名キーは、ACS 名前空間では認証トークンの署名に使用され、Azure Relay ではペアの ACS 名前空間によってトークンが発行されていることの確認に使用されます。 ACS 名前空間は、サービス ID と承認規則を保持します。 承認規則では、外部 ID プロバイダーによって発行されたサービス ID またはトークンが、リレー名前空間のグラフの一部に対して、どの種類のアクセスを許可されるかを定義します。定義には最長プレフィックス一致の形式が使用されます。

たとえば ACS ルールでは、パス プレフィックス `/` への **送信** 要求をサービス ID に付与できます。つまり、そのルールに基づいて ACS により発行されたトークンは、名前空間内のすべてのエンティティに対して送信する権限をクライアントに付与します。 パス プレフィックスが `/abc` の場合、ID による送信は `abc` という名前のエンティティか、またはそのプレフィックスの下に編成されたエンティティに制限されます。 この移行ガイドの読者は、すでにこれらの概念についてよく知っていることを前提としています。

移行シナリオは、大きく分けて 3 つのカテゴリに分類されます。

1.  **既定値を変更しない**。 [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) オブジェクトを使用して、自動的に生成された **所有者** のサービス ID および ACS 名前空間の秘密鍵をリレー名前空間とペアにして渡すようにしているお客様もいます。新しいルールは追加しません。

2.  **単純なルールのカスタム サービス ID**。 新しいサービス ID を追加し、新しいサービス ID それぞれに、ある特定のエンティティに対する **送信**、**リッスン**、および **管理** のアクセス許可を付与している場合があります。

3.  **複雑なルールのカスタム サービス ID**。 外部で発行されたトークンをリレーの権限にマップする、または 1 つのサービス ID を複数のルールを通して複数の名前空間パスの異なる権限に割り当てる複雑なルール セットを設定している場合がごくまれにあります。

複雑なルール セットの移行については、[Azure サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。 その他の 2 つのシナリオでは簡単に移行できます。

### <a name="unchanged-defaults"></a>既定値を変更しない

アプリケーションで ACS の既定値を変更していない場合は、すべての [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) の使用を [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) オブジェクトに置き換えることができます。ACS **所有者** アカウントの代わりに、あらかじめ構成された名前空間 **RootManageSharedAccessKey** を使用できます。 ACS **所有者** アカウントの場合であっても、この構成は一般的に推奨されるものではありませんでした (現在でも推奨されていません) が、それは、このアカウント/ルールにより、あらゆるエンティティの削除権限を含め、名前空間の完全な管理権限が付与されるためです。

### <a name="simple-rules"></a>単純なルール

単純なルールを含むカスタムのサービス ID をアプリケーションで使用する場合、ACS サービス ID を作成して特定のリレーのアクセスを制御していたのであれば、移行は簡単です。 このシナリオは、SaaS スタイル ソリューションのケースで多く見られます。このケースでは、各リレーがテナント サイトやブランチ オフィスへのブリッジとして使用され、その特定のサイトに対してサービス ID が作成されます。 ここでは、それぞれのサービス ID は、リレーで直接、Shared Access Signature 規則に移行できます。 サービス ID の名前を SAS ルールの名前にして、サービス ID キーを SAS ルール キーにすることができます。 そうすることで、SAS ルールの権限は、エンティティに対しそれぞれ該当する ACS ルールと同等に構成されます。

この新しい追加の SAS 構成を ACS と統合された既存の名前空間に配置し、その後、ACS からの移行を [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) ではなく [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) を使用して実行します。 名前空間では ACS からのリンクを解除する必要はありません。

### <a name="complex-rules"></a>複雑なルール

SAS ルールをアカウントにすることは想定されていませんが、権限に関連付けられた署名キーの名前が付けられています。 アプリケーションにより多くのサービス ID が作成され、複数のエンティティや名前空間全体に対してアクセス権限が付与されるようなシナリオでは、引き続きトークンを発行する仲介役が必要となります。 このような仲介機能のガイダンスを入手するには、[サポートにお問い合わせください](https://azure.microsoft.com/support/options/)。

## <a name="next-steps"></a>次の手順

Azure Relay 認証の詳細については、次のトピックをご覧ください。

* [Azure Relay の認証と承認](relay-authentication-and-authorization.md)
* [Shared Access Signature による Service Bus の認証](../service-bus-messaging/service-bus-sas.md)
