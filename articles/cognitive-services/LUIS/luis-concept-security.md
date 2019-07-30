---
title: 共同作業時のセキュリティ
titleSuffix: Language Understanding - Azure Cognitive Services
description: オーサリング アクセスは、所有者とコラボレーターが利用できます。 プライベート アプリについては、エンドポイント アクセスは所有者とコラボレーターが利用できます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: b52b0fffe9c32ab14600a3e296852faa626d794d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058522"
---
# <a name="authoring-and-endpoint-user-access"></a>オーサリングとエンドポイントのユーザー アクセス
オーサリング アクセスは、所有者とコラボレーターが利用できます。 プライベート アプリについては、エンドポイント アクセスは所有者とコラボレーターが利用できます。 パブリック アプリについては、エンドポイント アクセスは、自分の Azure [Cognitive Service](../cognitive-services-apis-create-account.md) または [LUIS](luis-how-to-azure-subscription.md#create-prediction-endpoint-runtime-resource-in-the-azure-portal) リソースを持ち、パブリック アプリの ID を持つすべてのユーザーが利用できます。 

## <a name="access-to-authoring"></a>オーサリングへのアクセス
[LUIS](luis-reference-regions.md#luis-website) Web サイトまたは[オーサリング API](https://go.microsoft.com/fwlink/?linkid=2092087) からアプリへのアクセスは、そのアプリの所有者によって制御されます。 

所有者とすべてのコラボレーターが、アプリのオーサリング アクセスを持ちます。 

|オーサリング アクセスに含まれるもの|メモ|
|--|--|
|エンドポイント キーの追加または削除||
|バージョンのエクスポート||
|エンドポイント ログのエクスポート||
|バージョンのインポート||
|アプリの公開|アプリが公開されている場合、オーサリング キーまたはエンドポイント キーを持つすべてのユーザーがアプリにクエリを実行できます。|
|モデルの変更|
|発行|
|[アクティブ ラーニング](luis-how-to-review-endpoint-utterances.md)のためのエンドポイントの発話の確認|
|トレーニング|

## <a name="access-to-endpoint"></a>エンドポイントへのアクセス

エンドポイントをクエリするためのアクセスは、 **[管理]** セクションの **[アプリケーション情報]** ページの設定によってによって制御されます。 

![アプリをパブリックに設定する](./media/luis-concept-security/set-application-as-public.png)

|[プライベート エンドポイント](#private-app-endpoint-security)|[パブリック エンドポイント](#public-app-endpoint-access)|
|:--|:--|
|所有者とコラボレーターが利用可能|所有者、コラボレーター、および、アプリ ID を知っている他の任意のユーザーが利用可能|

### <a name="private-app-endpoint-security"></a>プライベート アプリのエンドポイントのセキュリティ

プライベート アプリのエンドポイントは、次の場合のみ使用できます。

|キーとユーザー|説明|
|--|--|
|所有者のオーサリング キー| 最大で 1,000 エンドポイント ヒット|
|コラボレーターのオーサリング キー| 最大で 1,000 エンドポイント ヒット|
|作成者またはコラボレーターによって LUIS に割り当てられた任意のキー|キー使用法のレベルに基づく|

#### <a name="microsoft-user-accounts"></a>Microsoft のユーザー アカウント

作成者とコラボレーターは、プライベート LUIS アプリにキーを割り当てることができます。 Azure portal で LUIS キーを作成している Microsoft のユーザー アカウントには、アプリの所有者またはアプリ コラボレーターが必要です。 別の Azure アカウントからプライベート アプリにキーを割り当てることはできません。

Active Directory ユーザー アカウントの詳細については、「[Azure Active Directory テナント ユーザー](luis-how-to-collaborate.md#azure-active-directory-tenant-user)」を参照してください。 

### <a name="public-app-endpoint-access"></a>パブリック アプリのエンドポイントへのアクセス

アプリをいったんパブリックとして構成すると、キーでエンドポイントのクォータを使い切ってない場合に限り、"_すべての_" 有効な LUIS オーサリング キーまたは LUIS エンドポイント キーでアプリにクエリを実行できます。

アプリ ID を指定した場合のみ、所有者やコラボレーター以外のユーザーがパブリック アプリにアクセスできます。 LUIS には、パブリックな "_マーケット_" やパブリック アプリを検索する他の方法がありません。  

パブリック アプリはすべてのリージョンで公開されるので、リージョン ベースの LUIS リソース キーを持つユーザーは、どのリージョンがリソース キーと関連付けられているかにかかわらず、アプリにアクセスできます。

## <a name="microsoft-user-accounts"></a>Microsoft のユーザー アカウント

作成者とコラボレーターは、[公開] ページで LUIS にキーを追加できます。 Azure portal で LUIS キーを作成している Microsoft のユーザー アカウントには、アプリの所有者またはアプリ コラボレーターが必要です。 

Active Directory ユーザー アカウントの詳細については、「[Azure Active Directory テナント ユーザー](luis-how-to-collaborate.md#azure-active-directory-tenant-user)」を参照してください。 

## <a name="securing-the-endpoint"></a>エンドポイントのセキュリティ保護 

サーバー間環境で呼び出すことによって、LUIS エンドポイント キーを表示できるユーザーを制御できます。 ボットから LUIS を使用している場合、このボットと LUIS の間の接続は既にセキュリティで保護されています。 LUIS エンドポイントを直接呼び出している場合は、アクセスが制御された ([AAD](https://azure.microsoft.com/services/active-directory/) など) サーバー側 API (Azure [関数](https://azure.microsoft.com/services/functions/)など) を作成する必要があります。 サーバー側 API が呼び出され、認証と承認が確認されると、LUIS への呼び出しが渡されます。 この戦略では man-in-the-middle (中間者) 攻撃を防ぐことはできませんが、ユーザーからエンドポイントを難読化し、アクセスの追跡を可能にして、エンドポイントの応答のログ ([Application Insights](https://azure.microsoft.com/services/application-insights/) など) を追加できます。  

## <a name="security-compliance"></a>キュリティ コンプライアンス
 
[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-security-compliance.md)]

## <a name="next-steps"></a>次の手順

[ベスト プラクティス](luis-concept-best-practices.md)を参照し、意図とエンティティを使用して最適な予測を得る方法について学習します。
