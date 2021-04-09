---
title: Azure Service Bus - Shared Access Signature 承認
description: Azure Active Directory Access Control Service から Shared Access Signature 承認への移行について学習します。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e8cd12ac97020417f9958beded1fd198dd485fff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88064623"
---
# <a name="service-bus---migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Service Bus - Azure Active Directory Access Control Service から Shared Access Signature 承認への移行

Service Bus アプリケーションでは、以前は 2 つの承認モデルが存在していました。Service Bus により直接提供される [Shared Access Signature (SAS)](service-bus-sas.md) トークン モデルと、承認ルールの管理が [Azure Active Directory](../active-directory/index.yml) Access Control Service (ACS) によって内部管理される統合モデルです。この統合モデルでは、ACS から取得したトークンが、目的の機能へのアクセスを承認するために Service Bus に渡されます。

長い間、ACS 承認モデルよりも [SAS 承認](service-bus-authentication-and-authorization.md) のほうが推奨モデルとして優勢で、現在あらゆるドキュメント、ガイダンス、サンプルで SAS のみが扱われています。 さらに、ACS とペアになる新しい Service Bus 名前空間を作成することはできません。

SAS は、別のサービスに即時依存することはなく、SAS ルール名とルール キーへのクライアント アクセスを提供することにより、仲介機能なしでクライアントから直接使用できるという利点があります。 また SAS は、クライアントが別のサービスの承認チェックを通過してからトークンの発行を受ける手法にも簡単に統合できます。 後者のアプローチは ACS を使用するパターンと似ていますが、ACS では表現することが困難なアプリケーション固有の条件に基づいてアクセス トークンを発行することが可能です。

ACS に依存しているすべての既存アプリケーションに対して、代わりとして SAS に依存するようにアプリケーションを移行することをお勧めします。

## <a name="migration-scenarios"></a>移行シナリオ

ACS と Service Bus は、共有知識である *署名キー* を通して統合されます。 ACS 名前空間は署名キーを使用して認証トークンを署名します。Service Bus は署名キーを使用して、ペアの ACS 名前空間によってトークンが発行されていることを確認します。 ACS 名前空間は、サービス ID と承認規則を保持します。 承認ルールでは、外部 ID プロバイダーによって発行されたどのサービス ID とトークンが、Service Bus 名前空間グラフの一部に対しどのようなタイプのアクセスが可能かを、最長プレフィックス一致の形で定義します。

たとえば ACS ルールでは、パス プレフィックス `/` への **送信** 要求をサービス ID に付与できます。つまり、そのルールに基づいて ACS により発行されたトークンは、名前空間内のすべてのエンティティに対して送信する権限をクライアントに付与します。 パス プレフィックスが `/abc` の場合、ID による送信は `abc` という名前のエンティティか、またはそのプレフィックスの下に編成されたエンティティに制限されます。 この移行ガイドの読者は、すでにこれらの概念についてよく知っていることを前提としています。

移行シナリオは、大きく分けて 3 つのカテゴリに分類されます。

1.  **既定値を変更しない**。 自動生成される **所有者** サービス ID と ACS 名前空間の秘密キーを Service Bus 名前空間と組み合わせて渡す [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) オブジェクトを使用し、新しいルールを追加していない場合があります。

2.  **単純なルールのカスタム サービス ID**。 新しいサービス ID を追加し、新しいサービス ID それぞれに、ある特定のエンティティに対する **送信**、**リッスン**、および **管理** のアクセス許可を付与している場合があります。

3.  **複雑なルールのカスタム サービス ID**。 外部で発行されたトークンをリレーの権限にマップする、または 1 つのサービス ID を複数のルールを通して複数の名前空間パスの異なる権限に割り当てる複雑なルール セットを設定している場合がごくまれにあります。

複雑なルール セットの移行については、[Azure サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。 その他の 2 つのシナリオでは簡単に移行できます。

### <a name="unchanged-defaults"></a>既定値を変更しない

アプリケーションで ACS の既定値を変更していない場合は、すべての [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) の使用を [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) オブジェクトに置き換えることができます。ACS **所有者** アカウントの代わりに、あらかじめ構成された名前空間 **RootManageSharedAccessKey** を使用できます。 ACS **所有者** アカウントの場合であっても、この構成は一般的に推奨されるものではありませんでした (現在でも推奨されていません) が、それは、このアカウント/ルールにより、あらゆるエンティティの削除権限を含め、名前空間の完全な管理権限が付与されるためです。

### <a name="simple-rules"></a>単純なルール

アプリケーションが単純なルールのカスタム サービス ID を使用する場合、特定のキューのアクセスを制御する ACS サービス ID が作成されていれば移行は簡単です。 このシナリオは SaaS スタイルのソリューションのケースで多く見られます。このケースでは、各キューがテナント サイトやブランチ オフィスへのブリッジとして使用され、その特定のサイトに対してサービス ID が作成されます。 ここでは、それぞれのサービス ID は、キューで直接 Shared Access Signature ルールに移行できます。 サービス ID の名前を SAS ルールの名前にして、サービス ID キーを SAS ルール キーにすることができます。 そうすることで、SAS ルールの権限は、エンティティに対しそれぞれ該当する ACS ルールと同等に構成されます。

この新しい追加の SAS 構成を ACS と統合された既存の名前空間に配置し、その後、ACS からの移行を [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) ではなく [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) を使用して実行します。 名前空間では ACS からのリンクを解除する必要はありません。

### <a name="complex-rules"></a>複雑なルール

SAS ルールをアカウントにすることは想定されていませんが、権限に関連付けられた署名キーの名前が付けられています。 アプリケーションにより多くのサービス ID が作成され、複数のエンティティや名前空間全体に対してアクセス権限が付与されるようなシナリオでは、引き続きトークンを発行する仲介役が必要となります。 このような仲介機能のガイダンスを入手するには、[サポートにお問い合わせください](https://azure.microsoft.com/support/options/)。

## <a name="next-steps"></a>次のステップ

Service Bus 認証の詳細については、次のトピックをご覧ください。

* [Service Bus の認証と承認](service-bus-authentication-and-authorization.md)
* [Shared Access Signature による Service Bus の認証](service-bus-sas.md)