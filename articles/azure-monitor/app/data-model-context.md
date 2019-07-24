---
title: Azure Application Insights Telemetry のデータ モデル - テレメトリ コンテキスト | Microsoft Docs
description: Application Insights Telemetry のコンテキスト データ モデル
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 7c1f47c9b88bd68b326b3c8923ba5b81d425c3e4
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015476"
---
# <a name="telemetry-context-application-insights-data-model"></a>テレメトリ コンテキスト:Application Insights データ モデル

すべてのテレメトリ項目には、厳密に型指定されたコンテキストのフィールドがあります。 すべてのフィールドで特定の監視シナリオが可能です。 カスタム プロパティのコレクションを使用して、カスタムまたはアプリケーション固有のコンテキスト情報を格納します。


## <a name="application-version"></a>アプリケーションのバージョン

アプリケーション コンテキスト フィールドの情報は、必ずテレメトリを送信しているアプリケーションに関するものです。 アプリケーションのバージョンは、アプリケーションの動作の傾向変化、および展開との相関関係を分析するために使用されます。

最大長:1024


## <a name="client-ip-address"></a>クライアント IP アドレス

クライアント デバイスの IP アドレス。 IPv4 と IPv6 がサポートされています。 サービスからテレメトリが送信されるときのロケーション コンテキストは、サービスで操作を開始したユーザーに関するものです。 Application Insights は、クライアント IP から地理的位置情報を抽出し、それを切り詰めます。 したがって、クライアント IP 自体をエンド ユーザーを識別する情報として使用することはできません。 

最大長:46


## <a name="device-type"></a>デバイスの種類

もともとこのフィールドは、アプリケーションのエンド ユーザーが使用しているデバイスの種類を示すために使用されました。 現在は主に、デバイスの種類 "Browser" を持つ JavaScript のテレメトリと、デバイスの種類 "PC" を持つサーバー側テレメトリを区別するために使用されています。

最大長:64


## <a name="operation-id"></a>操作 ID

ルート操作を表す一意の識別子。 この識別子を使用すると、複数のコンポーネントでテレメトリをグループ化できます。 詳細については、「[テレメトリの相関付け](../../azure-monitor/app/correlation.md)」を参照してください。 操作 ID は、要求またはページ ビューのいずれかによって作成されます。 その他のすべてのテレメトリは、含まれている要求またはページ ビューの値でこのフィールドを設定します。 

最大長:128


## <a name="parent-operation-id"></a>親操作 ID

テレメトリ項目の直接の親の一意の識別子。 詳細については、「[テレメトリの相関付け](../../azure-monitor/app/correlation.md)」を参照してください。

最大長:128


## <a name="operation-name"></a>操作の名前

操作の名前 (グループ)。 操作の名前は、要求またはページ ビューのいずれかによって作成されます。 その他のすべてのテレメトリ項目は、含まれている要求またはページ ビューの値でこのフィールドを設定します。 操作の名前は、操作のグループ (たとえば、'GET Home / Index') のすべてのテレメトリ項目を検索するために使用されます。 このコンテキスト プロパティは、「このページでスローされる典型的な例外は何ですか」などの質問に答えるために使用されます。

最大長:1024


## <a name="synthetic-source-of-the-operation"></a>操作の合成ソース

合成ソースの名前。 アプリケーションからのテレメトリの一部は、合成トラフィックを表すことがあります。 これは、Web サイトにインデックスを付ける Web クローラー、サイトの可用性テスト、Application Insights SDK 自体のような診断ライブラリからのトレースである可能性があります。

最大長:1024


## <a name="session-id"></a>セッション ID

セッション ID - ユーザーとアプリケーションのやり取りのインスタンス。 セッション コンテキスト フィールドの情報は、必ずエンド ユーザーに関する情報です。 サービスからテレメトリが送信されるときのセッション コンテキストは、サービスで操作を開始したユーザーに関するものです。

最大長:64


## <a name="anonymous-user-id"></a>匿名ユーザー ID

匿名ユーザー ID。アプリケーションのエンド ユーザーを表します。 サービスからテレメトリが送信されるときのユーザー コンテキストは、サービスで操作を開始したユーザーに関するものです。

[サンプリング](../../azure-monitor/app/sampling.md)は、収集されたテレメトリの量を最小限に抑える方技術の 1 つです。 サンプリング アルゴリズムは、相関関係を持つテレメトリをすべて対象として、またはすべてを対象外としてサンプリングを試行します。 匿名ユーザー ID は、サンプリング スコアの生成に使用されます。 このため、匿名ユーザー ID は、十分にランダムな値である必要があります。 

ユーザー名を格納するために匿名ユーザー ID を使用すると、フィールドの不正使用になります。 認証されたユーザー ID を使用します。

最大長:128


## <a name="authenticated-user-id"></a>認証されたユーザー ID

認証されたユーザー ID。匿名ユーザー ID とは正反対に当たる、このフィールドはフレンドリ名を持つユーザーを表します。 PII 情報であるため、ほとんど SDK では既定で収集されません。

最大長:1024


## <a name="account-id"></a>アカウント ID

マルチテナント アプリケーションでは、これはユーザーが操作しているアカウント ID またはアカウント名です。 例には、Azure ポータルのサブスクリプション ID やブログ プラットフォームのブログ名などがあります。

最大長:1024


## <a name="cloud-role"></a>クラウド ロール

アプリケーションが含まれるロールの名前です。 Azure のロール名に直接マッピングされます。 単一のアプリケーションの一部であるマイクロ サービスを区別するためにも使用できます。

最大長:256


## <a name="cloud-role-instance"></a>クラウド ロール インスタンス

アプリケーションが実行されているインスタンスの名前。 オンプレミスのコンピューター名、Azure のインスタンス名。

最大長:256


## <a name="internal-sdk-version"></a>内部:SDK バージョン

SDK バージョン。 詳細については、「 https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification」を参照してください。

最大長:64


## <a name="internal-node-name"></a>内部:ノード名

このフィールドは、課金目的で使用されるノード名を表します。 これを使用して、ノードの標準検出をオーバーライドします。

最大長:256


## <a name="next-steps"></a>次の手順

- [テレメトリの拡張とフィルター処理](../../azure-monitor/app/api-filtering-sampling.md)を行う方法を確認します。
- Application Insights の型とデータ モデルについては、[データ モデル](data-model.md)に関するページを参照してください。
- 標準的なコンテキスト プロパティ コレクションの[構成](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)を確認します。
