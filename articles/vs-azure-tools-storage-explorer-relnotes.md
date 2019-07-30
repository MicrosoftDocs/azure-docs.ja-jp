---
title: Microsoft Azure Storage Explorer のリリース ノート
description: Microsoft Azure Storage Explorer のリリース ノート
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: 1a051f3a0c55e207e6a53955d1cb4b9ea7e54a4d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544136"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Microsoft Azure Storage Explorer のリリース ノート

この記事には、Azure Storage Explorer 1.9.0 リリースのリリース ノートだけでなく、以前のバージョンのリリース ノートも含まれています。

[Microsoft Azure Storage Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md) は、Windows、macOS、Linux で Azure Storage データを容易に操作できるスタンドアロン アプリです。

## <a name="version-190"></a>バージョン 1.9.0
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Azure Storage Explorer 1.9.0 をダウンロードする
- [Windows 用 Azure Storage Explorer 1.9.0](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Mac 用 Azure Storage Explorer 1.9.0](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Linux 用 Azure Storage Explorer 1.9.0](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>新規

* Azure AD (RBAC または ACL アクセス許可) を使用して BLOB コンテナーをアタッチできるようになりました。 この機能の目的は、コンテナーへのアクセス権はあるが、コンテナーが属しているストレージ アカウントへのアクセス権がないユーザーを支援することです。 この機能の詳細については、ファースト ステップ ガイドを参照してください。
* RBAC でリースの取得と解約が機能するようになりました。 [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* RBAC でアクセス ポリシーの管理とパブリック アクセス レベルの設定が機能するようになりました。 [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* RBAC で BLOB フォルダーの削除が機能するようになりました。 [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* RBAC で BLOB アクセス層の変更が機能するようになりました。 [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* [ヘルプ] → [リセット] を使用してクイック アクセスをすばやくリセットできるようになりました。 [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>プレビュー機能

* プレビューでデバイス コード フローのサインインを使用できるようになりました。 この機能を有効にするには、[プレビュー] の [Use Device Code Flow Sign-in]\(デバイス コード フローのサインインを使用する\) に移動します。 空白のサインイン ウィンドウが表示される問題が発生していた場合は、この機能の方がサインイン形式として信頼性が高い可能性があるので、試してみることをお勧めします。
* プレビューで、AzCopy と統合された Storage Explorer を使用できるようになりました。 この機能を有効にするには、[プレビュー] の [Use AzCopy for Improved Blob Upload and Download]\(向上した Blob アップロードおよびダウンロードに AzCopy を使用する\) に移動します。 AzCopy で実行される BLOB 転送では、速度とパフォーマンスが向上します。

### <a name="fixes"></a>修正

* 1 つのアカウントで 50 を超えるサブスクリプションを読み込めなかったのを修正しました。 [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* 直接リンクがエラーになったときに表示される情報バー上の [サインイン] ボタンが機能しなかったのを修正しました。 [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* MacOS で .app ファイルがアップロードされないのを修正しました。 [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* 失敗した BLOB の名前変更に対して [すべて再試行] が機能しなかったのを修正しました。 [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* BLOB を開くときに [キャンセル] が機能しないのを修正しました。 [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* 製品全体でスペル チェックとヒントに関する複数の問題を修正しました。 これらの問題を報告していただいたすべての方に感謝します。 [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303)、[#1328](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328)、[#1329](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329)、[#1331](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331)、[#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336)、[#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352)、[#1368](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368)、[#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>既知の問題

* AzCopy 以外の Blob のダウンロードを実行すると、サイズの大きいファイルの MD5 が検証されません。 これは、Storage SDK のバグが原因です。 [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* RBAC を使用する場合、Storage Explorer は、ストレージ リソースにアクセスするために管理レイヤーのアクセス許可を必要とします。 詳細については、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)を参照してください。
* プロキシの背後にある場合に ADLS Gen2 BLOB にアクセスしようとすると、失敗する可能性があります。
* BLOB コンテナーなど、SAS URI を使用してアタッチされているリソースからデタッチすると、他のアタッチが正しく表示されないエラーが発生することがあります。 この問題は、グループ ノードを更新するだけで回避できます。 詳細については、#537 をご覧ください。
* VS for Mac を使用しており、カスタム AAD 構成を作成したことがある場合、サインインできないことがあります。 この問題を回避するには、~/.IdentityService/AadConfigurations の内容を削除します。 これを行ってもブロックが解除されない場合は、この問題についてコメントをお寄せください。
* Azurite は、すべての Storage API を完全に実装しているわけではありません。 そのため、開発ストレージに Azurite を使用すると、予期しないエラーや動作が発生する可能性があります。
* まれに、ツリーのフォーカスがクイック アクセスから移動しなくなることがあります。 フォーカスを移動できるようにするには、[すべて更新] をクリックします。
* NodeJS のバグが原因で、OneDrive フォルダーからのアップロードが機能しません。 バグは修正されましたが、Electron にまだ統合されていません。 BLOB コンテナーにアップロードするとき、または BLOB コンテナーからダウンロードするときに、この問題を回避するには、試験段階の AzCopy 機能を使用できます。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、こちらで説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* Azure Stack では、次の機能はサポートされません。 Azure Stack リソースを操作しているときに、これらの機能を使用しようとすると、予期しないエラーが発生する場合があります。
   * ファイル共有
   * アクセス層
   * 論理的な削除
   * ADLS Gen2
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux で Storage Explorer を実行するには、特定の依存関係を先にインストールする必要があります。 詳細については、Storage Explorer の[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies)をご確認ください。

## <a name="previous-releases"></a>以前のリリース

* [バージョン 1.8.1](#version-181)
* [バージョン 1.8.0](#version-180)
* [バージョン 1.7.0](#version-170)
* [バージョン 1.6.2](#version-162)
* [バージョン 1.6.1](#version-161)
* [バージョン 1.6.0](#version-160)
* [バージョン 1.5.0](#version-150)
* [バージョン 1.4.4](#version-144)
* [バージョン 1.4.3](#version-143)
* [バージョン 1.4.2](#version-142)
* [バージョン 1.4.1](#version-141)
* [バージョン 1.3.0](#version-130)
* [バージョン 1.2.0](#version-120)
* [バージョン 1.1.0](#version-110)
* [バージョン 1.0.0](#version-100)
* [バージョン 0.9.6](#version-096)
* [バージョン 0.9.5](#version-095)
* [バージョン 0.9.4 および 0.9.3](#version-094-and-093)
* [バージョン 0.9.2](#version-092)
* [バージョン 0.9.1 および 0.9.0](#version-091-and-090)
* [バージョン 0.8.16](#version-0816)
* [Version 0.8.14](#version-0814)
* [バージョン 0.8.13](#version-0813)
* [バージョン 0.8.12 および 0.8.11 および 0.8.10](#version-0812-and-0811-and-0810)
* [バージョン 0.8.9 および 0.8.8](#version-089-and-088)
* [バージョン 0.8.7](#version-087)
* [バージョン 0.8.6](#version-086)
* [バージョン 0.8.5](#version-085)
* [バージョン 0.8.4](#version-084)
* [バージョン 0.8.3](#version-083)
* [バージョン 0.8.2](#version-082)
* [バージョン 0.8.0](#version-080)
* [バージョン 0.7.20160509.0](#version-07201605090)
* [バージョン 0.7.20160325.0](#version-07201603250)
* [バージョン 0.7.20160129.1](#version-07201601291)
* [バージョン 0.7.20160105.0](#version-07201601050)
* [バージョン 0.7.20151116.0](#version-07201511160)

## <a name="version-181"></a>バージョン 1.8.1
5/13/2019

### <a name="hotfixes"></a>修正プログラム
* リソース レベルで [さらに読み込む] をクリックしても、リソースの次のページが返されないことがあります。 この問題は修正されています。 [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* Windows の場合、1 つのファイルまたはフォルダーがダウンロードされていて、そのファイルまたはフォルダーの名前に Windows パスには無効な文字が含まれていたときは、AzCopy のダウンロードは失敗します。 この問題は修正されています。 [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* 非常にまれな場合ですが、ファイル共有の名前変更を実行したり、ファイル共有で名前変更を実行している間に、名前変更のコピーが失敗した場合や、Storage Explore が Azure によるコピーの成功を確認することができなかった場合、コピーが終了する前に Storage Explore が元のファイルを削除する可能性がありました。 この問題は修正されています。

### <a name="new"></a>新規

* AzCopy の統合されたバージョンがバージョン 10.1.0 に更新されました。
* Ctrl/Cmd+R を使用して、現在フォーカスがあるエディターを更新できるようになりました。 [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Azure Stack Storage API バージョンが 2017-04-17 に変更されました。
* ADLS Gen2 の [アクセスの管理] ダイアログでは、他の POSIX 権限ツールに類似する方法で、マスクの同期状態を維持するようになりました。 また、この UI では、加えられた変更によりユーザーまたはグループの権限がマスクの境界を超える場合には、ユーザーに警告します。 [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* AzCopy のアップロードのために、MD5 ハッシュを計算して設定するフラグが有効になっています。 [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>プレビュー機能

* プレビューでデバイス コード フローのサインインを使用できるようになりました。 この機能を有効にするには、[プレビュー] の [Use Device Code Flow Sign-in]\(デバイス コード フローのサインインを使用する\) に移動します。 空白のサインイン ウィンドウが表示される問題が発生していた場合は、この機能の方がサインイン形式として信頼性が高い可能性があるので、試してみることをお勧めします。
* プレビューで、AzCopy と統合された Storage Explorer を使用できるようになりました。 この機能を有効にするには、[プレビュー] の [Use AzCopy for Improved Blob Upload and Download]\(向上した Blob アップロードおよびダウンロードに AzCopy を使用する\) に移動します。 AzCopy で実行される BLOB 転送では、速度とパフォーマンスが向上します。

### <a name="fixes"></a>修正

* [アクセス ポリシー] ダイアログでは、有効期限のないストレージ アクセス ポリシーに有効期限が設定されなくなりました。 [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* [SAS の生成] ダイアログにいくつかの変更が加えられ、SAS の生成時に保存済みアクセス ポリシーが正しく使用されるようになりました。 [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* 512 バイトに合わせていないファイルをページ BLOB にアップロードしようとすると、Storage Explorer により正確なエラーが表示されるようになりました。 [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* 表示名を使用した BLOB コンテナーのコピーは失敗しました。 現在は、BLOB コンテナーの実際の名前が使用されます。 [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* 名前に Unicode 文字を含む ADLS Gen2 フォルダーに対して特定のアクションを実行しようとすると、失敗しました。 すべてのアクションが正常に実行されるようになりました。 [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>既知の問題

* AzCopy 以外の Blob のダウンロードを実行すると、サイズの大きいファイルの MD5 が検証されません。 これは、Storage SDK のバグが原因です。 [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* RBAC を使用する場合、Storage Explorer は、ストレージ リソースにアクセスするために管理レイヤーのアクセス許可を必要とします。 詳細については、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)を参照してください。
* プロキシの背後にある場合に ADLS Gen2 BLOB にアクセスしようとすると、失敗する可能性があります。
* BLOB コンテナーなど、SAS URI を使用してアタッチされているリソースからデタッチすると、他のアタッチが正しく表示されないエラーが発生することがあります。 この問題は、グループ ノードを更新するだけで回避できます。 詳細については、#537 をご覧ください。
* VS for Mac を使用しており、カスタム AAD 構成を作成したことがある場合、サインインできないことがあります。 この問題を回避するには、~/.IdentityService/AadConfigurations の内容を削除します。 これを行ってもブロックが解除されない場合は、この問題についてコメントをお寄せください。
* Azurite は、すべての Storage API を完全に実装しているわけではありません。 そのため、開発ストレージに Azurite を使用すると、予期しないエラーや動作が発生する可能性があります。
* まれに、ツリーのフォーカスがクイック アクセスから移動しなくなることがあります。 フォーカスを移動できるようにするには、[すべて更新] をクリックします。
* NodeJS のバグが原因で、OneDrive フォルダーからのアップロードが機能しません。 バグは修正されましたが、Electron にまだ統合されていません。 BLOB コンテナーにアップロードするとき、または BLOB コンテナーからダウンロードするときに、この問題を回避するには、試験段階の AzCopy 機能を使用できます。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、こちらで説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* Azure Stack では、次の機能はサポートされません。 Azure Stack リソースを操作しているときに、これらの機能を使用しようとすると、予期しないエラーが発生する場合があります。
   * ファイル共有
   * アクセス層
   * 論理的な削除
   * ADLS Gen2
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux で Storage Explorer を実行するには、特定の依存関係を先にインストールする必要があります。 詳細については、Storage Explorer の[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies)をご確認ください。

## <a name="version-180"></a>バージョン 1.8.0
5/1/2019

### <a name="new"></a>新規

* AzCopy の統合されたバージョンがバージョン 10.1.0 に更新されました。
* Ctrl/Cmd+R を使用して、現在フォーカスがあるエディターを更新できるようになりました。 [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Azure Stack Storage API バージョンが 2017-04-17 に変更されました。
* ADLS Gen2 の [アクセスの管理] ダイアログでは、他の POSIX 権限ツールに類似する方法で、マスクの同期状態を維持するようになりました。 また、この UI では、加えられた変更によりユーザーまたはグループの権限がマスクの境界を超える場合には、ユーザーに警告します。 [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* AzCopy のアップロードのために、MD5 ハッシュを計算して設定するフラグが有効になっています。 [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>プレビュー機能

* プレビューでデバイス コード フローのサインインを使用できるようになりました。 この機能を有効にするには、[プレビュー] の [Use Device Code Flow Sign-in]\(デバイス コード フローのサインインを使用する\) に移動します。 空白のサインイン ウィンドウが表示される問題が発生していた場合は、この機能の方がサインイン形式として信頼性が高い可能性があるので、試してみることをお勧めします。
* プレビューで、AzCopy と統合された Storage Explorer を使用できるようになりました。 この機能を有効にするには、[プレビュー] の [Use AzCopy for Improved Blob Upload and Download]\(向上した Blob アップロードおよびダウンロードに AzCopy を使用する\) に移動します。 AzCopy で実行される BLOB 転送では、速度とパフォーマンスが向上します。

### <a name="fixes"></a>修正

* [アクセス ポリシー] ダイアログでは、有効期限のないストレージ アクセス ポリシーに有効期限が設定されなくなりました。 [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* [SAS の生成] ダイアログにいくつかの変更が加えられ、SAS の生成時に保存済みアクセス ポリシーが正しく使用されるようになりました。 [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* 512 バイトに合わせていないファイルをページ BLOB にアップロードしようとすると、Storage Explorer により正確なエラーが表示されるようになりました。 [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* 表示名を使用した BLOB コンテナーのコピーは失敗しました。 現在は、BLOB コンテナーの実際の名前が使用されます。 [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* 名前に Unicode 文字を含む ADLS Gen2 フォルダーに対して特定のアクションを実行しようとすると、失敗しました。 すべてのアクションが正常に実行されるようになりました。 [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>既知の問題

* AzCopy 以外の Blob のダウンロードを実行すると、サイズの大きいファイルの MD5 が検証されません。 これは、Storage SDK のバグが原因です。 [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* RBAC を使用する場合、Storage Explorer は、ストレージ リソースにアクセスするために管理レイヤーのアクセス許可を必要とします。 詳細については、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)を参照してください。
* プロキシの背後にある場合に ADLS Gen2 BLOB にアクセスしようとすると、失敗する可能性があります。
* BLOB コンテナーなど、SAS URI を使用してアタッチされているリソースからデタッチすると、他のアタッチが正しく表示されないエラーが発生することがあります。 この問題は、グループ ノードを更新するだけで回避できます。 詳細については、#537 をご覧ください。
* VS for Mac を使用しており、カスタム AAD 構成を作成したことがある場合、サインインできないことがあります。 この問題を回避するには、~/.IdentityService/AadConfigurations の内容を削除します。 これを行ってもブロックが解除されない場合は、この問題についてコメントをお寄せください。
* Azurite は、すべての Storage API を完全に実装しているわけではありません。 そのため、開発ストレージに Azurite を使用すると、予期しないエラーや動作が発生する可能性があります。
* まれに、ツリーのフォーカスがクイック アクセスから移動しなくなることがあります。 フォーカスを移動できるようにするには、[すべて更新] をクリックします。
* NodeJS のバグが原因で、OneDrive フォルダーからのアップロードが機能しません。 バグは修正されましたが、Electron にまだ統合されていません。 BLOB コンテナーにアップロードするとき、または BLOB コンテナーからダウンロードするときに、この問題を回避するには、試験段階の AzCopy 機能を使用できます。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、こちらで説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* Azure Stack では、次の機能はサポートされません。 Azure Stack リソースを操作しているときに、これらの機能を使用しようとすると、予期しないエラーが発生する場合があります。
   * ファイル共有
   * アクセス層
   * 論理的な削除
   * ADLS Gen2
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux で Storage Explorer を実行するには、特定の依存関係を先にインストールする必要があります。 詳細については、Storage Explorer の[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies)をご確認ください。

## <a name="version-170"></a>バージョン 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Azure Storage Explorer 1.7.0 をダウンロードする
- [Windows 用 Azure Storage Explorer 1.7.0](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Mac 用 Azure Storage Explorer 1.7.0](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Linux 用 Azure Storage Explorer 1.7.0](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>新規

* ADLS Gen2 コンテナー、ファイル、またはフォルダーへのアクセスを管理する際に、所有者と所有グループを変更できるようになりました。
* Windows では、製品内からの Storage Explorer の更新は増分インストールになりました。 これにより、更新エクスペリエンスが高速化されます。 クリーン インストールを使用する場合は、[インストーラー](https://azure.microsoft.com/features/storage-explorer/)を自分でダウンロードし、手動でインストールします。 #1089

### <a name="preview-features"></a>プレビュー機能

* プレビューでデバイス コード フローのサインインを使用できるようになりました。 この機能を有効にするには、[プレビュー] の [Use Device Code Flow Sign-in]\(デバイス コード フローのサインインを使用する\) に移動します。 空白のサインイン ウィンドウが表示される問題が発生していた場合は、この機能の方がサインイン形式として信頼性が高い可能性があるので、試してみることをお勧めします。 #938
* プレビューで、AzCopy と統合された Storage Explorer を使用できるようになりました。 この機能を有効にするには、[プレビュー] の [Use AzCopy for Improved Blob Upload and Download]\(向上した Blob アップロードおよびダウンロードに AzCopy を使用する\) に移動します。 AzCopy で実行される BLOB 転送では、速度とパフォーマンスが向上します。

### <a name="fixes"></a>修正

* AzCopy が有効なっているときに、アップロードする BLOB の種類を選択できるようになりました。 #1111
* 以前は、ADLS Gen2 ストレージ アカウントに対して静的な Web サイトを有効化し、名前とキーをアタッチした場合、Storage Explorer では、階層型名前空間が有効になったことが検出されませんでした。 この問題は修正されています。 #1081
* BLOB エディターで、残りの保持日数またはステータスによる並べ替えに障害が発生していました。 この問題は修正されています。 #1106
* 1\.5.0 以降の Storage Explorer では、名前変更またはコピー/貼り付け時に、サーバー側の貼り付けが完了するまで待機しないまま、操作の完了が報告されていました。 この問題は修正されています。 #976
* 実験用の AzCopy 機能を使用する際に、[Copy command to clipboard]\(コマンドをクリップボードにコピー\) をクリックした後にコピーしたコマンドが、実行可能にならない場合がありました。 現在は、転送を手動で実行するために必要なすべてのコマンドがコピーされます。 #1079
* 以前は、プロキシの背後にある場合、ADLS Gen2 BLOB にアクセスできませんでした。 これは、Storage SDK で使用される新しいネットワーク ライブラリのバグが原因でした。 1\.7.0 では、この問題の解消が試行されましたが、一部のユーザーでは引き続きこの問題が発生する場合があります。 完全な修正プログラムは今後の更新でリリースされます。 #1090
* 1\.7.0 のファイル保存ダイアログでは、前回ファイルを保存した場所が正しく記憶されるようになりました。 #16
* プロパティ パネルで、ストレージ アカウントの SKU レベルがアカウントの種類として表示されていました。 この問題は修正されています。 #654
* BLOB の名前を正しく入力しても、BLOB のリースを中断できない場合がありました。 この問題は修正されています。 #1070

### <a name="known-issues"></a>既知の問題

* RBAC を使用する場合、Storage Explorer は、ストレージ リソースにアクセスするために管理レイヤーのアクセス許可を必要とします。 詳細については、[トラブルシューティング ガイド](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)を参照してください。
* プロキシの背後にある場合に ADLS Gen2 BLOB にアクセスしようとすると、失敗する可能性があります。
* BLOB コンテナーなど、SAS URI を使用してアタッチされているリソースからデタッチすると、他のアタッチが正しく表示されないエラーが発生することがあります。 この問題は、グループ ノードを更新するだけで回避できます。 詳細については、#537 をご覧ください。
* BLOB コンテナーなど、SAS URI を使用してアタッチされているリソースからデタッチすると、他のアタッチが正しく表示されないエラーが発生することがあります。 この問題は、グループ ノードを更新するだけで回避できます。 詳細については、#537 を参照してください。
* VS for Mac を使用しており、カスタム AAD 構成を作成したことがある場合、サインインできないことがあります。 この問題を回避するには、~/.IdentityService/AadConfigurations の内容を削除します。 これを行ってもブロックが解除されない場合は、この問題についてコメントをお寄せください。
* Azurite は、すべての Storage API を完全に実装しているわけではありません。 そのため、開発ストレージに Azurite を使用すると、予期しないエラーや動作が発生する可能性があります。
* まれに、ツリーのフォーカスがクイック アクセスから移動しなくなることがあります。 フォーカスを移動できるようにするには、[すべて更新] をクリックします。
* NodeJS のバグが原因で、OneDrive フォルダーからのアップロードが機能しません。 バグは修正されましたが、Electron にまだ統合されていません。 BLOB コンテナーにアップロードするとき、または BLOB コンテナーからダウンロードするときに、この問題を回避するには、試験段階の AzCopy 機能を使用できます。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、こちらで説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* Azure Stack では、次の機能はサポートされません。 Azure Stack リソースを操作しているときに、これらの機能を使用しようとすると、予期しないエラーが発生する場合があります。
   * ファイル共有
   * アクセス層
   * 論理的な削除
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux ユーザーは、[.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) をインストールする必要があります。
* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>バージョン 1.6.2
1/9/2019

### <a name="hotfixes"></a>修正プログラム
* 1\.6.1 では、ObjectId によって ADLS Gen2 ACL に追加される、ユーザーではないエンティティは、常にグループとして追加されました。 現在は、グループのみがグループとして追加され、エンタープライズ アプリケーションやサービス プリンシパルなどのエンティティはユーザーとして追加されます。 [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* コンテナーを持たない ADLS Gen2 Storage アカウントが名前とキーでアタッチされた場合、Storage Explorer ではそのストレージ アカウントが ADLS Gen2 であることが検出されませんでした。 この問題は修正されています。 [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* 1\.6.0 では、コピーおよび貼り付けでの競合に対して、解決を求めるメッセージが表示されませんでした。 代わりに、競合するコピーは単に失敗しました。 現在は、最初の競合では、ユーザーに解決するかどうかを確認するメッセージが表示されます。 [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* API の制限により、[アクセスの管理] ダイアログの ObjectId のすべての検証が無効になりました。 検証はユーザー UPN に対してのみ実行されるようになりました。 [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* ADLS Gen2 の [アクセスの管理] ダイアログでは、グループのアクセス許可を変更できませんでした。 この問題は修正されています。 [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* ADLS Gen2 エディターにドラッグ アンド ドロップによるアップロードのサポートが追加されました。 [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* ADLS Gen2 ファイルおよびフォルダーのプロパティ ダイアログに表示される URL プロパティに "/" が不足している場合がありました。 この問題は修正されています。 [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* ADLS Gen2 コンテナー、ファイル、またはフォルダーの現在のアクセス許可を取得できなかった場合、アクティビティ ログにエラーが適切に表示されるようになりました。 [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Windows の MAX_PATH よりも長いパスが作成される可能性を減らすために、ファイルを開くために作成される一時パスが短くなりました。 [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* サインインしているユーザーがおらず、リソースがアタッチされていない場合に [接続] ダイアログが正常に表示されるようになりました。 [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* 1\.6.0 では、HNS 以外の BLOB およびファイルのプロパティを保存すると、すべてのプロパティの値がエンコードされました。 この結果、ASCII 文字のみが含まれている値が不必要にエンコードされていました。 ASCII 以外の文字が含まれている場合にのみ、値がエンコードされるようになりました。 [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* 読み取りアクセス許可がない SAS を使用した場合、HNS 以外の BLOB コンテナーにフォルダーをアップロードできませんでした。 この問題は修正されています。 [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* AzCopy の転送のキャンセルが機能しませんでした。 この問題は修正されています。 [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* 名前にスペースが含まれているフォルダーを ADLS Gen2 の BLOB コンテナーからダウンロードしようとすると、AzCopy が失敗しました。 この問題は修正されています。 [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* 1\.6.0 では、CosmosDB エディターが壊れていました。 これが修正されました。 [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>新規

* Storage Explorer を使用して、[RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409) 経由で BLOB データにアクセスできるようになりました。 サインインしている場合に、自分のストレージ アカウントのキーを Storage Explorer が取得できないと、データ操作時の認証に OAuth トークンが使用されます。
* Storage Explorer は ADLS Gen2 ストレージ アカウントをサポートするようになりました。 ストレージ アカウントに対して階層型名前空間が有効になっていることを Storage Explorer が検出すると、ストレージ アカウントの名前の横に "(ADLS Gen2 プレビュー)" と表示されます。 Storage Explorer では、サインインしている場合に階層型名前空間が有効になっているかどうかや、ストレージ アカウントに名前とキーを関連付けているかどうかを検出できます。 ADLS Gen2 ストレージ アカウントでは、Storage Explorer を使用して、以下の操作を実行できます。
  * コンテナーの作成と削除
  * コンテナーのプロパティとアクセス許可の管理 (左側)
  * コンテナー内のデータの表示および移動
  * 新しいフォルダーの作成
  * ファイルおよびフォルダーのアップロード、ダウンロード、名前変更、削除
  * ファイルおよびフォルダーのプロパティとアクセス許可の管理 (右側)
    
    論理的な削除やスナップショットなど、その他の一般的な BLOB 機能は現在使用できません。 アクセス許可の管理は、サインインしている場合にのみ使用できます。 さらに、ADLS Gen2 ストレージ アカウントで作業している場合、Storage Explorer では、すべてのアップロードおよびダウンロードに AzCopy が使用されます。また、既定ですべての操作に名前とキー資格情報が使用されます (使用可能な場合)。
* 多数のユーザー フィードバックを受け、リースの解約をもう一度使用して、一度に複数の BLOB でリースを解約できるようになっています。

### <a name="known-issues"></a>既知の問題

* ADLS Gen2 ストレージ アカウントからダウンロードしているときに、転送中のファイルの 1 つが既に存在していると、AzCopy がクラッシュすることがあります。 今後の修正プログラムで修正される予定です。
* BLOB コンテナーなど、SAS URI を使用してアタッチされているリソースからデタッチすると、他のアタッチが正しく表示されないエラーが発生することがあります。 この問題は、グループ ノードを更新するだけで回避できます。 詳細については、#537 を参照してください。
* VS for Mac を使用しており、カスタム AAD 構成を作成したことがある場合、サインインできないことがあります。 この問題を回避するには、~/.IdentityService/AadConfigurations の内容を削除します。 これを行ってもブロックが解除されない場合は、この問題についてコメントをお寄せください。
* Azurite は、すべての Storage API を完全に実装しているわけではありません。 そのため、開発ストレージに Azurite を使用すると、予期しないエラーや動作が発生する可能性があります。
* まれに、ツリーのフォーカスがクイック アクセスから移動しなくなることがあります。 フォーカスを移動できるようにするには、[すべて更新] をクリックします。
* NodeJS のバグが原因で、OneDrive フォルダーからのアップロードが機能しません。 バグは修正されましたが、Electron にまだ統合されていません。 BLOB コンテナーにアップロードするとき、または BLOB コンテナーからダウンロードするときに、この問題を回避するには、試験段階の AzCopy 機能を使用できます。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、こちらで説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* Azure Stack では、次の機能はサポートされません。 Azure Stack リソースを操作しているときに、これらの機能を使用しようとすると、予期しないエラーが発生する場合があります。
   * ファイル共有
   * アクセス層
   * 論理的な削除
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux ユーザーは、[.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) をインストールする必要があります。
* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>バージョン 1.6.1
2018/12/18

### <a name="hotfixes"></a>修正プログラム
* API の制限により、[アクセスの管理] ダイアログの ObjectId のすべての検証が無効になりました。 検証はユーザー UPN に対してのみ実行されるようになりました。 [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* ADLS Gen2 の [アクセスの管理] ダイアログでは、グループのアクセス許可を変更できませんでした。 この問題は修正されています。 [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* ADLS Gen2 エディターにドラッグ アンド ドロップによるアップロードのサポートが追加されました。 [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* ADLS Gen2 ファイルおよびフォルダーのプロパティ ダイアログに表示される URL プロパティに "/" が不足している場合がありました。 この問題は修正されています。 [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* ADLS Gen2 コンテナー、ファイル、またはフォルダーの現在のアクセス許可を取得できなかった場合、アクティビティ ログにエラーが適切に表示されるようになりました。 [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Windows の MAX_PATH よりも長いパスが作成される可能性を減らすために、ファイルを開くために作成される一時パスが短くなりました。 [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* サインインしているユーザーがおらず、リソースがアタッチされていない場合に [接続] ダイアログが正常に表示されるようになりました。 [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* 1\.6.0 では、HNS 以外の BLOB およびファイルのプロパティを保存すると、すべてのプロパティの値がエンコードされました。 この結果、ASCII 文字のみが含まれている値が不必要にエンコードされていました。 ASCII 以外の文字が含まれている場合にのみ、値がエンコードされるようになりました。 [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* 読み取りアクセス許可がない SAS を使用した場合、HNS 以外の BLOB コンテナーにフォルダーをアップロードできませんでした。 この問題は修正されています。 [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* AzCopy の転送のキャンセルが機能しませんでした。 この問題は修正されています。 [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* 名前にスペースが含まれているフォルダーを ADLS Gen2 の BLOB コンテナーからダウンロードしようとすると、AzCopy が失敗しました。 この問題は修正されています。 [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* 1\.6.0 では、CosmosDB エディターが壊れていました。 これが修正されました。 [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>新規

* Storage Explorer を使用して、[RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409) 経由で BLOB データにアクセスできるようになりました。 サインインしている場合に、自分のストレージ アカウントのキーを Storage Explorer が取得できないと、データ操作時の認証に OAuth トークンが使用されます。
* Storage Explorer は ADLS Gen2 ストレージ アカウントをサポートするようになりました。 ストレージ アカウントに対して階層型名前空間が有効になっていることを Storage Explorer が検出すると、ストレージ アカウントの名前の横に "(ADLS Gen2 プレビュー)" と表示されます。 Storage Explorer では、サインインしている場合に階層型名前空間が有効になっているかどうかや、ストレージ アカウントに名前とキーを関連付けているかどうかを検出できます。 ADLS Gen2 ストレージ アカウントでは、Storage Explorer を使用して、以下の操作を実行できます。
  * コンテナーの作成と削除
  * コンテナーのプロパティとアクセス許可の管理 (左側)
  * コンテナー内のデータの表示および移動
  * 新しいフォルダーの作成
  * ファイルおよびフォルダーのアップロード、ダウンロード、名前変更、削除
  * ファイルおよびフォルダーのプロパティとアクセス許可の管理 (右側)
    
    論理的な削除やスナップショットなど、その他の一般的な BLOB 機能は現在使用できません。 アクセス許可の管理は、サインインしている場合にのみ使用できます。 さらに、ADLS Gen2 ストレージ アカウントで作業している場合、Storage Explorer では、すべてのアップロードおよびダウンロードに AzCopy が使用されます。また、既定ですべての操作に名前とキー資格情報が使用されます (使用可能な場合)。
* 多数のユーザー フィードバックを受け、リースの解約をもう一度使用して、一度に複数の BLOB でリースを解約できるようになっています。

### <a name="known-issues"></a>既知の問題

* ADLS Gen2 ストレージ アカウントからダウンロードしているときに、転送中のファイルの 1 つが既に存在していると、AzCopy がクラッシュすることがあります。 今後の修正プログラムで修正される予定です。
* BLOB コンテナーなど、SAS URI を使用してアタッチされているリソースからデタッチすると、他のアタッチが正しく表示されないエラーが発生することがあります。 この問題は、グループ ノードを更新するだけで回避できます。 詳細については、#537 を参照してください。
* VS for Mac を使用しており、カスタム AAD 構成を作成したことがある場合、サインインできないことがあります。 この問題を回避するには、~/.IdentityService/AadConfigurations の内容を削除します。 これを行ってもブロックが解除されない場合は、この問題についてコメントをお寄せください。
* Azurite は、すべての Storage API を完全に実装しているわけではありません。 そのため、開発ストレージに Azurite を使用すると、予期しないエラーや動作が発生する可能性があります。
* まれに、ツリーのフォーカスがクイック アクセスから移動しなくなることがあります。 フォーカスを移動できるようにするには、[すべて更新] をクリックします。
* NodeJS のバグが原因で、OneDrive フォルダーからのアップロードが機能しません。 バグは修正されましたが、Electron にまだ統合されていません。 BLOB コンテナーにアップロードするとき、または BLOB コンテナーからダウンロードするときに、この問題を回避するには、試験段階の AzCopy 機能を使用できます。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、こちらで説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* Azure Stack では、次の機能はサポートされません。 Azure Stack リソースを操作しているときに、これらの機能を使用しようとすると、予期しないエラーが発生する場合があります。
   * ファイル共有
   * アクセス層
   * 論理的な削除
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux ユーザーは、[.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) をインストールする必要があります。
* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>バージョン 1.6.0
2018/12/5

### <a name="new"></a>新規

* Storage Explorer を使用して、[RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409) 経由で BLOB データにアクセスできるようになりました。 サインインしている場合に、自分のストレージ アカウントのキーを Storage Explorer が取得できないと、データ操作時の認証に OAuth トークンが使用されます。
* Storage Explorer は ADLS Gen2 ストレージ アカウントをサポートするようになりました。 ストレージ アカウントに対して階層型名前空間が有効になっていることを Storage Explorer が検出すると、ストレージ アカウントの名前の横に "(ADLS Gen2 プレビュー)" と表示されます。 Storage Explorer では、サインインしている場合に階層型名前空間が有効になっているかどうかや、ストレージ アカウントに名前とキーを関連付けているかどうかを検出できます。 ADLS Gen2 ストレージ アカウントでは、Storage Explorer を使用して、以下の操作を実行できます。
  * コンテナーの作成と削除
  * コンテナーのプロパティとアクセス許可の管理 (左側)
  * コンテナー内のデータの表示および移動
  * 新しいフォルダーの作成
  * ファイルおよびフォルダーのアップロード、ダウンロード、名前変更、削除
  * ファイルおよびフォルダーのプロパティとアクセス許可の管理 (右側)
    
    論理的な削除やスナップショットなど、その他の一般的な BLOB 機能は現在使用できません。 アクセス許可の管理は、サインインしている場合にのみ使用できます。 さらに、ADLS Gen2 ストレージ アカウントで作業している場合、Storage Explorer では、すべてのアップロードおよびダウンロードに AzCopy が使用されます。また、既定ですべての操作に名前とキー資格情報が使用されます (使用可能な場合)。
* 多数のユーザー フィードバックを受け、リースの解約をもう一度使用して、一度に複数の BLOB でリースを解約できるようになっています。

### <a name="known-issues"></a>既知の問題

* ADLS Gen2 ストレージ アカウントからダウンロードしているときに、転送中のファイルの 1 つが既に存在していると、AzCopy がクラッシュすることがあります。 今後の修正プログラムで修正される予定です。
* BLOB コンテナーなど、SAS URI を使用してアタッチされているリソースからデタッチすると、他のアタッチが正しく表示されないエラーが発生することがあります。 この問題は、グループ ノードを更新するだけで回避できます。 詳細については、#537 を参照してください。
* VS for Mac を使用しており、カスタム AAD 構成を作成したことがある場合、サインインできないことがあります。 この問題を回避するには、~/.IdentityService/AadConfigurations の内容を削除します。 これを行ってもブロックが解除されない場合は、この問題についてコメントをお寄せください。
* Azurite は、すべての Storage API を完全に実装しているわけではありません。 そのため、開発ストレージに Azurite を使用すると、予期しないエラーや動作が発生する可能性があります。
* まれに、ツリーのフォーカスがクイック アクセスから移動しなくなることがあります。 フォーカスを移動できるようにするには、[すべて更新] をクリックします。
* NodeJS のバグが原因で、OneDrive フォルダーからのアップロードが機能しません。 バグは修正されましたが、Electron にまだ統合されていません。 BLOB コンテナーにアップロードするとき、または BLOB コンテナーからダウンロードするときに、この問題を回避するには、試験段階の AzCopy 機能を使用できます。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、こちらで説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* Azure Stack では、次の機能はサポートされません。 Azure Stack リソースを操作しているときに、これらの機能を使用しようとすると、予期しないエラーが発生する場合があります。
   * ファイル共有
   * アクセス層
   * 論理的な削除
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux ユーザーは、[.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) をインストールする必要があります。
* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>バージョン 1.5.0
10/29/2018

### <a name="new"></a>新規

* BLOB のアップロードおよびダウンロードに、[AzCopy v10 (プレビュー)](https://github.com/Azure/azure-storage-azcopy) を使用できるようになりました。 この機能を有効にするには、[試験段階] メニューに移動して、[Use AzCopy for Improved Blob Upload and Download]\(改善された BLOB のアップロードとダウンロードに AzCopy を使用する\) をクリックします。 有効な場合は、次のシナリオで AzCopy が使用されます。
   * ツール バーか、ドラッグ アンド ドロップのいずれかで、フォルダーとファイルを BLOB コンテナーにアップロードする。
   * ツール バーまたはコンテキスト メニューのいずれかで、フォルダーとファイルをダウンロードする。

* また、AzCopy を使用する場合
   * 使用した AzCopy コマンドをコピーして、クリップボードへの転送を実行できます。 アクティビティ ログで [Copy AzCopy Command to Clipboard]\(AzCopy コマンドをクリップボードにコピー\) をクリックするだけです。
   * アップロードした後に、手動で BLOB エディターを更新する必要があります。
   * BLOB を追加するためのファイルのアップロードはサポートされておらず、vhd ファイルはページ BLOB としてアップロードされ、その他のファイルはすべてブロック BLOB としてアップロードされます。
   * アップロードまたはダウンロード時に発生するエラーや競合は、アップロードまたはダウンロードが完了するまで提示されません。

最後に、ファイル共有での AzCopy の使用のサポートは、今後提供される予定です。
* 現在、Storage Explorer では Electron バージョン 2.0.11 を使用しています。
* リースの解約は一度に 1 つの BLOB 上のみで実行することができます。 さらに、解約するリースを含む BLOB の名前を入力する必要があります。 この変更は、特に VM の場合に、誤ってリースを解約する可能性を低減するために加えられました。 #394
* サインインの問題が発生した場合、認証のリセットを試行できるようになりました。 この機能にアクセスするには、[ヘルプ] メニューに移動して [リセット] をクリックします。 #419

### <a name="fix"></a>解決策

* 強力なユーザーのフィードバックの後、既定のエミュレーター ノードを再度有効にしています。 引き続き [接続] ダイアログからさらにエミュレーターの接続を追加することはできますが、ご利用のエミュレーターが既定のポートを使用するように構成されている場合は、[Local & Attached]\(ローカルおよびアタッチ済み\)、[ストレージ アカウント] の下で "エミュレーター * 既定のポート" ノードを使用することもできます。 #669
* Storage Explorer では、先頭または末尾に空白がある BLOB メタデータの値を設定することはできなくなりました。 #760
* [サインイン] ボタンは、[接続] ダイアログの同じページ上で常に有効になっていました。 必要に応じて、無効にされるようになりました。 #761
* クイック アクセスの項目が追加されていない場合に、クイック アクセスによってコンソール内にエラーが生成されることはなくなりました。

### <a name="known-issues"></a>既知の問題

* BLOB コンテナーなど、SAS URI を使用してアタッチされているリソースからデタッチすると、他のアタッチが正しく表示されないエラーが発生することがあります。 この問題は、グループ ノードを更新するだけで回避できます。 詳細については、#537 を参照してください。
* VS for Mac を使用しており、カスタム AAD 構成を作成したことがある場合、サインインできないことがあります。 この問題を回避するには、~/.IdentityService/AadConfigurations の内容を削除します。 これを行ってもブロックが解除されない場合は、この問題についてコメントをお寄せください。
* Azurite は、すべての Storage API を完全に実装しているわけではありません。 そのため、開発ストレージに Azurite を使用すると、予期しないエラーや動作が発生する可能性があります。
* まれに、ツリーのフォーカスがクイック アクセスから移動しなくなることがあります。 フォーカスを移動できるようにするには、[すべて更新] をクリックします。
* NodeJS のバグが原因で、OneDrive フォルダーからのアップロードが機能しません。 バグは修正されましたが、Electron にまだ統合されていません。 BLOB コンテナーにアップロードするとき、または BLOB コンテナーからダウンロードするときに、この問題を回避するには、試験段階の AzCopy 機能を使用できます。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、こちらで説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* Azure Stack では、次の機能はサポートされません。 Azure Stack リソースを操作しているときに、これらの機能を使用しようとすると、予期しないエラーが発生する場合があります。
   * ファイル共有
   * アクセス層
   * 論理的な削除
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux ユーザーは、[.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) をインストールする必要があります。
* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>バージョン 1.4.4
10/15/2018

### <a name="hotfixes"></a>修正プログラム
* Azure Resource Management の API バージョンは、Azure US Government ユーザーのブロックを解除するためにロールバックされました。 [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* 読み込みスピナーは現在、CSS アニメーションを使用して、Storage Explorer によって使用される GPU の量を削減しています。 [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>新規
* SAS 接続やエミュレーターなどの外部リソースのアタッチが大幅に改善されました。 ここでは、次の操作を実行できます。
   * アタッチしているリソースの表示名をカスタマイズします。 [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * 異なるポートを使用して複数のローカル エミュレーターにアタッチします。 [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * クイック アクセスにアタッチされているリソースを追加します。 [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer は論理的な削除をサポートするようになりました。 次のようにすることができます。
   * ストレージ アカウントの BLOB コンテナー ノードを右クリックして、論理的な削除のポリシーを構成します。
   * ナビゲーション バーの横にあるドロップダウン リストで "アクティブな BLOB と削除された BLOB" を選択して、BLOB エディターで論理的に削除された BLOB を表示します。
   * 論理的に削除された BLOB の削除を取り消します。

### <a name="fixes"></a>修正
* Premium Storage アカウントでは CORS をサポートしていないため、"CORS 設定の構成" アクションを Premium Storage アカウントで使用できなくなりました。 [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* SAS のアタッチされたサービスに Shared Access Signature プロパティが追加されました。 [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* クイック アクセスにピン留めされた BLOB および GPV2 ストレージ アカウントで、"既定のアクセス層の設定" アクションを使用できるようになりました。 [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* 場合によっては、Storage Explorer は Classic Storage アカウントを表示するのに失敗します。 [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>既知の問題
* Azure Storage エミュレーターや Azurite などのエミュレーターを使用している場合、エミュレーターに既定のポートでの接続をリッスンさせる必要があります。 そうしないと、Storage Explorer がそれらに接続できません。
* VS for Mac を使用しており、カスタム AAD 構成を作成したことがある場合、サインインできないことがあります。 この問題を回避するには、~/.IdentityService/AadConfigurations の内容を削除します。 これを行ってもブロックが解除されない場合は、[この問題](https://github.com/Microsoft/AzureStorageExplorer/issues/97)についてコメントをお寄せください。
* Azurite は、すべての Storage API を完全に実装しているわけではありません。 そのため、開発ストレージに Azurite を使用すると、予期しないエラーや動作が発生する可能性があります。
* まれに、ツリーのフォーカスがクイック アクセスから移動しなくなることがあります。 フォーカスを移動できるようにするには、[すべて更新] をクリックします。
* NodeJS のバグが原因で、OneDrive フォルダーからのアップロードが機能しません。 バグは修正されましたが、Electron にまだ統合されていません。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、[こちら](https://github.com/Azure/azure-storage-node/issues/317)で説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* 現在、Azure Stack ではファイル共有をサポートしていませんが、ファイル共有ノードがアタッチされた Azure Stack ストレージ アカウントの下に表示され続けます。
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux ユーザーは、[.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) をインストールする必要があります。
* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>バージョン 1.4.3
10/11/2018

### <a name="hotfixes"></a>修正プログラム
* Azure Resource Management の API バージョンは、Azure US Government ユーザーのブロックを解除するためにロールバックされました。 [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* 読み込みスピナーは現在、CSS アニメーションを使用して、Storage Explorer によって使用される GPU の量を削減しています。 [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>新規
* SAS 接続やエミュレーターなどの外部リソースのアタッチが大幅に改善されました。 ここでは、次の操作を実行できます。
   * アタッチしているリソースの表示名をカスタマイズします。 [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * 異なるポートを使用して複数のローカル エミュレーターにアタッチします。 [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * クイック アクセスにアタッチされているリソースを追加します。 [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer は論理的な削除をサポートするようになりました。 次のようにすることができます。
   * ストレージ アカウントの BLOB コンテナー ノードを右クリックして、論理的な削除のポリシーを構成します。
   * ナビゲーション バーの横にあるドロップダウン リストで "アクティブな BLOB と削除された BLOB" を選択して、BLOB エディターで論理的に削除された BLOB を表示します。
   * 論理的に削除された BLOB の削除を取り消します。

### <a name="fixes"></a>修正
* Premium Storage アカウントでは CORS をサポートしていないため、"CORS 設定の構成" アクションを Premium Storage アカウントで使用できなくなりました。 [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* SAS のアタッチされたサービスに Shared Access Signature プロパティが追加されました。 [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* クイック アクセスにピン留めされた BLOB および GPV2 ストレージ アカウントで、"既定のアクセス層の設定" アクションを使用できるようになりました。 [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* 場合によっては、Storage Explorer は Classic Storage アカウントを表示するのに失敗します。 [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>既知の問題
* Azure Storage エミュレーターや Azurite などのエミュレーターを使用している場合、エミュレーターに既定のポートでの接続をリッスンさせる必要があります。 そうしないと、Storage Explorer がそれらに接続できません。
* VS for Mac を使用しており、カスタム AAD 構成を作成したことがある場合、サインインできないことがあります。 この問題を回避するには、~/.IdentityService/AadConfigurations の内容を削除します。 これを行ってもブロックが解除されない場合は、[この問題](https://github.com/Microsoft/AzureStorageExplorer/issues/97)についてコメントをお寄せください。
* Azurite は、すべての Storage API を完全に実装しているわけではありません。 そのため、開発ストレージに Azurite を使用すると、予期しないエラーや動作が発生する可能性があります。
* まれに、ツリーのフォーカスがクイック アクセスから移動しなくなることがあります。 フォーカスを移動できるようにするには、[すべて更新] をクリックします。
* NodeJS のバグが原因で、OneDrive フォルダーからのアップロードが機能しません。 バグは修正されましたが、Electron にまだ統合されていません。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、[こちら](https://github.com/Azure/azure-storage-node/issues/317)で説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* 現在、Azure Stack ではファイル共有をサポートしていませんが、ファイル共有ノードがアタッチされた Azure Stack ストレージ アカウントの下に表示され続けます。
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux ユーザーは、[.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) をインストールする必要があります。
* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>バージョン 1.4.2
09/24/2018

### <a name="hotfixes"></a>修正プログラム
* Azure Resource Management の API バージョンを 2018-07-01 に更新して、新しい Azure Storage アカウントの種類に対するサポートを追加します。 [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>新規
* SAS 接続やエミュレーターなどの外部リソースのアタッチが大幅に改善されました。 ここでは、次の操作を実行できます。
   * アタッチしているリソースの表示名をカスタマイズします。 [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * 異なるポートを使用して複数のローカル エミュレーターにアタッチします。 [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * クイック アクセスにアタッチされているリソースを追加します。 [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer は論理的な削除をサポートするようになりました。 次のようにすることができます。
   * ストレージ アカウントの BLOB コンテナー ノードを右クリックして、論理的な削除のポリシーを構成します。
   * ナビゲーション バーの横にあるドロップダウン リストで "アクティブな BLOB と削除された BLOB" を選択して、BLOB エディターで論理的に削除された BLOB を表示します。
   * 論理的に削除された BLOB の削除を取り消します。

### <a name="fixes"></a>修正
* Premium Storage アカウントでは CORS をサポートしていないため、"CORS 設定の構成" アクションを Premium Storage アカウントで使用できなくなりました。 [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* SAS のアタッチされたサービスに Shared Access Signature プロパティが追加されました。 [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* クイック アクセスにピン留めされた BLOB および GPV2 ストレージ アカウントで、"既定のアクセス層の設定" アクションを使用できるようになりました。 [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* 場合によっては、Storage Explorer は Classic Storage アカウントを表示するのに失敗します。 [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>既知の問題
* Azure Storage エミュレーターや Azurite などのエミュレーターを使用している場合、エミュレーターに既定のポートでの接続をリッスンさせる必要があります。 そうしないと、Storage Explorer がそれらに接続できません。
* VS for Mac を使用しており、カスタム AAD 構成を作成したことがある場合、サインインできないことがあります。 この問題を回避するには、~/.IdentityService/AadConfigurations の内容を削除します。 これを行ってもブロックが解除されない場合は、[この問題](https://github.com/Microsoft/AzureStorageExplorer/issues/97)についてコメントをお寄せください。
* Azurite は、すべての Storage API を完全に実装しているわけではありません。 そのため、開発ストレージに Azurite を使用すると、予期しないエラーや動作が発生する可能性があります。
* まれに、ツリーのフォーカスがクイック アクセスから移動しなくなることがあります。 フォーカスを移動できるようにするには、[すべて更新] をクリックします。
* NodeJS のバグが原因で、OneDrive フォルダーからのアップロードが機能しません。 バグは修正されましたが、Electron にまだ統合されていません。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、[こちら](https://github.com/Azure/azure-storage-node/issues/317)で説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* 現在、Azure Stack ではファイル共有をサポートしていませんが、ファイル共有ノードがアタッチされた Azure Stack ストレージ アカウントの下に表示され続けます。
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux ユーザーは、[.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) をインストールする必要があります。
* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>バージョン 1.4.1
2018/08/28

### <a name="hotfixes"></a>修正プログラム
* 最初の起動時に、Storage Explorer は機密データの暗号化に使用されるキーを生成できませんでした。 これにより、クイック アクセスおよびリソースのアタッチのときに問題が発生しました。 [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* ホーム テナント用には MFA が必要なくても、他のテナントには必要なアカウントの場合、Storage Explorer ではサブスクリプションを一覧表示できませんでした。 現在は、そのようなアカウントでサインインした後、Storage Explorer は資格情報を再入力して MFA を実行するように要求します。 [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Storage Explorer は、Azure Germany および Azure 米国政府機関からのリソースをアタッチできませんでした。 [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* メール アドレスが同じ 2 つのアカウントにサインインした場合、Storage Explorer はリソースをツリー ビューで表示できないことがありました。 [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* 低速の Windows コンピューターでは、スプラッシュ スクリーンの表示に長い時間がかかる場合がありました。 [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* アタッチされたアカウントまたはサービスの場合でも、接続ダイアログが表示されました。 [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>新規
* SAS 接続やエミュレーターなどの外部リソースのアタッチが大幅に改善されました。 ここでは、次の操作を実行できます。
   * アタッチしているリソースの表示名をカスタマイズします。 [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * 異なるポートを使用して複数のローカル エミュレーターにアタッチします。 [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * クイック アクセスにアタッチされているリソースを追加します。 [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer は論理的な削除をサポートするようになりました。 次のようにすることができます。
   * ストレージ アカウントの BLOB コンテナー ノードを右クリックして、論理的な削除のポリシーを構成します。
   * ナビゲーション バーの横にあるドロップダウン リストで "アクティブな BLOB と削除された BLOB" を選択して、BLOB エディターで論理的に削除された BLOB を表示します。
   * 論理的に削除された BLOB の削除を取り消します。

### <a name="fixes"></a>修正
* Premium Storage アカウントでは CORS をサポートしていないため、"CORS 設定の構成" アクションを Premium Storage アカウントで使用できなくなりました。 [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* SAS のアタッチされたサービスに Shared Access Signature プロパティが追加されました。 [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* クイック アクセスにピン留めされた BLOB および GPV2 ストレージ アカウントで、"既定のアクセス層の設定" アクションを使用できるようになりました。 [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* 場合によっては、Storage Explorer は Classic Storage アカウントを表示するのに失敗します。 [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>既知の問題
* Azure Storage エミュレーターや Azurite などのエミュレーターを使用している場合、エミュレーターに既定のポートでの接続をリッスンさせる必要があります。 そうしないと、Storage Explorer がそれらに接続できません。
* VS for Mac を使用しており、カスタム AAD 構成を作成したことがある場合、サインインできないことがあります。 この問題を回避するには、~/.IdentityService/AadConfigurations の内容を削除します。 これを行ってもブロックが解除されない場合は、[この問題](https://github.com/Microsoft/AzureStorageExplorer/issues/97)についてコメントをお寄せください。
* Azurite は、すべての Storage API を完全に実装しているわけではありません。 そのため、開発ストレージに Azurite を使用すると、予期しないエラーや動作が発生する可能性があります。
* まれに、ツリーのフォーカスがクイック アクセスから移動しなくなることがあります。 フォーカスを移動できるようにするには、[すべて更新] をクリックします。
* NodeJS のバグが原因で、OneDrive フォルダーからのアップロードが機能しません。 バグは修正されましたが、Electron にまだ統合されていません。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、[こちら](https://github.com/Azure/azure-storage-node/issues/317)で説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* 現在、Azure Stack ではファイル共有をサポートしていませんが、ファイル共有ノードがアタッチされた Azure Stack ストレージ アカウントの下に表示され続けます。
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux ユーザーは、[.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) をインストールする必要があります。
* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>バージョン 1.3.0
2018/07/09

### <a name="new"></a>新規
* 静的な Web サイトで使用される $web コンテナーへのアクセスがサポートされるようになりました。 これにより、Web サイトで使用されるファイルとフォルダーのアップロードと管理を容易に実行できます。 [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* macOS のアプリ バーが再構成されています。 変更には、[ファイル] メニュー、いくつかのショートカット キーの変更、およびアプリ メニューのさまざまな新しいコマンドが含まれています。 [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Azure US Government にサインインするための証明機関のエンドポイントが https://login.microsoftonline.us/ に変更されています。
* アクセシビリティ:スクリーン リーダーがアクティブであるとき、キーボード ナビゲーションが、項目を右側に表示するために使用されるテーブルでも機能するようになりました。 方向キーを使用して行と列を移動し、Enter キーで既定のアクションを呼び出し、コンテキスト メニュー キーで項目のコンテキスト メニューを開き、Shift または Control キーで複数選択を行うことができます。 [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>修正
*  一部のコンピュータで、子プロセスの起動に時間がかかっていました。 これが発生すると、"子プロセスを適切なタイミングで開始できませんでした" エラーが表示されました。 現在、子プロセスに割り当てられる開始時間が、20 秒から90 秒に引き上げられています。 この問題による影響がまだある場合は、リンクされた GitHub 問題でコメントしてください。 [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* 読み取りアクセス許可がない SAS を使用した場合、大きな BLOB をアップロードすることはできませんでした。 このシナリオでも機能するように、アップロードのロジックが変更されています。 [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* コンテナーのパブリック アクセス レベルを設定すると、すべてのアクセス ポリシーが削除されました (逆の場合も同様です)。 パブリック アクセス レベルとアクセス ポリシーのどちらを設定した場合でも、両方が保持されるようになりました。 [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* [プロパティ] ダイアログで "AccessTierChangeTime" が切り捨てられていました。 この問題は修正されています。 [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* [新しいディレクトリの作成] ダイアログで "Microsoft Azure Storage Explorer -" プレフィックスが欠落していました。 この問題は修正されています。 [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* アクセシビリティ:ボイス オーバーを使用したときに [エンティティの追加] ダイアログに簡単に移動できませんでした。 改良が加えられています。 [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* アクセシビリティ:[アクション] ウィンドウと [プロパティ] ウィンドウの折りたたみ/展開ボタンの背景色が、ハイコントラスト黒テーマ表示での類似する UI コントロールと一貫性がありませんでした。 色が変更されています。 [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* アクセシビリティ:ハイコントラスト黒テーマ表示で、[プロパティ] ダイアログの [X] ボタンのフォーカス スタイルが見えませんでした。 この問題は修正されています。 [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* アクセシビリティ:[アクション] タブと [プロパティ] タブで、複数の aria 値が欠落していたため、スクリーン リーダーの質が低下していました。 欠落していた aria 値が追加されています。 [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* アクセシビリティ:左側の折りたたまれたツリー ノードに、aria 拡張値 false が指定されませんでした。 この問題は修正されています。 [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>既知の問題
* BLOB コンテナーなど、SAS URI を使用してアタッチされているリソースからデタッチすると、他のアタッチが正しく表示されないエラーが発生することがあります。 この問題は、グループ ノードを更新するだけで回避できます。 詳しくは、[この問題](https://github.com/Microsoft/AzureStorageExplorer/issues/537)をご覧ください。
* VS for Mac を使用しており、カスタム AAD 構成を作成したことがある場合、サインインできないことがあります。 この問題を回避するには、~/.IdentityService/AadConfigurations の内容を削除します。 これを行ってもブロックが解除されない場合は、[この問題](https://github.com/Microsoft/AzureStorageExplorer/issues/97)についてコメントをお寄せください。
* Azurite は、すべての Storage API を完全に実装しているわけではありません。 そのため、開発ストレージに Azurite を使用すると、予期しないエラーや動作が発生する可能性があります。
* まれに、ツリーのフォーカスがクイック アクセスから移動しなくなることがあります。 フォーカスを移動できるようにするには、[すべて更新] をクリックします。
* NodeJS のバグが原因で、OneDrive フォルダーからのアップロードが機能しません。 バグは修正されましたが、Electron にまだ統合されていません。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、[こちら](https://github.com/Azure/azure-storage-node/issues/317)で説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* Azure Stack は次の機能をサポートしておらず、Azure Stack の使用中にそれらの機能を使用しようとすると予期しないエラーが発生することがあります。
   * ファイル共有
   * アクセス層
   * 論理的な削除
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux ユーザーは、[.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) をインストールする必要があります。
* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>バージョン 1.2.0
2018/06/12

### <a name="new"></a>新規
* Storage Explorer がテナントのサブセットについてのみ、サブスクリプションの読み込みに失敗した場合は、正常に読み込まれたサブスクリプションと、失敗したテナントに関するエラー メッセージが表示されます。 [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* Windows では、更新プログラムが提供された際に、[Update on Close]\(終了時に更新\) を選択できるようになりました。 このオプションを選択した場合、更新プログラムのインストーラーは Storage Explorer を終了した後に実行されます。 [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* ファイル共有スナップショットを表示しているときに、ファイル共有エディターのコンテキスト メニューに [スナップショットの復元] が表示されるようになりました。[#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* [キューのクリア] ボタンが常に有効化されるようになりました。[#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* ADFS Azure Stack へのサインインのサポートが再度有効化されました。 Azure Stack バージョン 1804 以上が必要です。 [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>修正
* 同じストレージ アカウント内の別のファイル共有のプレフィックスを名前に持つファイル共有のスナップショットを表示すると、もう一方のファイル共有のスナップショットも一覧に表示されていました。 この問題は修正されています。 [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* SAS を介してアタッチされている場合、ファイル共有スナップショットからファイルを復元すると、エラーが発生していました。 この問題は修正されています。 [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* BLOB のスナップショットを表示しているときに、ベース BLOB と 1 つのスナップショットが選択されると、スナップショットの昇格アクションが有効化されていました。 このアクションは、1 つのスナップショットが選択された場合にのみ、有効化されるようになりました。 [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* 1 つのジョブ (BLOB のダウンロードなど) が開始され、その後失敗しても、同じ種類の別のジョブが開始されるまで、ジョブは自動的に再試行されませんでした。 現在は、キュー内のジョブの数に関係なく、すべてのジョブが自動的に再試行されるようになりました。
* GPV2 および Blob Storage アカウントで新規作成された BLOB コンテナーに対して開かれるエディターに、アクセス レベル列が表示されませんでした。 この問題は修正されています。 [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Storage アカウントまたは BLOB コンテナーが SAS を介してアタッチされている場合に、アクセス レベル列が表示されないことがありました。 現在、この列は常に表示されるようになりました (ただし、アクセス レベルが設定されていない場合は、空の値が使用されます)。 [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* 新たにアップロードされたブロック BLOB のアクセス レベルの設定が無効化されていました。 この問題は修正されています。 [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* [タブを開いたままにする] ボタンがキーボードを使って呼び出された場合に、キーボードのフォーカスが失われていました。 現在は、開いたままにされたタブへとフォーカスが移動するようになりました。 [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* クエリ ビルダーのクエリについて、VoiceOver で現在の演算子のわかりやすい説明が提供されませんでした。 現在では、説明が分かりやすくなっています。 [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* 各種エディターの改ページ リンクがわかりやすくありませんでした。 これは、わかりやすいように変更されました。 [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* [エンティティの追加] ダイアログ ボックスで、入力要素がどの列に属しているのかが VoiceOver によって伝えられませんでした。 今リリースでは、要素の説明に現在の列の名前が含まれるようになりました。 [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* オプション ボタンとチェック ボックスに、フォーカス時に表示される枠線がありませんでした。 この問題は修正されています。 [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>既知の問題
* Azure Storage エミュレーターや Azurite などのエミュレーターを使用している場合、エミュレーターに既定のポートでの接続をリッスンさせる必要があります。 そうしないと、Storage Explorer がそれらに接続できません。
* VS for Mac を使用しており、カスタム AAD 構成を作成したことがある場合、サインインできないことがあります。 この問題を回避するには、~/.IdentityService/AadConfigurations の内容を削除します。 これを行ってもブロックが解除されない場合は、[この問題](https://github.com/Microsoft/AzureStorageExplorer/issues/97)についてコメントをお寄せください。
* Azurite は、すべての Storage API を完全に実装しているわけではありません。 そのため、開発ストレージに Azurite を使用すると、予期しないエラーや動作が発生する可能性があります。
* まれに、ツリーのフォーカスがクイック アクセスから移動しなくなることがあります。 フォーカスを移動できるようにするには、[すべて更新] をクリックします。
* NodeJS のバグが原因で、OneDrive フォルダーからのアップロードが機能しません。 バグは修正されましたが、Electron にまだ統合されていません。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、[こちら](https://github.com/Azure/azure-storage-node/issues/317)で説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* 現在、Azure Stack ではファイル共有をサポートしていませんが、ファイル共有ノードがアタッチされた Azure Stack ストレージ アカウントの下に表示され続けます。
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux ユーザーは、[.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) をインストールする必要があります。
* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>バージョン 1.1.0
2018/05/09

### <a name="new"></a>新規
* Storage Explorer で Azurite を使用できるようになりました。 注: Azurite への接続は、既定の開発エンドポイントにハードコードされています。
* Storage Explorer で、BLOB 専用のストレージ アカウントと GPV2 ストレージ アカウントのアクセス レベルがサポートされるようになりました。 アクセス レベルの詳細については、[こちら](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)をご覧ください。
* SAS を生成するときに、開始時刻が不要になりました。

### <a name="fixes"></a>修正
* 米国政府機関のアカウントのサブスクリプションを取得できませんでした。 この問題は修正されています。 [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* アクセス ポリシーの有効期限が正しく保存されていませんでした。 この問題は修正されています。 [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* コンテナー内の項目の SAS URL を生成したときに、項目の名前が URL に追加されていませんでした。 この問題は修正されています。 [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* SAS の作成時に、過去の有効期限が既定値になることがありました。 これは、Storage Explorer で、前回使用された開始時刻と有効期限が既定値として使用されていたことが原因です。 現在は、SAS ダイアログを開くたびに、一連の新しい既定値が生成されます。 [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* ストレージ アカウント間でコピーを実行すると、24 時間の SAS が生成されます。 コピーが 24 時間以上続いた場合、コピーは失敗します。 期限切れの SAS が原因でコピーが失敗する可能性を低減するために、SAS の期間を 1 週間に延長しました。 [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* 一部のアクティビティで、[キャンセル] のクリックが機能しないことがありました。 この問題は修正されています。 [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* 一部のアクティビティで、転送速度が間違っていました。 この問題は修正されています。 [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* [View] メニューの [Previous] のスペルが間違っていました。 現在は正しいスペルになっています。 [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* Windows インストーラーの最後のページに [次へ] ボタンがありました。 これが [完了] ボタンに変更されました。 [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* HC Black テーマの使用時に、ダイアログのボタンのタブ フォーカスが表示されませんでした。 現在は表示されます。[#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* アクティビティ ログでアクションの "Auto-Resolve" の大文字小文字の表記が間違っていました。 現在は正しい表記になっています。 [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* テーブルからエンティティを削除するときに、確認を求めるダイアログにエラー アイコンが表示されていました。 このダイアログで警告アイコンが使用されるようになりました。 [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>既知の問題
* VS for Mac を使用しており、カスタム AAD 構成を作成したことがある場合、サインインできないことがあります。 この問題を回避するには、~/.IdentityService/AadConfigurations の内容を削除します。 これを行ってもブロックが解除されない場合は、[この問題](https://github.com/Microsoft/AzureStorageExplorer/issues/97)についてコメントをお寄せください。
* Azurite は、すべての Storage API を完全に実装しているわけではありません。 そのため、開発ストレージに Azurite を使用すると、予期しないエラーや動作が発生する可能性があります。
* まれに、ツリーのフォーカスがクイック アクセスから移動しなくなることがあります。 フォーカスを移動できるようにするには、[すべて更新] をクリックします。
* NodeJS のバグが原因で、OneDrive フォルダーからのアップロードが機能しません。 バグは修正されましたが、Electron にまだ統合されていません。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、[こちら](https://github.com/Azure/azure-storage-node/issues/317)で説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* 現在、Azure Stack ではファイル共有をサポートしていませんが、ファイル共有ノードがアタッチされた Azure Stack ストレージ アカウントの下に表示され続けます。
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux ユーザーは、[.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) をインストールする必要があります。
* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>バージョン 1.0.0
2018/04/16

### <a name="new"></a>新規
* 認証が強化され、Storage Explorer で Visual Studio 2017 と同じアカウント ストアを使用できるようになりました。 この機能を使用するには、アカウントに再ログインし、フィルター処理されたサブスクリプションを再設定する必要があります。
* AAD に対応した Azure Stack アカウントの場合、[Target Azure Stack]\(対象となる Azure Stack\) が有効になっていると、Storage Explorer が Azure Stack サブスクリプションを取得するようになりました。 カスタム ログイン環境を作成する必要がなくなりました。
* より迅速に操作できるように、ショートカットがいくつか追加されました。 これには、さまざまなパネルの切り替えやエディター間の移動などがあります。 詳細については、[表示] メニューを参照してください。
* Storage Explorer のフィードバックが GitHub で公開されるようになりました。 左下にあるフィードバック ボタンをクリックするか、[https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues) にアクセスすると、問題に関するページに移動できます。 自由に、提案、問題の報告、質問を行ったり、その他の形式のフィードバックを提供したりしてください。
* SSL 証明書の問題が発生していて、問題となっている証明書が見つからない場合に、コマンド ラインから `--ignore-certificate-errors` フラグを指定して Storage Explorer を起動できるようになりました。 このフラグを指定して起動すると、Storage Explorer は SSL 証明書のエラーを無視します。
* BLOB とファイル項目のコンテキスト メニューに「ダウンロード」オプションが追加されました。
* アクセシビリティとスクリーン リーダーのサポートが強化されました。 ユーザー補助機能を使用する場合、詳細については、[アクセシビリティのドキュメント](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility)を参照してください。
* Storage Explorer で Electron 1.8.3 を使用するようになりました。

### <a name="breaking-changes"></a>重大な変更
* Storage Explorer が新しい認証ライブラリに切り替わりました。 ライブラリの切り替えの一環として、アカウントに再ログインし、フィルター処理されたサブスクリプションを再設定する必要があります。
* 機密データを暗号化する方法が変更されました。 これにより、クイック アクセス項目を再度追加したり、接続したリソースを再接続したりすることが必要になる場合があります。

### <a name="fixes"></a>修正
* ユーザーがプロキシの背後にいる場合、グループの BLOB のアップロードやダウンロードが中断され、「解決できません」というエラー メッセージが表示されることがありました。 この問題は修正されています。
* ダイレクト リンクを使用する際にサインインが必要な場合に、[サインイン] プロンプトをクリックすると空白のダイアログが表示されていました。 この問題は修正されています。
* Linux では、GPU プロセスのクラッシュが原因で Storage Explorer が起動できない場合に、そのクラッシュについて通知され、"--disable-gpu" スイッチを使用するよう指示されるようになりました。その後、Storage Explorer はそのスイッチを有効にして自動的に再起動します。
* [アクセス ポリシー] ダイアログでは、無効なアクセス ポリシーを特定するのが困難でした。 見つけやすくするために、無効なアクセス ポリシーの ID が赤枠で囲まれるようになりました。
* アクティビティ ログには、アクティビティのさまざまな部分の間に大きな空白領域がある場合がありました。 この問題は修正されています。
* テーブル クエリ エディターで、タイムスタンプ句が無効な状態のまま、他の句を変更しようとすると、エディターがフリーズしていました。 他の句の変更が検出されると、タイムスタンプ句が前回の有効な状態に復元されるようになりました。
* ツリー ビューで検索クエリの入力を一時停止すると、検索が開始され、テキスト ボックスからフォーカスが移動していました。 Enter キーを押すか、検索開始ボタンをクリックして、明示的に検索を開始する必要があるようになりました。
* ファイル共有内のファイルを右クリックしたときに、[Get Shared Access Signature]\(Shared Access Signature の取得\) コマンドが無効になることがありました。 この問題は修正されています。
* 検索時にフォーカスのあるリソース ツリー ノードがフィルターで除外されると、Tab キーでリソース ツリーに移動することや、方向キーを使用してリソース ツリー内を移動することができませんでした。 フォーカスのあるリソース ツリー ノードが非表示になると、リソース ツリーの最初のノードに自動的にフォーカスが移動するようになりました。
* 編集ツール バーに余分な区切り文字が表示されることがありました。 この問題は修正されています。
* 階層リンク テキスト ボックスがオーバーフローすることがありました。 この問題は修正されています。
* 一度に多数のファイルをアップロードすると、BLOB およびファイル共有エディターが繰り返し最新の情報に更新されることがありました。 この問題は修正されています。
* "フォルダーの統計" 機能は、ファイル共有スナップショット管理ビューでは使い道がありませんでした。 そのため、無効になりました。
* Linux では、[ファイル] メニューが表示されませんでした。 この問題は修正されています。
* ファイル共有にフォルダーをアップロードすると、既定ではフォルダーの内容のみがアップロードされていました。 既定の動作では、フォルダーの内容がファイル共有内の一致するフォルダーにアップロードされるようになりました。
* いくつかのダイアログでボタンの順序が逆になっていました。 この問題は修正されています。
* セキュリティ関連のさまざまな修正が行われました。

### <a name="known-issues"></a>既知の問題
* まれに、ツリーのフォーカスがクイック アクセスから移動しなくなることがあります。 フォーカスを移動できるようにするには、[すべて更新] をクリックします。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、こちらで説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* 現在、Azure Stack ではファイル共有をサポートしていませんが、ファイル共有ノードがアタッチされた Azure Stack ストレージ アカウントの下に表示され続けます。
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux ユーザーは、[.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) をインストールする必要があります。
* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>バージョン 0.9.6
2018/02/28

### <a name="fixes"></a>修正
* 予期される BLOB/ファイルがエディターに表示されない問題がありました。 この問題は修正されています。
* スナップショット ビューを切り替えた際に、項目が正しく表示されない問題がありました。 この問題は修正されています。

### <a name="known-issues"></a>既知の問題
* Storage Explorer では ADFS アカウントがサポートされません。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、[こちら](https://github.com/Azure/azure-storage-node/issues/317)で説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* サブスクリプションをフィルターするために資格情報の再入力が必要であると、アカウント設定パネルに表示されることがあります。
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* 現在、Azure Stack ではファイル共有をサポートしていませんが、ファイル共有ノードがアタッチされた Azure Stack ストレージ アカウントの下に表示され続けます。
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>バージョン 0.9.5
2018/02/06

### <a name="new"></a>新規

* ファイル共有のスナップショットのサポート:
    * ファイル共有のスナップショットを作成、管理できます。
    * ファイル共有のスナップショット間でビューを簡単に切り替えながら、内容を参照できます。
    * 以前のバージョンのファイルを復元できます。
* Azure Data Lake Store のプレビュー サポート:
    * 複数のアカウントで ADLS リソースに接続できます。
    * ADL URI を使用して ADLS リソースに接続し、リソースを共有できます。
    * 基本的なファイル/フォルダー操作を再帰的を実行できます。
    * 個々のフォルダーをクイック アクセスにピン留めできます。
    * フォルダーの統計を表示できます。

### <a name="fixes"></a>修正
* 起動パフォーマンスの改善。
* さまざまなバグの修正。

### <a name="known-issues"></a>既知の問題
* Storage Explorer では ADFS アカウントがサポートされません。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、こちらで説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* サブスクリプションをフィルターするために資格情報の再入力が必要であると、アカウント設定パネルに表示されることがあります。
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* 現在、Azure Stack ではファイル共有をサポートしていませんが、ファイル共有ノードがアタッチされた Azure Stack ストレージ アカウントの下に表示され続けます。
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>バージョン 0.9.4 および 0.9.3
2018/01/21

### <a name="new"></a>新規
* 既存の Storage Explorer ウィンドウは以下の場合に再利用されます。
    * Storage Explorer で生成された直接リンクを開く。
    * Storage Explorer をポータルから開く。
    * Storage Explorer を Azure Storage VS Code 拡張機能 (近日公開予定) から開く。
* Storage Explorer 内から新しい Storage Explorer ウィンドウを開く機能が追加されました。
    * Windows の場合は、[ファイル] メニューとタスク バーのコンテキスト メニューに [新しいウィンドウ] オプションがあります。
    * Mac の場合は、アプリケーション メニューに [新規ウィンドウ] オプションがあります。

### <a name="fixes"></a>修正
* セキュリティの問題を修正しました。 できるだけ早く 0.9.4 にアップグレードしてください。
* 古いアクティビティが適切にクリーンアップされませんでした。 これは長時間実行ジョブのパフォーマンスに影響していました。 現在は適切にクリーンアップされるようになっています。
* 大量のファイルとディレクトリに関連するアクションによって、Storage Explorer がフリーズすることがありました。 Azure へのファイル共有の要求は、システム リソースの使用を制限するように調節されるようになりました。

### <a name="known-issues"></a>既知の問題
* Storage Explorer では ADFS アカウントがサポートされません。
* "Explorer の表示" と "アカウント管理の表示" のショートカット キーはそれぞれ、Ctrl/Cmd + Shift + E キーと Ctrl/Cmd + Shift + A キーです。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、こちらで説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* サブスクリプションをフィルターするために資格情報の再入力が必要であると、アカウント設定パネルに表示されることがあります。
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* 現在、Azure Stack ではファイル共有をサポートしていませんが、ファイル共有ノードがアタッチされた Azure Stack ストレージ アカウントの下に表示され続けます。
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>バージョン 0.9.2
11/01/2017

### <a name="hotfixes"></a>修正プログラム
* ローカル タイム ゾーンによっては、テーブル エンティティの Edm.DateTime 値を編集する時に、予期しないデータの変更が発生する可能性がありました。 エディターは現在プレーンテキスト ボックスを使用しており、Edm.DateTime 値の制御は正確で一貫性があります。
* 名前とキーを付けると BLOB のグループのアップロード/ダウンロードが起動しませんでした。 この問題は修正されています。
* これまで Storage Explorer では、1 つまたは複数のアカウントのサブスクリプションが選択された場合に、古いアカウントの再認証のダイアログを表示するだけでした。 現在では Storage Explorer は、アカウントが完全に除外された場合でもダイアログを表示します。
* Azure US Government のエンドポイントのドメインに誤りがありました。 この問題は修正されています。
* アカウントの管理パネルの適用ボタンをクリックしにくい場合がありました。 この問題はもう発生しません。

### <a name="new"></a>新規
* Azure Cosmos DB のプレビューのサポート:
    * [オンライン ドキュメント](./cosmos-db/storage-explorer.md)
    * データベースとコレクションの作成
    * データの操作
    * ドキュメントの照会、作成、または削除
    * ストアド プロシージャ、ユーザー定義関数、またはトリガーの更新
    * 接続文字列を使用したデータベース接続と管理
* 多数の小さな BLOB をアップロード/ダウンロードするときのパフォーマンスが向上しました。
* "すべて再試行" アクションが追加されました (BLOB アップロード グループまたは BLOB ダウンロード グループでのエラー発生時)。
* Storage Explorer で BLOB をアップロード/ダウンロード中、ネットワーク接続が失われたことが検出されると、イテレーションが一時停止します。 ネットワーク接続が再確立された時点で、イテレーションを再開できます。
* コンテキスト メニューから、タブに対して "すべて閉じる"、"その他を閉じる"、"閉じる" 操作を行うことができるようになりました。
* Storage Explorer で、ネイティブ ダイアログとネイティブ コンテキスト メニューを利用できます。
* Storage Explorer が、さらにアクセスしやすくなりました。 機能強化は次のとおりです。
    * スクリーン リーダー (Windows の NVDA、Mac の VoiceOver) のサポートの強化
    * ハイ コントラスト テーマの向上
    * キーボードの Tab キーを使った移動とキーボード フォーカスの修正

### <a name="fixes"></a>修正
* 無効な Windows ファイル名の BLOB を開こうとしたとき、またはダウンロードしようとしたときに、操作が失敗しました。 Storage Explorer によって BLOB 名が無効かどうかが検出され、エンコードするか、BLOB をスキップするかが尋ねられます。 また、Storage Explorer によって、ファイル名がエンコードされている可能性があるかどうかも検出され、アップロードの前にデコードするかどうかを尋ねられます。
* BLOB アップロード中、ターゲット BLOB コンテナーのエディターが正しく更新されないことがありました。 この問題は修正されています。
* 接続文字列と SAS URI の形式のいくつかについて、サポートが後退していました。 既知の問題はすべて解決されていますが、さらに問題が発生した場合は、フィードバックをお送りください。
* 更新の通知が、0.9.0 の一部のユーザーに送信されませんでした。 この問題は修正されました。バグの影響を受けた方は、Storage Explorer の最新バージョンを[こちら](https://azure.microsoft.com/features/storage-explorer/)からダウンロードしてください。

### <a name="known-issues"></a>既知の問題
* Storage Explorer では ADFS アカウントがサポートされません。
* "Explorer の表示" と "アカウント管理の表示" のショートカット キーはそれぞれ、Ctrl/Cmd + Shift + E キーと Ctrl/Cmd + Shift + A キーです。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、こちらで説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* サブスクリプションをフィルターするために資格情報の再入力が必要であると、アカウント設定パネルに表示されることがあります。
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* 現在、Azure Stack ではファイル共有をサポートしていませんが、ファイル共有ノードがアタッチされた Azure Stack ストレージ アカウントの下に表示され続けます。
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>バージョン 0.9.1 および 0.9.0
10/20/2017
### <a name="new"></a>新規
* Azure Cosmos DB のプレビューのサポート:
    * [オンライン ドキュメント](./cosmos-db/storage-explorer.md)
    * データベースとコレクションの作成
    * データの操作
    * ドキュメントの照会、作成、または削除
    * ストアド プロシージャ、ユーザー定義関数、またはトリガーの更新
    * 接続文字列を使用したデータベース接続と管理
* 多数の小さな BLOB をアップロード/ダウンロードするときのパフォーマンスが向上しました。
* "すべて再試行" アクションが追加されました (BLOB アップロード グループまたは BLOB ダウンロード グループでのエラー発生時)。
* Storage Explorer で BLOB をアップロード/ダウンロード中、ネットワーク接続が失われたことが検出されると、イテレーションが一時停止します。 ネットワーク接続が再確立された時点で、イテレーションを再開できます。
* コンテキスト メニューから、タブに対して "すべて閉じる"、"その他を閉じる"、"閉じる" 操作を行うことができるようになりました。
* Storage Explorer で、ネイティブ ダイアログとネイティブ コンテキスト メニューを利用できます。
* Storage Explorer が、さらにアクセスしやすくなりました。 機能強化は次のとおりです。
    * スクリーン リーダー (Windows の NVDA、Mac の VoiceOver) のサポートの強化
    * ハイ コントラスト テーマの向上
    * キーボードの Tab キーを使った移動とキーボード フォーカスの修正

### <a name="fixes"></a>修正
* 無効な Windows ファイル名の BLOB を開こうとしたとき、またはダウンロードしようとしたときに、操作が失敗しました。 Storage Explorer によって BLOB 名が無効かどうかが検出され、エンコードするか、BLOB をスキップするかが尋ねられます。 また、Storage Explorer によって、ファイル名がエンコードされている可能性があるかどうかも検出され、アップロードの前にデコードするかどうかを尋ねられます。
* BLOB アップロード中、ターゲット BLOB コンテナーのエディターが正しく更新されないことがありました。 この問題は修正されています。
* 接続文字列と SAS URI の形式のいくつかについて、サポートが後退していました。 既知の問題はすべて解決されていますが、さらに問題が発生した場合は、フィードバックをお送りください。
* 更新の通知が、0.9.0 の一部のユーザーに送信されませんでした。 この問題は修正されました。バグの影響を受けた方は、Storage Explorer の最新バージョンを[こちら](https://azure.microsoft.com/features/storage-explorer/)からダウンロードしてください

### <a name="known-issues"></a>既知の問題
* Storage Explorer では ADFS アカウントがサポートされません。
* "Explorer の表示" と "アカウント管理の表示" のショートカット キーはそれぞれ、Ctrl/Cmd + Shift + E キーと Ctrl/Cmd + Shift + A キーです。
* Azure Stack を対象にしている場合、一部のファイルについては、追加 BLOB としてアップロードできない可能性があります。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、こちらで説明したフィルターのキャンセル回避策を使用しているためです。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* サブスクリプションをフィルターするために資格情報の再入力が必要であると、アカウント設定パネルに表示されることがあります。
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* 現在、Azure Stack ではファイル共有をサポートしていませんが、ファイル共有ノードがアタッチされた Azure Stack ストレージ アカウントの下に表示され続けます。
* Storage Explorer で使用されている Electron シェルには、一部の GPU (グラフィックス処理装置) ハードウェア アクセラレータで問題が発生します。 Storage Explorer に空白 (空) のメイン ウィンドウが表示される場合は、コマンド ラインから Storage Explorer を起動し、`--disable-gpu` スイッチを追加して、GPU アクセラレータを無効にしてみてください:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>バージョン 0.8.16
8/21/2017

### <a name="new"></a>新規
* 変更が検出された場合は、BLOB を開いたときに、ダウンロードしたファイルをアップロードするようストレージ エクスプ ローラーから求められます。
* 改善された Azure Stack サインイン エクスペリエンス
* 同時に多数の小さなファイルをアップロード/ダウンロードしたときのパフォーマンスの向上


### <a name="fixes"></a>修正
* BLOB の種類によっては、アップロード競合中に "置換" を選択すると、アップロードが再開されることがあります。
* バージョン 0.8.15 では、アップロードが 99% 完了した時点で停止する場合があります。
* ファイル共有にファイルをアップロードする場合、まだ存在しないディレクトリへのアップロードを選択すると、アップロードは失敗します。
* ストレージ エクスプ ローラーで共有アクセス署名とテーブル クエリに対するタイムスタンプが正しく生成されませんでした。


### <a name="known-issues"></a>既知の問題
* 名前とキー接続文字列は現在使用できません。 この問題は次のリリースで修正される予定です。 それまでは、名前とキーの添付を使用できます。
* 無効な Windows ファイル名のファイルを開こうとすると、ダウンロード中にファイルが見つからないというエラーが発生します。
* タスクの [キャンセル] をクリックすると、そのタスクのキャンセルに少し時間がかかる場合があります。 これは、Azure Storage ノード ライブラリの制限事項です。
* BLOB のアップロードが完了したら、アップロードを開始するタブが更新されます。 これは以前の動作から変更されており、選択しているコンテナーのルートに戻ることになります。
* 誤った PIN/スマートカードの証明書を選択した場合、その記録をストレージ エクスプローラーから消すためには、再起動する必要があります
* サブスクリプションをフィルターするために資格情報の再入力が必要であると、アカウント設定パネルに表示されることがあります。
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* 現在、Azure Stack ではファイル共有をサポートしていませんが、ファイル共有ノードがアタッチされた Azure Stack ストレージ アカウントの下に表示され続けます。
* Ubuntu 14.04 のユーザーの場合、GCC が最新版であることを確認する必要があります。これは、次のコマンドを実行し、コンピューターを再起動して行います。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 のユーザーの場合、GConf をインストールする必要があります。次のコマンドを実行し、マシンを再起動して、この操作を行うことができます。

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Version 0.8.14
06/22/2017

### <a name="new"></a>新規

* いくつかの重要なセキュリティ アップデートを利用するために、Electron バージョンを 1.7.2 に更新しました
* ヘルプ メニューからオンラインのトラブルシューティング ガイドにすばやくアクセスできるようになりました
* Storage Explorer トラブルシューティング [ガイド][2]
* Azure Stack サブスクリプションへの接続に関する[指示][3]

### <a name="known-issues"></a>既知の問題

* フォルダーの削除の確認ダイアログ上のボタンは、Linux のマウス クリックに登録されません。 Enter キーを使用して回避することができます
* 誤った PIN/スマートカードの証明書を選択した場合、ストレージ エクスプローラーがその決定を忘れるように、再起動する必要があります
* BLOB またはファイルのグループを 3 つ以上同時にアップロードすると、エラーが発生する場合があります
* サブスクリプションをフィルターするために、資格情報の再入力が必要であることがアカウント設定パネルに表示されることがあります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* 現在、Azure Stack ではファイル共有をサポートしていませんが、ファイル共有ノードがアタッチされた Azure Stack ストレージ アカウントの下に表示され続けます。
* Ubuntu 14.04 のインストールには、更新またはアップグレードされた gcc バージョンが必要です。アップグレードの手順は次のとおりです。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>バージョン 0.8.13
05/12/2017

#### <a name="new"></a>新規

* Storage Explorer トラブルシューティング [ガイド][2]
* Azure Stack サブスクリプションへの接続に関する[指示][3]

#### <a name="fixes"></a>修正

* 修正:ファイルのアップロードは、メモリ不足エラーを発生させる高い可能性がありました
* 修正:PIN/スマートカードでサインインできるようになりました
* 修正済み:[ポータルで開く] が、Azure China 21Vianet、Azure Germany、Azure US Government、Azure Stack で機能するようになりました
* 修正済み:フォルダーを BLOB コンテナーにアップロードしているときに、"無効な操作" エラーが発生する場合があります
* 修正:スナップショットを管理しているときに、[すべて選択] が無効になっていました
* 修正:ベース BLOB のメタデータは、そのスナップショットのプロパティを表示した後に上書きされる可能性があります

#### <a name="known-issues"></a>既知の問題

* 誤った PIN/スマートカードの証明書を選択した場合、ストレージ エクスプローラーがその決定を忘れるように、再起動する必要があります
* 拡大縮小を行っているときに、ズーム レベルが既定のレベルにすぐにリセットされる可能性があります
* BLOB またはファイルのグループを 3 つ以上同時にアップロードすると、エラーが発生する場合があります
* サブスクリプションをフィルターするために、資格情報の再入力が必要であることがアカウント設定パネルに表示されることがあります
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* 現在、Azure Stack ではファイル共有をサポートしていませんが、ファイル共有ノードがアタッチされた Azure Stack ストレージ アカウントの下に表示され続けます。
* Ubuntu 14.04 のインストールには、更新またはアップグレードされた gcc バージョンが必要です。アップグレードの手順は次のとおりです。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>バージョン 0.8.12 および 0.8.11 および 0.8.10
04/07/2017

#### <a name="new"></a>新規

* 更新通知から更新プログラムをインストールすると、ストレージ エクスプローラーは自動的に閉じます
* インプレース クイック アクセスでは、頻繁にアクセスされるリソースを操作するために、拡張されたエクスペリエンスを提供します
* BLOB コンテナー エディターで、リース BLOB が属している仮想マシンを確認できるようになりました
* 左側のパネルを折りたためるようになりました
* 検出がダウンロードと同時に実行されるようになりました
* BLOB コンテナー、ファイル共有、およびテーブル エディターの統計情報を使用して、リソースまたは選択範囲のサイズを確認します
* Azure Active Directory (AAD) を基にした Azure Stack アカウントにサインインできるようになりました。
* 32MB を超えるアーカイブ ファイルを Premium ストレージ アカウントにアップロードできるようになりました
* アクセシビリティ サポートの向上
* [編集] &gt; [SSL 証明書] &gt; [証明書のインポート] に移動して、信頼できる Base 64 encoded X.509 SSL 証明書を追加できるようになりました

#### <a name="fixes"></a>修正

* 修正: アカウントの資格情報を更新した後に、ツリー ビューが自動的に更新されないことがありました
* 修正: エミュレーター キューとテーブルに SAS を生成すると、無効な URL になる場合がありました
* 修正: プロキシが有効なときに、Premium Storage アカウントを展開できるようになりました
* 修正: 選択したアカウントが 1 または 0 個の場合、アカウント管理ページの適用ボタンが機能しませんでした
* 修正: 競合の解決を必要とする BLOB のアップロードに失敗する場合があります - 0.8.11 で修正済み
* 修正: 0.8.11 でフィードバックの送信が中断されました - 0.8.12 で修正済み

#### <a name="known-issues"></a>既知の問題

* 0\.8.10 にアップグレードした後に、すべての資格情報を更新する必要があります。
* 拡大縮小を行っているときに、ズーム レベルが既定のレベルにすぐにリセットされる可能性があります。
* BLOB またはファイルのグループを 3 つ以上同時にアップロードすると、エラーが発生する場合があります。
* サブスクリプションをフィルターするために、資格情報の再入力が必要であることがアカウント設定パネルに表示されることがあります。
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* 現在、Azure Stack ではファイル共有をサポートしていませんが、ファイル共有ノードがアタッチされた Azure Stack ストレージ アカウントの下に表示され続けます。
* Ubuntu 14.04 のインストールには、更新またはアップグレードされた gcc バージョンが必要です。アップグレードの手順は次のとおりです。

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>バージョン 0.8.9 および 0.8.8
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>新規

* ストレージ エクスプローラー 0.8.9 では、更新プログラムの最新バージョンを自動的にダウンロードします。
* 修正プログラム: ポータルで生成された SAS URL を使用して、ストレージ アカウントをアタッチすると、エラーになります。
* BLOB スナップショットの作成、管理、および昇格を行えるようになりました。
* Azure China 21Vianet、Azure Germany、Azure US Government のアカウントにサインインできるようになりました。
* ズーム レベルを変更できるようになりました。 [表示] メニューのオプションを使用して、拡大、縮小、およびズームのリセットを行います。
* Unicode 文字が、BLOB とファイルのユーザー メタデータでサポートされるようになりました。
* アクセシビリティが向上しました。
* 次のバージョンのリリース ノートは、更新通知から表示できます。 [ヘルプ] メニューから最新のリリース ノートを表示することもできます。

#### <a name="fixes"></a>修正

* 修正: バージョン番号が、Windows のコントロール パネルで正しく表示されるようになりました
* 修正: 検索の 50,000 ノードまでの制限がなくなりました
* 修正: 対象になるディレクトリが存在しない場合に、ファイル共有へのアップロードが回転し続けます
* 修正: 長時間のアップロードとダウンロードの安定性が向上しました

#### <a name="known-issues"></a>既知の問題

* 拡大縮小を行っているときに、ズーム レベルが既定のレベルにすぐにリセットされる可能性があります。
* クイック アクセスは、サブスクリプション ベースのアイテムでのみ動作します。 このリリースでは、ローカル リソースと、キーまたは SAS トークンによってアタッチされたリソースは、サポートされていません。
* リソースの数によっては、クイック アクセスで対象リソースに移動するまでに数秒かかる場合があります。
* BLOB またはファイルのグループを 3 つ以上同時にアップロードすると、エラーが発生する場合があります。

12/16/2016
### <a name="version-087"></a>バージョン 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>新規

* [アクティビティ] ウィンドウで更新、ダウンロード、またはコピー セッションの開始時に競合を解決する方法を選ぶことができます
* タブをポイントすると、ストレージ リソースの完全なパスが表示されます
* タブをクリックすると、左側のナビゲーション ウィンドウ内の場所と同期されます

#### <a name="fixes"></a>修正

* 修正:Storage Explorer は、Mac の信頼できるアプリになりました
* 修正:Ubuntu 14.04 が再びサポートされるようになりました
* 修正:サブスクリプションを読み込むときに [アカウントの追加] UI が点滅することがありました
* 修正:左側のナビゲーション ウィンドウにすべてのストレージ リソースが一覧表示されないことがありました
* 修正:操作ウィンドウに空の操作が表示されることがありました
* 修正:前回終了したセッションからウィンドウ サイズが維持されるようになりました
* 修正:コンテキスト メニューを使って同じリソースに対して複数のタブを開くことができるようになりました

#### <a name="known-issues"></a>既知の問題

* クイック アクセスは、サブスクリプション ベースのアイテムでのみ動作します。 このリリースでは、ローカル リソースと、キーまたは SAS トークンによってアタッチされたリソースは、サポートされていません
* リソースの数によっては、クイック アクセスで対象リソースに移動するまでに数秒かかる場合があります
* BLOB またはファイルのグループを 3 つ以上同時にアップロードすると、エラーが発生する場合があります
* 検索によって処理されたノードが約 50,000 を超えると、パフォーマンスが低下するか、またはハンドルされない例外が発生する可能性があります
* macOS でストレージ エクスプローラーを初めて使うとき、キーチェーンにアクセスするためのユーザーのアクセス許可を確認する複数のプロンプトが表示される可能性があります。 プロンプトが繰り返し表示されないように、[常に許可] を選ぶことをお勧めします

11/18/2016
### <a name="version-086"></a>バージョン 0.8.6

#### <a name="new"></a>新規

* 最もよく使うサービスを簡単にアクセスできるようにクイック アクセスにピン留めできます
* 複数のエディターを別のタブで開けるようになりました。 シングルクリックで一時的なタブが開き、ダブルクリックで永続的なタブが開きます。一時的なタブをクリックして永続的なタブにすることもできます
* アップロードとダウンロードでのパフォーマンスと安定性を大幅に向上させました (特に高速コンピューターでの大きいファイルの場合)
* 空の "仮想" フォルダーを BLOB コンテナーに作成できます
* 新しく拡張されたサブ文字列の検索で範囲指定の検索を再導入したため、検索には次の 2 つのオプションがあります。
    * グローバル検索 - 検索用語を検索テキストボックスに入力するだけです
    * 範囲指定の検索 - ノードの横にある虫眼鏡アイコンをクリックして、パスの末尾に検索用語を追加するか、右クリックして [ここから検索] を選択します
* さまざまなテーマを追加しました(明るい (既定値)、暗い、ハイコントラスト黒、ハイ コントラスト白)。 [編集] &gt; [テーマ] を選んで、テーマの設定を変更します
* BLOB とファイルのプロパティを変更できます
* エンコードされた (Base64) キュー メッセージとエンコードされていないキュー メッセージをサポートするようになりました
* Linux では、64 ビット OS が必要になりました。 このリリースでは、 64 ビット Ubuntu 16.04.1 LTS のみをサポートします
* ロゴを更新しました。

#### <a name="fixes"></a>修正

* 修正:画面がフリーズする問題
* 修正:強化されたセキュリティ
* 修正:アタッチされたアカウントが重複して表示されることがあります
* 修正:定義されていないコンテンツ タイプを持つ BLOB は、例外を生成する場合があります
* 修正:空のテーブルでクエリ パネルを開くことができませんでした
* 修正:検索でのさまざまなバグ
* 修正:[さらに読み込む] をクリックしたときに読み込まれるリソース数が 50 から 100 に増えました
* 修正:最初の実行で、アカウントがサインインされた場合、既定でアカウントにすべてのサブスクリプションを選択するようになりました

#### <a name="known-issues"></a>既知の問題

* ストレージ エクスプローラーのこのリリースは、Ubuntu 14.04 で実行されません
* 同じリソースに対して複数のタブを開くために、同じリソースを連続してクリックしないでください。 別のリソースをクリックし、元のリソースに戻ってクリックし、別のタブでもう一度開きます
* クイック アクセスは、サブスクリプション ベースのアイテムでのみ動作します。 このリリースでは、ローカル リソースと、キーまたは SAS トークンによってアタッチされたリソースは、サポートされていません
* リソースの数によっては、クイック アクセスで対象リソースに移動するまでに数秒かかる場合があります
* BLOB またはファイルのグループを 3 つ以上同時にアップロードすると、エラーが発生する場合があります
* 検索によって処理されたノードが約 50,000 を超えると、パフォーマンスが低下するか、またはハンドルされない例外が発生する可能性があります

10/03/2016
### <a name="version-085"></a>バージョン 0.8.5

#### <a name="new"></a>新規

* ポータルで生成された SAS キーを使用して、ストレージ アカウントやリソースにアタッチできるようになりました

#### <a name="fixes"></a>修正

* 修正: 検索中の競合状態は、ノードが展開不可能になることがあります
* 修正:アカウント名とキーでストレージ アカウントに接続している場合、"HTTP の使用" は動作しません
* 修正:SAS キー (特にポータルで生成されたキー) は、"末尾のスラッシュ" エラーを返します
* 修正: テーブルのインポートの問題
    * パーティション キーと行キーが逆順になる場合がありました
    * "null" のパーティション キーを読み取ることができません

#### <a name="known-issues"></a>既知の問題

* 検索によって処理されたノードが約 50,000 を超えると、パフォーマンスが低下する可能性があります
* Azure Stack は現在ファイルをサポートしないので、[ファイル] を展開しようとするとエラーが表示されます

09/12/2016
### <a name="version-084"></a>バージョン 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>新規

* リソースの共有と簡単なアクセスのため、ストレージ アカウントまたはコンテナー、キュー、テーブル、ファイル共有に直接リンクします (Windows と Mac OS でサポート)
* 検索ボックスから BLOB コンテナー、テーブル、キュー、ファイル共有、またはストレージ アカウントを検索します
* テーブル キュー ビルダーで句をグループ化できまるようになりました
* BLOB コンテナー、ファイル共有、テーブル、BLOB、BLOB フォルダー、ファイルおよびディレクトリの名前を変更したり、SAS がアタッチされたアカウントとコンテナー内からコピーまたは貼り付けを行います
* BLOB コンテナーとファイル共有の名前を変更してコピーすると、プロパティとメタデータを保持するようになりました

#### <a name="fixes"></a>修正

* 修正: ブール値またはバイナリ プロパティが含まれる場合は、テーブル エンティティを編集できません

#### <a name="known-issues"></a>既知の問題

* 検索によって処理されたノードが約 50,000 を超えると、パフォーマンスが低下する可能性があります

08/03/2016
### <a name="version-083"></a>バージョン 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>新規

* コンテナー、テーブル、ファイル共有の名前を変更します
* クエリ ビルダーのエクスペリエンスが向上しました
* クエリの保存および読み込み機能
* リソースの共有と簡単なアクセスのため、ストレージ アカウントまたはコンテナー、キュー、テーブル、ファイル共有に直接リンクします (Windows のみ、macOS のサポートは近日公開予定です)
* CORS ルールを管理および構成する機能

#### <a name="fixes"></a>修正

* 修正:Microsoft アカウントは、8 - 12 時間ごとに再認証が必要です

#### <a name="known-issues"></a>既知の問題

* UI がフリーズして表示されることがあります。ウィンドウを最大化すると、この問題を解決することができます
* macOS のインストールには管理者アクセス許可が必要な場合があります
* サブスクリプションをフィルターするために、資格情報の再入力が必要であることがアカウント設定パネルに表示されることがあります
* ファイル共有、BLOB コンテナー、およびテーブルの名前を変更しても、ファイル共有のクォータ、パブリック アクセス レベル、またはアクセス ポリシーなど、コンテナーのメタデータやその他のプロパティを保持することはありません
* BLOB の名前の変更で (個別または名前を変更する BLOB コンテナーの内部)、スナップショットが保持されません。 BLOB、ファイル、エンティティの他のすべてのプロパティとメタデータは、名前変更の間に保持されます。
* リソースのコピーまたは名前の変更は、SAS がアタッチされたアカウントで機能しません

07/07/2016
### <a name="version-082"></a>バージョン 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>新規

* ストレージ アカウントはサブスクリプションごとにグループ化されます。キーまたは SAS によってアタッチされた開発ストレージとリソースは、(ローカルと接続されている) ノードに表示されます
* [Azure アカウントの設定] パネルのアカウントからサインアウトします
* サインインを有効にして管理するように、プロキシ設定を構成します
* BLOB リースの作成と中断
* シングルクリックで、BLOB コンテナー、キュー、テーブル、およびファイルを開きます

#### <a name="fixes"></a>修正

* 修正: .NET または Java ライブラリで挿入されたキュー メッセージは、Base64 から適切にデコードされません
* 修正: $metrics テーブルは、Blob Storage アカウントに表示されません
* 修正: テーブル ノードはローカル (開発) ストレージで機能しません

#### <a name="known-issues"></a>既知の問題

* macOS のインストールには管理者アクセス許可が必要な場合があります

06/15/2016
### <a name="version-080"></a>バージョン 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>新規

* ファイル共有のサポート: ファイルとディレクトリ、SAS URI (作成および接続) の表示、アップロード、ダウンロード、コピー
* SAS URI またはアカウント キーでストレージに接続するためのユーザー エクスペリエンスが向上しました
* テーブル クエリの結果をエクスポートします
* テーブルの列の並べ替えとカスタマイズ
* メトリックスを有効にしたストレージ アカウントの $logs BLOB コンテナーと $metrics テーブルの表示
* 改善されたエクスポートとインポートの動作にプロパティの値の種類が含まれるようになりました

#### <a name="fixes"></a>修正

* 修正: 大きい BLOB のアップロードまたはダウンロードは、不完全なアップロードまたはダウンロードになります
* 修正: 数値文字列 ("1") を含むエンティティの編集、追加、またはインポートは、double に変換されます
* 修正:ローカル開発環境のテーブル ノードを展開できません

#### <a name="known-issues"></a>既知の問題

* $metrics テーブルが、Blob Storage アカウントに表示されません
* Base64 エンコーディングを使用してメッセージがエンコードされている場合、プログラムを使用して追加されたキュー メッセージが正しく表示されない可能性があります

05/17/2016
### <a name="version-07201605090"></a>バージョン 0.7.20160509.0

#### <a name="new"></a>新規

* アプリ クラッシュのエラー処理が改善されました

#### <a name="fixes"></a>修正

* サインインの資格情報が必要なときに、情報バーのメッセージが表示されないバグが修正されました

#### <a name="known-issues"></a>既知の問題

* テーブル:あいまいな数値 ("1"、"1.0" など) を含むプロパティがあるエンティティを追加、編集、またはインポートしている場合、ユーザーは `Edm.String` としてエンティティを送信しようとし、値は Edm.Double としてクライアント API によって戻されます

03/31/2016

### <a name="version-07201603250"></a>バージョン 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>新規

* テーブルのサポート: エンティティの表示、クエリ、エクスポート、インポート、および CRUD 操作
* キューのサポート: メッセージの表示、追加、デキュー
* ストレージ アカウントの SAS URI の生成
* SAS URI でストレージ アカウントに接続
* ストレージ エクスプローラーへの今後の更新プログラムの通知を更新します
* 外観を更新しました

#### <a name="fixes"></a>修正

* パフォーマンスと信頼性の向上

### <a name="known-issues-amp-mitigations"></a>既知の問題 &amp; 軽減策

* 大きい BLOB ファイルのダウンロードは、現在機能しません - Microsoft がこの問題に対処している間は、AzCopy を使用することをお勧めします
* ホーム フォルダーが見つからない、または書き込めない場合に、アカウントの資格情報が取得またはキャッシュされることはありません
* あいまいな数値 ("1"、"1.0" など) を含むプロパティがあるエンティティを追加、編集、またはインポートしている場合は、ユーザーは `Edm.String` としてエンティティを送信しようとし、値は Edm.Double としてクライアント API によって戻されます
* 複数行のレコードを含む CSV ファイルをインポートしていると、データはチョップされたり、スクランブルをかけられる場合があります

02/03/2016

### <a name="version-07201601291"></a>バージョン 0.7.20160129.1

#### <a name="fixes"></a>修正

* BLOB をアップロード、ダウンロード、コピーするときの全体的なパフォーマンスが改善されました

01/14/2016

### <a name="version-07201601050"></a>バージョン 0.7.20160105.0

#### <a name="new"></a>新規

* Linux Support (OSX へのパリティ機能)
* Shared Access Signatures (SAS) キーを持つ BLOB コンテナーを追加します
* Azure China 21Vianet 用にストレージ アカウントを追加します
* カスタム エンドポイントを持つストレージ アカウントを追加します
* コンテンツ テキストと画像 BLOB を開いて表示します
* BLOB のプロパティとメタデータを表示および編集します

#### <a name="fixes"></a>修正

* 修正: 大量の BLOB (500 以上) をアップロードまたはダウンロードすると、アプリの画面が白くなることがあります
* 修正: BLOB コンテナーをパブリック アクセス レベルに設定すると、コンテナーのフォーカスをリセットするまで新しい値は更新されません。 また、ダイアログは常に既定の "パブリック アクセスはありません" に設定され、実際の現在の値にはなりません。
* キーボードまたはアクセシビリティと UI のサポートが全体的に改善されました
* 階層リンクの履歴テキストが空白で長い場合は、折り返されます
* SAS ダイアログでは、入力の検証をサポートします
* ユーザーの資格情報の期限が切れた場合でも、ローカル ストレージがそのまま有効になります
* 開かれている BLOB コンテナーを削除すると、右側の BLOB エクスプローラーが閉じられます

#### <a name="known-issues"></a>既知の問題

* Linux のインストールには、更新またはアップグレードされた gcc バージョンが必要です。アップグレードの手順は次のとおりです。
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>バージョン 0.7.20151116.0

#### <a name="new"></a>新規

* macOS、Windows バージョン
* サインインしてストレージ アカウントを表示します。組織のアカウント、Microsoft アカウント、2FA などを使用します。
* ローカル開発ストレージ (ストレージ エミュレーターを使用、Windows のみ)
* Azure Resource Manager およびクラシック リソースのサポート
* BLOB、キュー、またはテーブルを作成および削除します
* 特定の BLOB、キュー、またはテーブルを検索します
* BLOB コンテナーの内容を探索します
* ディレクトリ内を表示して移動します
* BLOB とフォルダーをアップロード、ダウンロード、削除します
* BLOB のプロパティとメタデータを表示および編集します
* SAS キーを生成します
* 保存されているアクセス ポリシー (SAP) を管理および作成します
* プレフィックスで BLOB を検索します
* ドラッグ アンド ドロップでファイルをアップロードまたはダウンロードします

#### <a name="known-issues"></a>既知の問題

* BLOB コンテナーをパブリック アクセス レベルに設定すると、コンテナーのフォーカスをリセットするまで新しい値は更新されません
* ダイアログを開いてパブリック アクセス レベルを設定した場合、常に既定の "パブリック アクセスはありません" と表示され、実際の現在の値ではありません
* ダウンロードされた BLOB の名前を変更できません
* エラーが発生したときに、アクティビティ ログ エントリで進行中の状態に "スタック" を受け取ることがあり、そのエラーは表示されません
* 大量の BLOB をアップロードまたはダウンロードしようとすると、クラッシュしたり、完全に白くなったりする場合があります
* コピー操作のキャンセルが機能しない場合があります
* コンテナー (BLOB、キュー、テーブル) の作成中に、無効な名前を入力して、別のコンテナーの種類でもう 1 つ作成を続行した場合は、新しい種類にフォーカスを設定することはできません
* 新しいフォルダーの作成またはフォルダーの名前の変更を行うことができません




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
