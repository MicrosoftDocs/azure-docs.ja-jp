---
title: ローカル キャッシュ
description: Azure App Service でローカルキャッシュがどのように機能するか、アプリのローカルキャッシュのステータスを有効化、サイズ変更、照会する方法を学びます。
tags: optional
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.topic: article
ms.date: 03/04/2016
ms.custom: seodec18
ms.openlocfilehash: 81782f63199a9fe8f43f56aeefcd1c68951d57a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96852254"
---
# <a name="azure-app-service-local-cache-overview"></a>Azure App Service のローカル キャッシュの概要

> [!NOTE]
> ローカル キャッシュは、Function アプリでもコンテナー化された App Service アプリ ([Windows コンテナー](quickstart-custom-container.md?pivots=container-windows)内または [App Service on Linux](overview.md#app-service-on-linux)上など) ではサポートされません。


Azure App Service のコンテンツは Azure Storage に保存され、コンテンツ共有として永続的な方法で表示されます。 これは多様なアプリが機能するための設計であり、次の特徴があります。  

* コンテンツは、アプリの複数の仮想マシン (VM) インスタンス全体で共有されます。
* コンテンツは永続的であり、アプリを実行して変更できます。
* ログ ファイルと診断データ ファイルは、同じ共有コンテンツ フォルダーで使用できます。
* 新しいコンテンツを直接発行すると、コンテンツ フォルダーが更新されます。 SCM Web サイトと実行中のアプリでもすぐに同じコンテンツを表示できます (通常、なんらかのファイルの変更があった場合、ASP.NET などの一部のテクノロジはアプリの再起動を開始し、最新のコンテンツを取得します)。

多くのアプリはこれらの機能の 1 つまたはすべてを使用しますが、高可用で実行できる高パフォーマンスの読み取り専用コンテンツ ストアのみを必要とするアプリもあります。 このようなアプリは、特定のローカル キャッシュの VM インスタンスを利用できます。

Azure App Service のローカル キャッシュ機能では、コンテンツの Web ロール ビューが提供されます。 このコンテンツは、サイトの起動時に非同期に作成されるストレージ コンテンツの書き込み/破棄キャッシュです。 キャッシュの準備ができると、キャッシュされたコンテンツに対して実行するようにサイトが切り替わります。 ローカル キャッシュで実行するアプリには、次の利点があります。

* Azure Storage のコンテンツにアクセスするときに発生する遅延の影響を受けません。
* コンテンツ共有を提供するサーバーで発生する、Azure Storage の計画的なアップグレードまたは計画されていないダウンタイムおよびその他の中断の影響は受けます。
* ストレージ共有の変更によるアプリの再起動回数が少なくなります。

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>ローカル キャッシュによる App Service の動作の変化
* _D:\home_ は、アプリの起動時に VM インスタンス上に作成されるローカル キャッシュを指します。 _D:\local_ は、一時的な VM 固有の記憶域を引き続き指します。
* 共有コンテンツ ストアの _/site_ および _/siteextensions_ フォルダーの 1 回限りのコピーが、ローカル キャッシュの _D:\home\site_ および _D:\home\siteextensions_ のそれぞれに格納されます。 ファイルは、アプリの起動時にローカル キャッシュにコピーされます。 各アプリの 2 つのフォルダーのサイズは既定で上限が 1 GB ですが、最大 2 GB まで増やすことができます。 キャッシュ サイズが大きくなると、読み込みにかかる時間が長くなることにご注意ください。 ローカル キャッシュの上限を 2 GB に増加していて、コピーされたファイルが最大サイズの 2 GB を超えると、App Service は通知なしにローカル キャッシュを無視して、リモート ファイル共有から読み取りを行います。 上限が定義されていない場合、または上限が 2 GB 未満に設定されていて、コピーされたファイルがその上限を超えると、デプロイまたはスワップがエラーで失敗することがあります。
* ローカル キャッシュは読み取り/書き込み対応です。 ただし、アプリが仮想マシンを移動した場合や再起動された場合、変更は破棄されます。 コンテンツ ストアにミッション クリティカルなデータを保存するアプリには、ローカル キャッシュを使用しないでください。
* _D:\home\LogFiles_ と _D:\home\Data_ には、ログ ファイルとアプリ データが格納されます。 この 2 つのサブフォルダーは、VM インスタンスにローカルに格納され、共有コンテンツ ストアに定期的にコピーされます。 アプリは、これらのフォルダーに書き込むことによって、ログ ファイルとデータを保持できます。 ただし、共有コンテンツ ストアへのコピーはベストエフォートで行われるため、VM インスタンスが突然クラッシュした場合はログ ファイルおよびデータが失われる可能性があります。
* [ログのストリーミング](troubleshoot-diagnostic-logs.md#stream-logs)は、ベストエフォート コピーの影響を受けます。 ストリーム配信されるログにおいて最大で 1 分間の遅延が発生する可能性があります。
* 共有コンテンツ ストアでは、ローカル キャッシュを使用するアプリの _LogFiles_ および _Data_ フォルダーのフォルダー構造が変更されています。 "一意の識別子" + タイムスタンプという命名パターンに従ってサブフォルダーが作成されます。 各サブフォルダーは、実行中か、実行されていたアプリの VM インスタンスに対応します。
* _D:\home_ 内の他のフォルダーはローカル キャッシュに保持され、共有コンテンツ ストアにはコピーされません。
* サポートされている方法によるアプリのデプロイは、持続的な共有コンテンツ ストアに直接発行されます。 ローカル キャッシュ内の _D:\home\site_ および _D:\home\siteextensions_ フォルダーを更新するには、アプリを再起動する必要があります。 この記事の後半の情報を参照して、ライフサイクルをシームレスにしてください。
* SCM サイトの既定のコンテンツ ビューは、引き続き共有コンテンツ ストアのビューです。

## <a name="enable-local-cache-in-app-service"></a>App Service でローカル キャッシュを有効にする 

> [!NOTE]
> ローカル キャッシュは、**F1** または **D1** レベルではサポートされていません。 

ローカル キャッシュは、予約されたアプリケーション設定を組み合わせて使用して構成します。 このアプリケーション設定は、次の方法を使用して構成できます。

* [Azure Portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Azure ポータルを使用してローカル キャッシュを構成する
<a name="Configure-Local-Cache-Portal"></a>

ローカル キャッシュは、 `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Azure portal のアプリ設定:ローカル キャッシュ](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Azure Resource Manager を使用してローカル キャッシュを構成する
<a name="Configure-Local-Cache-ARM"></a>

```json

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "1000"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>ローカル キャッシュのサイズ設定を変更する
ローカル キャッシュの既定のサイズは **1 GB** です。 これには、コンテンツ ストアからコピーされる /site フォルダーと /siteextensions フォルダーだけでなく、ローカルで作成されたログとデータのフォルダーが含まれます。 この上限を上げるには、アプリケーション設定 `WEBSITE_LOCAL_CACHE_SIZEINMB`を使用します。 サイズは、アプリごとに最大 **2 GB** (2000 MB) まで増やすことができます。 ローカル キャッシュは、サイズが大きくなると読み込みに時間がかかることにご注意ください。

## <a name="best-practices-for-using-app-service-local-cache"></a>App Service のローカル キャッシュを使用する場合のベスト プラクティス
ローカル キャッシュは、 [ステージング環境](../app-service/deploy-staging-slots.md) 機能と併用することをお勧めします。

* 値が `Always` の "*固定の*" アプリケーション設定 `WEBSITE_LOCAL_CACHE_OPTION` を **運用** スロットに追加します。 `WEBSITE_LOCAL_CACHE_SIZEINMB`を使用する場合は、運用スロットにそれも固定の設定として追加します。
* **ステージング** スロットを作成し、ステージング スロットに発行します。 通常は、ステージングのシームレスなビルド、デプロイ、テストのライフサイクルを有効にするためにローカル キャッシュを使用するようにステージング スロットを設定しませんが、運用スロットの場合はローカル キャッシュの利点があります。
* ステージング スロットに対してサイトをテストします。  
* 準備ができたら、ステージング スロットと運用スロット間の [スワップ操作](../app-service/deploy-staging-slots.md#Swap) を実行します。  
* 固定の設定には名前が含まれ、スロットに固定されます。 そのため、ステージング スロットが運用スロットにスワップされると、ローカル キャッシュのアプリケーション設定が継承されます。 新しくスワップされた運用スロットは、数分後にローカル キャッシュに対して実行され、スワップ後のスロット ウォームアップ時にウォームアップされます。 したがって、スロットのスワップが完了すると、運用スロットはローカル キャッシュに対して実行されるようになります。

## <a name="frequently-asked-questions-faq"></a>よく寄せられる質問 (FAQ)

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>アプリにローカル キャッシュを適用できるかどうかは、どうやって判断できますか?
アプリが高パフォーマンスで信頼性の高いコンテンツ ストアが必要としていて、実行時に重要なデータ書き込むためにコンテンツ ストアを使用せず、合計サイズが 2 GB 未満であれば、ローカル キャッシュを適用できます。 /site フォルダーと /siteextensions フォルダーの合計サイズを確認するには、サイト拡張機能の "Azure Web Apps Disk Usage" を使用します。

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>サイトがローカル キャッシュの使用に切り替わったかどうかを確認するにはどうすればよいですか?
ステージング環境でローカル キャッシュ機能を使用している場合、ローカル キャッシュのウォームアップが完了するまでスワップ操作は完了しません。 サイトがローカル キャッシュに対して実行されているかどうかは、worker プロセス環境変数の `WEBSITE_LOCALCACHE_READY`で確認できます。 「 [worker プロセス環境変数](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) 」ページの手順を使用して、複数インスタンスで worker プロセス環境変数にアクセスしてください。  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>新しい変更を発行してもアプリに反映されないのは なぜですか?
アプリがローカル キャッシュを使用している場合、最新の変更を反映するには、サイトを再起動する必要があります。 運用サイトに変更を発行したくない場合は、 前述のベスト プラクティス セクションのスロットのオプションを参照してください。

> [!NOTE]
> [パッケージから実行](deploy-run-package.md)するデプロイ オプションは、ローカル キャッシュに対応していません。

### <a name="where-are-my-logs"></a>ログはどこにありますか?
ローカル キャッシュのログ フォルダーとデータ フォルダーの見た目は少し違いますが、 サブフォルダーの構造は、"一意の VM 識別子" + タイムスタンプという形式のサブフォルダー以下に入れ子になっている点を除くと同じです。

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>ローカル キャッシュを有効にした後も、アプリが再起動されるのは なぜですか? ローカル キャッシュを使用すると、アプリが頻繁に再起動しないと思っていました。
ローカル キャッシュを使用した場合、ストレージ関連のアプリの再起動回数が減りますが、 VM の計画的なインフラストラクチャのアップグレード時には、アプリが引き続き再起動する可能性があります。 ローカル キャッシュを有効にした場合、アプリ全体の再起動回数は減ります。

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>高速なローカル ドライブへのコピー時にローカル キャッシュで除外されるディレクトリはありますか?
ストレージ コンテンツをコピーする手順の一環として、リポジトリという名前が付けられたフォルダーはすべて除外されます。 これは、サイトのコンテンツにアプリの日常業務で必要にならないソース管理リポジトリを含める可能性のあるシナリオで役立ちます。 

### <a name="how-to-flush-the-local-cache-logs-after-a-site-management-operation"></a>サイトの管理操作後にローカル キャッシュ ログをフラッシュする方法
ローカル キャッシュ ログをフラッシュするには、アプリを停止して再起動します。 この操作により、古いキャッシュがクリアされます。 
