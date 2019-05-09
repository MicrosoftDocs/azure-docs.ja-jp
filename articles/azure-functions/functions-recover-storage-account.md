---
title: Azure Functions Runtime に到達できない問題のトラブルシューティング方法。
description: 無効なストレージ アカウントのトラブルシューティング方法について説明します。
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.service: azure-functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 6057fa52cd2f1e9b9fd525723f96ab66983fb5d4
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521722"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>"Functions Runtime に到達できない" 問題のトラブルシューティング方法


## <a name="error-text"></a>エラー テキスト
このドキュメントは、Functions ポータルで次のエラーが表示された場合のトラブルシューティングを行うことを目的としています。

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>まとめ
この問題は、Azure Functions Runtime が起動できない場合に発生します。 このエラーが発生する最も一般的な理由は、関数アプリでそのストレージ アカウントへのアクセスが失われていることです。 ストレージ アカウントの要件について詳しくは、[こちら](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)をご覧ください。

### <a name="troubleshooting"></a>トラブルシューティング
最も一般的な次の 4 つのエラー ケースについて、各ケースの識別方法および解決方法を説明します。

1. ストレージ アカウントが削除された
1. ストレージ アカウントのアプリケーション設定が削除された
1. ストレージ アカウントの資格情報が無効
1. ストレージ アカウントにアクセスできない
1. 日ごとの実行クォータがいっぱいになった

## <a name="storage-account-deleted"></a>ストレージ アカウントが削除された

すべての関数アプリには、操作するためのストレージ アカウントが必要です。 そのアカウントが削除されると、Functions は機能しません。

### <a name="how-to-find-your-storage-account"></a>ストレージ アカウントの検索方法

まず、アプリケーションの設定でご自分のストレージ アカウント名を検索します。 `AzureWebJobsStorage` または `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` のいずれかに、接続文字列でラップされたストレージ アカウントの名前が含まれます。 詳細については、[こちら](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)のアプリケーション設定のリファレンスをご覧ください。

Azure portal でご自分のストレージ アカウントを検索し、まだ存在しているかどうかを確認します。 削除されている場合は、ストレージ アカウントを作成し直して、ストレージ接続文字列を置換する必要があります。 関数コードが失われ、もう一度デプロイし直す必要があります。

## <a name="storage-account-application-settings-deleted"></a>ストレージ アカウントのアプリケーション設定が削除された

前の手順で、ストレージ アカウントの接続文字列がなかった場合は、削除されたか上書きされた可能性があります。 アプリの設定の削除は、デプロイ スロットまたは Azure Resource Manager スクリプトを使用して、アプリケーション設定を設定するときに最もよく行われます。

### <a name="required-application-settings"></a>必須のアプリケーションの設定

* 必須
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* 従量課金プラン (Functions) には必須
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

これらのアプリケーション設定については、[こちら](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)をご覧ください

### <a name="guidance"></a>ガイダンス

* これらの設定のいずれに対しても、"スロット設定" をチェックしないでください。 デプロイ スロットをスワップすると、関数が中断されます。
* 自動デプロイの一環としてこれらの設定を変更しないようにしてください。
* これらの設定は、作成時に指定して有効にする必要があります。 これらの設定が含まれない自動デプロイでは、アプリが機能しなくなります。後から設定を追加しても機能しません。

## <a name="storage-account-credentials-invalid"></a>ストレージ アカウントの資格情報が無効

ストレージ キーを再生成する場合、上記のストレージ アカウント接続文字列を更新する必要があります。 ストレージ キーの管理については、[こちら](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)をご覧ください。

## <a name="storage-account-inaccessible"></a>ストレージ アカウントにアクセスできない

Function App は、ストレージ アカウントにアクセスできる必要があります。 ストレージ アカウントへの Functions のアクセスをブロックする一般的な問題は次のとおりです。

* ストレージ アカウント間のトラフィックを許可するため、正しいネットワーク規則なしで Function App が App Service Environment にデプロイされた
* ストレージ アカウントのファイアウォールが有効になっていて、Functions 間のトラフィックを許可するように構成されていない ストレージ アカウントのファイアウォール構成については、[こちら](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)をご覧ください。

## <a name="daily-execution-quota-full"></a>日ごとの実行クォータがいっぱいになった

日ごとの実行クォータが構成されている場合、Function App は一時的に無効になり、多くのポータル コントロールが使用できなくなります。 

* 確認するには、ポータルで [プラットフォーム機能]> [Function App の設定] を開きます。 クォータを超過している場合は、次のメッセージが表示されます。
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* 問題を解決するには、クォータを削除し、アプリを再起動します。

## <a name="next-steps"></a>次の手順

Function App が戻り、機能するようになったので、クイック スタートと開発者用リファレンスを参照して、もう一度起動して実行しましょう。

* [初めての Azure 関数の作成](functions-create-first-azure-function.md)  
  Azure Functions のクイック スタートですぐに最初の関数を作成します。 
* [Azure Functions 開発者向けリファレンス](functions-reference.md)  
  Azure Functions ランタイムに関する詳細な技術情報と、関数のコーディングやトリガーおよびバインドの定義に関するリファレンスを提供します。
* [Azure Functions のテスト](functions-test-a-function.md)  
  関数をテストするための各種ツールと手法について説明します。
* [Azure Functions のスケーリング方法](functions-scale.md)  
  Azure Functions で利用できるサービス プラン (従量課金ホスティング プランを含む) と、適切なプランを選択する方法について説明します。 
* [Azure App Service とは](../app-service/overview.md)  
  Azure Functions では、デプロイ、環境変数、診断などの主要な機能に Azure App Service を活用しています。 
