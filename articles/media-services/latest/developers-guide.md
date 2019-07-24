---
title: Azure Media Services v3 SDK - Azure
description: この記事では、SDK を使用して Media Services v3 API での開発を始める方法の概要を示します。
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 04/11/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 9fb4d1561a661387f759aada9e776d43a95aa5c7
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564511"
---
# <a name="develop-against-media-services-v3-api-using-sdks"></a>Media Services v3 API に対する SDK を使用した開発

開発者は、Media Services の [REST API](https://aka.ms/ams-v3-rest-ref) または REST API と対話できるクライアント ライブラリを使って、カスタム メディア ワークフローを簡単に作成、管理、メンテナンスできます。 [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API は、OpenAPI 仕様 (旧称 Swagger) に基づいています。

> [!NOTE]
> Azure Media Services v3 SDK は、スレッドセーフである保証はありません。 マルチスレッドのアプリケーションを開発するときは、独自のスレッド同期ロジックを追加してクライアントを保護するか、スレッドごとに新しい AzureMediaServicesClient オブジェクトを使用する必要があります。 .NET の HttpClient インスタンスなど、コード内の任意のオブジェクトによってクライアントにもたらされるマルチスレッドの問題にも注意を払う必要があります。

このトピックでは、SDK、ツール、攻略ガイドへのリンクを提供します。

## <a name="prerequisites"></a>前提条件

Media Services に対する開発を始めるには、次のものが必要です。

- 有効な Azure サブスクリプション Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。
- [基本的な概念を理解する](concepts-overview.md)
- 「[Media Services v3 API を使用した開発](media-services-apis-overview.md)」を参照する
- [Media Services アカウントを作成する - CLI](create-account-cli-how-to.md)

## <a name="start-developing-with-sdks"></a>SDK を使用した開発を始める

### <a name="net"></a>.NET

[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk) を使用して [Media Services に接続](configure-connect-dotnet-howto.md)します。

Media Services の [.NET リファレンス](https://aka.ms/ams-v3-dotnet-ref) ドキュメントを確認してください。

### <a name="java"></a>Java

[Java SDK](https://aka.ms/ams-v3-java-sdk) を使用して [Media Services に接続](configure-connect-java-howto.md)します。

Media Services の [Java リファレンス](https://aka.ms/ams-v3-java-ref) ドキュメントを確認してください。

### <a name="nodejs"></a>Node.js

[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) を使用して [Media Services に接続](configure-connect-nodejs-howto.md)します。

Media Services の [Node.js リファレンス](https://aka.ms/ams-v3-nodejs-ref) ドキュメントを調べて、node.js で Media Services API を使用する方法が示されている[サンプル](https://github.com/Azure-Samples/media-services-v3-node-tutorials)をご覧ください。

### <a name="python"></a>Python

[Python SDK](https://aka.ms/ams-v3-python-sdk) を使用します。

Media Services の [Python リファレンス](https://aka.ms/ams-v3-python-ref) ドキュメントを確認してください。

### <a name="go"></a>Go

[Go SDK](https://aka.ms/ams-v3-go-sdk) を使用します。

Media Services の [Go リファレンス](https://aka.ms/ams-v3-go-ref) ドキュメントを確認してください。

### <a name="ruby"></a>Ruby

[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk) を使用します。

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) は、Media Services について学習したい Windows ユーザーが使用できるツールです。 AMSE は、Media Services で VOD およびライブ コンテンツをアップロード、ダウンロード、エンコード、ストリーミングする Winforms/C# アプリケーションです。 AMSE ツールは、コードを記述しないで Media Services をテストしたいクライアント用です。 AMSE コードは、Media Services による開発を希望するお客様用のリソースとして提供されています。

AMSE はオープン ソース プロジェクトであり、サポートはコミュニティによって提供されます (問題は https://github.com/Azure/Azure-Media-Services-Explorer/issues) に報告できます)。 このプロジェクトでは、[Microsoft オープン ソースの倫理規定](https://opensource.microsoft.com/codeofconduct/)を採用しています。 詳しくは、「[Code of Conduct FAQ (倫理規定についてよくある質問)](https://opensource.microsoft.com/codeofconduct/faq/)」を参照するか、opencode@microsoft.com 宛てに質問またはコメントをお送りください。

## <a name="next-steps"></a>次の手順

[概要](media-services-overview.md)
