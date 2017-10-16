---
title: "既知の問題とトラブルシューティング ガイド | Microsoft Docs"
description: "既知の問題の一覧とトラブルシューティングに役立つガイド"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ef5e058d81b64c46e8bad6f85c0bef0c69fb1512
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench - 既知の問題とトラブルシューティング ガイド 
この記事は、Azure Machine Learning Workbench アプリケーションの使用の一環として発生したエラーや障害を見つけて修正するのに役立ちます。 

> [!NOTE]
> 電子メールやフォーラムの投稿でサポート チームとやり取りする場合は、ビルド番号を記載することをお勧めします。 アプリのビルド番号を調べるには、**[ヘルプ]** メニューをクリックします。 ビルド番号をクリックすると、クリップボードにコピーされます。 これを電子メールやサポート フォーラムに貼り付けると、問題のレポートに役立ちます。

![バージョン番号の検査](media/known-issues-and-troubleshooting-guide/version.png)

## <a name="windows-and-mac"></a>Windows と Mac
### <a name="centos-based-azure-data-science-virtual-machine"></a>CentOS ベースの Azure データ サイエンス仮想マシン 
* CentOS ベースの Azure データ サイエンス仮想マシン (DSVM) へのジョブの送信はサポートされていません。 [Ubuntu ベース DSVM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) をターゲットにすることができます。

### <a name="docker-error"></a>Docker エラー 
* ローカルの Docker コンテナーに対して実行する際に、次のエラーが表示される場合は、Docker DNS サーバーを [自動] から固定値 8.8.8.8 に変更することで修正できます。 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```
![イメージ](media/known-issues-and-troubleshooting-guide/docker_dns.png)


### <a name="linux-vm-password"></a>Linux VM のパスワード 
* Ubuntu Linux VM 上の Azure Portal からパスワードを変更する場合、ジョブの実行時に次のエラーが発生することがあります。
  ```
  sudo: no tty present and no askpass program specified.
  ``` 

  回避策は、次のコンテンツを含むファイル (例: _myDockerUsers_) を _/etc/sudoers.d_ に追加することです。
  ```
  <your_username> ALL = NOPASSWD: /usr/bin/docker, /usr/bin/nvidia-docker
  ```

### <a name="ssh-keys"></a>SSH キー 
* SSH キーは、リモート マシンまたは SSH 経由の Spark クラスターに接続しているときにはサポートされません。 ユーザー名/パスワード モードのみがサポートされます。

## <a name="windows-only"></a>Windows のみ 
### <a name="sharing-c-drive-in-docker"></a>Docker での C ドライブの共有 
* ファイル エクスプローラーを使用して、C ドライブ上の共有を検査します
* ネットワーク アダプターの設定を開き、vEthernet の "Microsoft ネットワーク用ファイルとプリンター共有" をアンインストール/再インストールします
* Docker の設定を開き、Docker の設定から C ドライブを共有します
* Windows パスワードの変更は共有に影響します。 ファイル エクスプローラーを開き、C ドライブをもう一度共有し、新しいパスワードを入力します。
* C ドライブを Docker と共有しようとするときに、ファイアウォールの問題が発生する可能性もあります。 この [Stack Overflow の投稿](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051)が役立つ可能性があります。
* ドメインの資格情報を使用して C ドライブを共有する場合、ドメイン コントローラーにアクセスできないネットワーク (たとえば、ホーム ネットワークやパブリック wifi など) で共有が動作を停止することがあります。 詳細については、[こちらの投稿](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/)をご覧ください。



## <a name="mac-only"></a>Mac のみ 
* Mac では、テキストのクラスタリング変換はサポートされていません。
* Mac では、RevoScalePy ライブラリはサポートされていません。

## <a name="azure-machine-learning-service-limits"></a>Azure Machine Learning サービスの制限事項

- 許容される最大のプロジェクト フォルダー サイズ: 25 MB
    >[!Note]
    >この制限は、`.git`、`docs`、`outputs` フォルダーには適用されません。 これらのフォルダー名では大文字と小文字が区別されます。

- 許容される最大実験実行時間: 7 日間
- 実行時に `outputs` フォルダーで追跡されるファイルの最大サイズ: 512 MB 

>[!NOTE]
>大きなファイルを処理する場合は、「[Persisting Changes and Deal with Large Files (大きなファイルの変更の永続化と処理)](how-to-read-write-files.md)」をご覧ください。

## <a name="other-issues"></a>その他の問題
ドキュメントに記載されていないその他の問題にお気づきの場合は、"_[気に入った機能の報告]/[問題点、改善点の報告]_" でフィードバックをお送りください。 




