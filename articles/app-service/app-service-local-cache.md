---
title: "Azure App Service のローカル キャッシュの概要 | Microsoft Docs"
description: "この記事では、Azure App Service のローカル キャッシュ機能を有効にする方法、サイズを変更する方法、状態をクエリする方法について説明します。"
services: app-service
documentationcenter: app-service
author: SyntaxC4
manager: yochayk
editor: 
tags: optional
keywords: 
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2016
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: e00d453e9ae34cafb5ce753f63c253e954d6b09a
ms.lasthandoff: 03/22/2017


---
# <a name="azure-app-service-local-cache-overview"></a>Azure App Service のローカル キャッシュの概要
Azure Web アプリのコンテンツは Azure Storage に保存され、コンテンツ共有として永続的な方法で表示されます。 これは多様なアプリが機能するための設計であり、次の特徴があります。  

* コンテンツは、Web アプリの複数の仮想マシン (VM) インスタンス全体で共有されます。
* コンテンツは永続的であり、Web アプリを実行して変更できます。
* ログ ファイルと診断データ ファイルは、同じ共有コンテンツ フォルダーで使用できます。
* 新しいコンテンツを直接発行すると、コンテンツ フォルダーが更新されます。 SCM Web サイトと実行中の Web アプリでもすぐに同じコンテンツを表示できます (通常、何らかのファイルの変更があった場合、ASP.NET などの一部のテクノロジは Web アプリの再起動を開始し、最新のコンテンツを取得します)。

多くの Web アプリはこれらの機能の 1 つまたはすべてを使用しますが、高可用で実行できる高パフォーマンスの読み取り専用コンテンツ ストアのみを必要とする Web アプリもあります。 このようなアプリは、特定のローカル キャッシュの VM インスタンスを利用できます。

Azure App Service のローカル キャッシュ機能では、コンテンツの Web ロール ビューが提供されます。 このコンテンツは、サイトの起動時に非同期に作成されるストレージ コンテンツの書き込み/破棄キャッシュです。 キャッシュの準備ができると、キャッシュされたコンテンツに対して実行するようにサイトが切り替わります。 ローカル キャッシュで実行する Web アプリには、次の利点があります。

* Azure Storage のコンテンツにアクセスするときに発生する遅延の影響を受けません。
* コンテンツ共有を提供するサーバーで発生する、Azure Storage の計画的なアップグレードまたは計画されていないダウンタイムおよびその他の中断の影響は受けます。
* ストレージ共有の変更によるアプリの再起動回数が少なくなります。

## <a name="how-local-cache-changes-the-behavior-of-app-service"></a>ローカル キャッシュによる App Service の動作の変化
* ローカル キャッシュは、Web アプリの /site フォルダーと /siteextensions フォルダーのコピーです。 Web アプリの起動時にローカル VM インスタンス上に作成されます。 Web アプリごとのローカル キャッシュのサイズは既定で上限が 300 MB ですが、最大 2 GB まで増やすことができます。
* ローカル キャッシュは読み取り/書き込み対応です。 ただし、Web アプリが仮想マシンを移動した場合や再起動された場合、変更は破棄されます。 コンテンツ ストアにミッション クリティカルなデータを保存するアプリには、ローカル キャッシュを使用しないでください。
* Web アプリは、ローカル キャッシュを使用しない場合と同様に、ログ ファイルと診断データの書き込みを継続できます。 ただし、ログ ファイルとデータはローカルの VM に保存されます。 その後、共有コンテンツ ストアに定期的にコピーされます。 共有コンテンツ ストアへのコピーはベスト ケース エフォートで行われ、VM インスタンスが突然クラッシュした場合は書き戻しできない可能性があります。
* ローカル キャッシュを使用する Web アプリの LogFiles フォルダーと Data フォルダーの構造は変わります。 ストレージの LogFiles フォルダーと Data フォルダー以下に、"一意の識別子" + タイムスタンプという命名パターンに従ってサブフォルダーが作成されます。 各サブフォルダーは、実行中か、実行されていた Web アプリの VM インスタンスに対応します。  
* 何らかの発行メカニズムで変更を Web アプリに発行すると、共有コンテンツ ストアに発行されます。 これは、発行されたコンテンツに持続性を持たせるための仕様によるものです。 Web アプリのローカル キャッシュを更新するには、Web アプリを再起動する必要があります。 余計な手順のように見えるかもしれませんが、 この記事の後半の情報を参照して、ライフサイクルをシームレスにしてください。
* D:\Home はローカル キャッシュを指します。 D:\local は、一時的な VM 固有の記憶域を引き続き指します。
* SCM サイトの既定のコンテンツ ビューは、引き続き共有コンテンツ ストアのビューです。

## <a name="enable-local-cache-in-app-service"></a>App Service でローカル キャッシュを有効にする
ローカル キャッシュは、予約されたアプリケーション設定を組み合わせて使用して構成します。 このアプリケーション設定は、次の方法を使用して構成できます。

* [Azure ポータル](#Configure-Local-Cache-Portal)
* [Azure リソース マネージャー](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Azure ポータルを使用してローカル キャッシュを構成する
<a name="Configure-Local-Cache-Portal"></a>

ローカル キャッシュは、 `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Azure ポータルのアプリケーション設定: ローカル キャッシュ](media/app-service-local-cache/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Azure Resource Manager を使用してローカル キャッシュを構成する
<a name="Configure-Local-Cache-ARM"></a>

```

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
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>ローカル キャッシュのサイズ設定を変更する
ローカル キャッシュの既定のサイズは **300 MB**です。 これには、コンテンツ ストアからコピーされる /site フォルダーと /siteextensions フォルダーだけでなく、ローカルで作成されたログとデータのフォルダーが含まれます。 この上限を上げるには、アプリケーション設定 `WEBSITE_LOCAL_CACHE_SIZEINMB`を使用します。 サイズは、Web アプリごとに最大 **2 GB** (2000 MB) まで増やすことができます。

## <a name="best-practices-for-using-app-service-local-cache"></a>App Service のローカル キャッシュを使用する場合のベスト プラクティス
ローカル キャッシュは、 [ステージング環境](../app-service-web/web-sites-staged-publishing.md) 機能と併用することをお勧めします。

* 値が `Always` の "*固定の*" アプリケーション設定 `WEBSITE_LOCAL_CACHE_OPTION` を**運用**スロットに追加します。 `WEBSITE_LOCAL_CACHE_SIZEINMB`を使用する場合は、運用スロットにそれも固定の設定として追加します。
* **ステージング** スロットを作成し、ステージング スロットに発行します。 通常は、ステージングのシームレスなビルド、デプロイ、テストのライフサイクルを有効にするためにローカル キャッシュを使用するようにステージング スロットを設定しませんが、運用スロットの場合はローカル キャッシュの利点があります。
* ステージング スロットに対してサイトをテストします。  
* 準備ができたら、ステージング スロットと運用スロット間の [スワップ操作](../app-service-web/web-sites-staged-publishing.md#Swap) を実行します。  
* 固定の設定には名前が含まれ、スロットに固定されます。 そのため、ステージング スロットが運用スロットにスワップされると、ローカル キャッシュのアプリケーション設定が継承されます。 新しくスワップされた運用スロットは、数分後にローカル キャッシュに対して実行され、スワップ後のスロット ウォームアップ時にウォームアップされます。 したがって、スロットのスワップが完了すると、運用スロットはローカル キャッシュに対して実行されるようになります。

## <a name="frequently-asked-questions-faq"></a>よく寄せられる質問 (FAQ)
### <a name="how-can-i-tell-if-local-cache-applies-to-my-web-app"></a>Web アプリにローカル キャッシュを適用できるかどうかは、どうやって判断できますか?
Web アプリが高パフォーマンスで信頼性の高いコンテンツ ストアが必要で、実行時に重要なデータ書き込むためにコンテンツ ストアを使用せず、合計サイズが 2 GB 未満であれば、ローカル キャッシュを適用できます。 /site フォルダーと /siteextensions フォルダーの合計サイズを確認するには、サイト拡張機能の "Azure Web Apps Disk Usage" を使用します。  

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>サイトがローカル キャッシュの使用に切り替わったかどうかを確認するにはどうすればよいですか?
ステージング環境でローカル キャッシュ機能を使用している場合、ローカル キャッシュのウォームアップが完了するまでスワップ操作は完了しません。 サイトがローカル キャッシュに対して実行されているかどうかは、worker プロセス環境変数の `WEBSITE_LOCALCACHE_READY`で確認できます。 「 [worker プロセス環境変数](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) 」ページの手順を使用して、複数インスタンスで worker プロセス環境変数にアクセスしてください。  

### <a name="i-just-published-new-changes-but-my-web-app-does-not-seem-to-have-them-why"></a>新しい変更を発行しても Web アプリに反映されないのは なぜですか?
Web アプリがローカル キャッシュを使用している場合、最新の変更を反映するには、サイトを再起動する必要があります。 運用サイトに変更を発行したくない場合は、 前述のベスト プラクティス セクションのスロットのオプションを参照してください。

### <a name="where-are-my-logs"></a>ログはどこにありますか?
ローカル キャッシュのログ フォルダーとデータ フォルダーの見た目は少し違いますが、 サブフォルダーの構造は、"一意の VM 識別子" + タイムスタンプという形式のサブフォルダー以下に入れ子になっている点を除くと同じです。

### <a name="i-have-local-cache-enabled-but-my-web-app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>ローカル キャッシュを有効にした後も、Web アプリが再起動されるのは なぜですか? ローカル キャッシュを使用すると、アプリが頻繁に再起動しないと思っていました。
ローカル キャッシュを使用した場合、ストレージ関連の Web アプリの再起動回数が減りますが、 VM の計画的なインフラストラクチャのアップグレード時には、Web アプリが引き続き再起動する可能性があります。 ローカル キャッシュを有効にした場合、アプリ全体の再起動回数は減ります。

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>高速なローカル ドライブへのコピー時にローカル キャッシュで除外されるディレクトリはありますか?
ストレージ コンテンツをコピーする手順の一環として、リポジトリという名前が付けられたフォルダーはすべて除外されます。 これは、サイトのコンテンツに Web アプリの日常業務で必要にならないソース管理リポジトリを含める可能性のあるシナリオで役立ちます。 

