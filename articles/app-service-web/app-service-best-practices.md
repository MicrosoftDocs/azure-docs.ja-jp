---
title: "Azure App Service のベスト プラクティス"
description: "Azure App Service のベスト プラクティスとトラブルシューティングについて説明します。"
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1068ac3c3ffd832df5e61bc24887780ced6444d0
ms.lasthandoff: 11/17/2016


---
# <a name="best-practices-for-azure-app-service"></a>Azure App Service のベスト プラクティス
この記事では、 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)を使用するためのベスト プラクティスを概説します。 

## <a name="a-namecolocationacolocation"></a><a name="colocation"></a>コロケーション
Web アプリやデータベースなど、ソリューションを構成する Azure リソースを別々のリージョンに配置すると、次のような影響が考えられます。

* リソース間の通信における待機時間の増大
* [Azure の料金](https://azure.microsoft.com/pricing/details/data-transfers)に関するページに記載されている、リージョン間の送信データ転送にかかる料金の請求

Web アプリ、データベース、コンテンツやデータを保持するためのストレージ アカウントなど、ソリューションを構成する Azure リソースは、同じリージョンに配置すること (コロケーション) をお勧めします。 リソースを作成する場合は、それらを必ず同じ Azure リージョン内に配置してください。ただし、特定のビジネスまたは設計上の理由で難しい場合はその限りでありません。 Premium App Service プラン アプリで現在使用できる [App Service の複製機能](app-service-web-app-cloning-portal.md)を利用すれば、App Service アプリをデータベースと同じリージョンに移動することができます。   

## <a name="a-namememoryresourcesawhen-apps-consume-more-memory-than-expected"></a><a name="memoryresources"></a>アプリが予想よりも多くのメモリを消費している場合
監視またはサービスの推奨事項に示されている情報を基に、アプリが予想よりも多くのメモリを消費していることに気付いた場合は、 [App Service の自動修復機能](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites)を検討してください。 自動修復機能のオプションの 1 つでは、メモリのしきい値に基づいて独自の処置を行います。 処置の内容は、電子メールによる通知から、メモリ ダンプによる調査、さらに worker プロセスのリサイクルによるその場での軽減処理まで広範囲に及びます。 自動修復は、 [App Service サポート サイトの拡張機能](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps)に関するこのブログ投稿の説明に沿って、Web.config またはわかりやすいユーザー インターフェイスを使用して構成することができます。   

## <a name="a-namecpuresourcesawhen-apps-consume-more-cpu-than-expected"></a><a name="CPUresources"></a>アプリが予想よりも多くの CPU リソースを消費している場合
監視またはサービスの推奨事項の説明を基に、アプリが予想よりも多くの CPU リソースを消費していること、または CPU スパイクが繰り返し発生していることに気が付いた場合は、App Service プランのスケール アップまたはスケール アウトを検討してください。 アプリケーションがステートフルである場合は、スケール アップが唯一のオプションとなります。一方、アプリケーションがステートレスである場合、スケール アウトにより、柔軟性と拡張性を高めることができます。 

"ステートフル" アプリケーション対 "ステートレス" アプリケーションの詳細については、ビデオ「 [Planning a Scalable End-to-End Multi-Tier Application on Microsoft Azure Web App (Microsoft Azure Web App でスケーラブルなエンド ツー エンドの多層アプリケーションを計画する)](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid)」を参照してください。 App Service のスケール オプションおよび自動スケール オプションの詳細については、「 [Azure App Service の Web アプリをスケーリングする](web-sites-scale.md)」を参照してください。  

## <a name="a-namesocketresourcesawhen-socket-resources-are-exhausted"></a><a name="socketresources"></a>ソケット リソースを使い果たした場合
送信 TCP 接続を使い果たしてしまう理由としては、一般的には、使用しているライブラリが TCP 接続を再利用するように実装されていないことや、HTTP - Keep-Alive などの上位レベルのプロトコルが活用されていないことなどが挙げられます。 App Service プランでアプリによって参照される各ライブラリのドキュメントを再確認し、送信接続が効率的に再利用されるようにコード内でライブラリが構成またはアクセスされるようにしてください。 また、ライブラリのドキュメントのガイダンスに従って適切に作成しリリースするか、接続のリークを防ぐためにクリーンアップを行ってください。 このようなクライアント ライブラリの調査中は、複数のインスタンスにスケール アウトすることで影響を軽減することができます。  

## <a name="a-nameappbackupawhen-your-app-backup-starts-failing"></a><a name="appbackup"></a>アプリのバックアップが失敗するようになった場合
アプリのバックアップが失敗する場合は、最も一般的な理由として、無効なストレージ設定と無効なデータベース構成の 2 つが考えられます。 通常、このようなエラーはストレージまたはデータベースのリソースに変更が加えられた場合か、これらのリソースへのアクセス方法が変更された場合 (バックアップ設定で選択したデータベースに関して資格情報が更新された場合など) に発生します。 バックアップは、通常はスケジュールに応じて実行され、ストレージへのアクセス (バックアップしたファイルの出力用) とデータベースへのアクセス (バックアップに含まれる内容のコピーと読み取り用) が必要になります。 これらのリソースのいずれかにアクセスできないと、バックアップは常に失敗します。 

バックアップ エラーが発生した場合は、最新のバックアップ結果を確認してどちらの種類のエラーが発生しているのかを把握してください。 ストレージへのアクセス エラーが発生している場合は、バックアップ構成で使用しているストレージ設定を確認し、更新してください。 データベースへのアクセス エラーが発生している場合は、アプリ設定に含まれる接続文字列を確認し、更新してから、必要なデータベースが正しく含まれるようにバックアップ構成を更新してください。 アプリのバックアップの詳細については、 [Azure App Service での Web アプリのバックアップ](web-sites-backup.md) に関するドキュメントを参照してください。

## <a name="a-namenodejsawhen-new-nodejs-apps-are-deployed-to-azure-app-service"></a><a name="nodejs"></a>新しい Node.js アプリを Azure App Service にデプロイする場合
Node.js アプリ向けの Azure App Service の既定の構成は、最も一般的なアプリのニーズに最も合うように設計されています。 Node.js アプリの構成で、パーソナライズされたチューニングの利点を活用してパフォーマンスの向上または CPU/メモリ/ネットワーク リソースのリソース使用量の最適化を行う場合には、マイクロソフトが用意したベスト プラクティスやトラブルシューティング手順を確認できます。 次のドキュメントの記事では、Node.js アプリでの構成に必要となる可能性のある iisnode 設定について、また、アプリが直面する可能性のあるさまざまなシナリオや問題を説明し、これらの問題に対処する方法を示します: [Azure App Service でのノード アプリケーションのベスト プラクティスとトラブルシューティング ガイド](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)。   


