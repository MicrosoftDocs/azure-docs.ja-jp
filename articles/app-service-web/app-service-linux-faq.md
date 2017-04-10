---
title: "Azure App Service Web Apps on Linux のFAQ | Microsoft Docs"
description: "Azure App Service Web Apps on Linux のFAQ。"
keywords: "Azure App Service, Web アプリ, FAQ, Linux, OSS"
services: app-service
documentationCenter: 
authors: ahmedelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 148bc76b7f3e09745cbecfa41710a5e949704948
ms.lasthandoff: 03/08/2017


---

# <a name="azure-app-service-web-apps-on-linux-faq"></a>Azure App Service Web Apps on Linux のFAQ

Azure App Service on Linux (現在プレビュー段階) のリリースでは、機能の追加とプラットフォームの向上に取り組んでいます。 この数か月の間にお客様からよく寄せられた質問 (FAQ) を以下にいくつか示します。
ご質問がある場合は、この記事の最後にあるコメント欄をご利用ください。できるだけ早く回答いたします。

## <a name="built-in-images"></a>組み込まれているイメージ

**Q:** プラットフォームに用意されている組み込みの Docker コンテナーをフォークしたいと思っています。 これらのファイルはどこで入手できますか。

**A:** すべての Docker ファイルは [GitHub](https://github.com/azure-app-service) にあります。 すべての Docker コンテナーは [Docker Hub](https://hub.docker.com/u/appsvc/) にあります。

**Q:** ランタイム スタックを構成する場合、[スタートアップ ファイル] セクションではどのような値が有効ですか。

**A:** Node.Js の場合は、PM2 構成ファイルまたはスクリプト ファイルを指定します。 .NET Core の場合は、コンパイル済みの DLL 名を指定します。 Ruby の場合は、アプリの初期化に使用する Ruby スクリプトを指定できます。

## <a name="management"></a>管理

**Q:** Azure Portal で [再起動] ボタンを押しましたが、Web アプリが再起動しませんでした。 なぜですか。

**A:** 近い将来に [再起動] ボタンを有効にするよう取り組んでいます。 現在は、次の&2; つの選択肢があります。
- ダミー アプリケーション設定を追加または変更します。 これにより、Web アプリが強制的に再起動されます。
- Web アプリを停止してから起動します。

**Q:** アプリ コンテナーの仮想マシン (VM) への接続に Secure Shell (SSH) を使用できますか。

**A:** いいえ。 今後のリリースで、SSH を使用してアプリ コンテナーに接続する方法を提供する予定です。

## <a name="continuous-integrationdeployment"></a>継続的インテグレーション/デプロイ

**Q:** 自分の Web アプリでは、Docker Hub 上のイメージを更新した後も、古い Docker コンテナー イメージを引き続き使用しています。 カスタム コンテナーの継続的な統合/デプロイはサポートしていますか。

**A:** Web アプリを停止してから起動すると、コンテナーを更新できます。 または、ダミー アプリケーション設定を変更または追加して、コンテナーを強制的に更新できます。 今後のリリースで、カスタム コンテナーの継続的インテグレーション/デプロイ機能を追加する予定です。

## <a name="language-support"></a>言語のサポート

**Q:** コンパイルされていない .NET Core アプリはサポートされていますか。

**A:** いいえ。 コンパイル済みの .NET Core アプリはすべての依存関係と共にデプロイする必要があります。 今後のリリースで、完全なデプロイとビルドのエクスペリエンスを計画しています。

**Q:** PHP アプリの依存関係マネージャーとして Composer はサポートされていますか。

**A:** いいえ。 PHP アプリはすべての依存関係と共にデプロイする必要があります。 今後のリリースで、完全なデプロイ エクスペリエンスを計画しています。

## <a name="custom-containers"></a>カスタム コンテナー

**Q:** 自分が所有するカスタム コンテナーを使用しています。 私のアプリは \home\ ディレクトリに存在しますが、[SCM サイト](https://github.com/projectkudu/kudu)や FTP クライアントを使用して内容を参照してもファイルが見つかりません。 ファイルはどこにありますか?

**A:** SMB 共有は \home\ ディレクトリにマウントされます。 これにより、その場所にあるコンテンツが上書きされます。

**Q:** カスタム コンテナー イメージで&1; つ以上のポートを公開したいと思っています。 可能でしょうか。

**A:** 現時点ではサポートされていません。

**Q:** 自分が所有するストレージを持ち込むことはできますか?

**A:** 現時点ではサポートされていません。

**Q:** SCM サイトからカスタム コンテナーのファイル システムや実行中のプロセスを参照できません。 なぜですか?

**A:** SCM サイトは別のコンテナーで実行されています。 アプリ コンテナーのファイル システムや実行中のプロセスをチェックすることはできません。

**Q:** カスタム コンテナーがポート 80 以外のポートをリッスンしています。 そのポートに要求をルーティングするようにアプリを構成するにはどうすればよいですか。

**A:** **PORT** というアプリケーション設定を指定して、必要なポート番号の値を設定できます。

## <a name="pricing-and-sla"></a>料金と SLA

**Q:** パブリック プレビューを使用しているときの料金はいくらですか。

**A:** 通常の Azure App Service の料金で、アプリを実行する時間数の半分が課金されます。 つまり、通常の Azure App Service の料金から 50% 割引になります。

## <a name="other"></a>その他

**Q:** アプリの設定名でサポートされる文字は何ですか。

**A:** アプリケーション設定では、A から Z、a から z、0 から 9、およびアンダースコアのみを使用できます。

**Q:** 新機能はどこでリクエストできますか。

**A:** [Web Apps フィードバック フォーラム](https://aka.ms/webapps-uservoice)で自分のアイデアを送信できます。 アイデアのタイトルに "[Linux]" を追加してください。

## <a name="next-steps"></a>次のステップ
* [App Service on Linux とは](app-service-linux-intro.md)
* [App Service on Linux での Web Apps の作成](app-service-linux-how-to-create-a-web-app.md)

