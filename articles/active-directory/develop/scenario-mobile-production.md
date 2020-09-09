---
title: モバイル アプリの準備 - Web API を実稼働用に準備する | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出すモバイル アプリを構築する方法について説明します。 (アプリを実稼働用に準備します。)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d1a7407c947c1c5bae1eed00acb0f216722f3d09
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121036"
---
# <a name="prepare-mobile-apps-for-production"></a>モバイル アプリを実稼働用に準備する

この記事では、運用環境に移行する前にモバイル アプリの品質と信頼性を向上させる方法に関する詳細情報を提供します。

## <a name="handle-errors"></a>エラーを処理する

モバイル アプリを実稼働用に準備するときに、いくつかのエラー状態が発生する可能性があります。 処理する主なケースは、サイレント障害と対話へのフォールバックです。 また、ネットワークが利用できない状況、サービスの停止、管理者の同意の要件、およびその他のシナリオ固有のケースなどの他の条件についても、検討する必要があります。

Microsoft Authentication Library (MSAL) の種類ごとに、エラー状態の処理方法を説明するサンプル コードと wiki コンテンツを見つけることができます。

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>問題の軽減と調査

アプリの問題をより的確に診断するには、データを収集します。 収集できるデータの種類については、「[MSAL アプリケーションでのログ記録](./msal-logging.md)」を参照してください。

データ収集に関する推奨事項を次に示します。

- ユーザーは、問題があるときに支援を求めることがあります。 ログをキャプチャして一時的に保存することをお勧めします。 ユーザーがログをアップロードできる場所を指定します。 MSAL には、認証に関する詳細情報をキャプチャするログの拡張機能があります。

- テレメトリが利用できる場合は、MSAL を介して有効にして、ユーザーがアプリにサインインしている方法についてデータを収集します。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

追加のサンプルを試すには、「[デスクトップおよびモバイルのパブリック クライアント アプリ](sample-v2-code.md#desktop-and-mobile-public-client-apps)」を参照してください。