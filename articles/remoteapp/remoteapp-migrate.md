---
title: "Azure RemoteApp からユーザー データを移行する | Microsoft Docs"
description: "Azure RemoteApp を移行先または移行元とするデータの移行方法について説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: d7e4fbf1-cb42-4430-94a0-ed6d4676fc86
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: e4d94d3f9736378d93e93be6645ed04ade763ca3
ms.openlocfilehash: 456175d718898287496d9e19cae8da259fba2e8e


---
# <a name="how-to-migrate-data-into-and-out-of-azure-remoteapp"></a>Azure RemoteApp を移行先または移行元とするデータの移行方法
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

多様なツールおよび方法を使用して、 [ユーザー データ](remoteapp-upd.md) を Azure RemoteApp へ、または Azure RemoteApp から転送できます。 次に方法をいくつか示します。

* クリップボードの共有を使用して、コピーして貼り付ける
* ファイルとデータをファイル サーバーにコピーする
* ブラウザー経由でファイルを OneDrive for Business にコピーする
* リダイレクトを使用してファイルをコピーする

> [!NOTE]
> OneDrive for Business やコンシューマー同期エージェントを有効にすることはできません。これらは Azure RemoteApp で[サポートされていません](remoteapp-onedrive.md)。
> 
> 

## <a name="use-copy-and-paste-in-file-explorer"></a>エクスプローラーでコピー/貼り付けを使用する
クリップボードを使用したコピー/貼り付けは、RemoteApp のデプロイで[既定](remoteapp-redirection.md)で有効になります。 そのため、ユーザーはローカル PC と RemoteApp のアプリとの間でファイルをコピーできます。 多くの場合、RemoteApp でアプリを使用する標準的な過程では、ユーザーは UPD にファイルを保存します。RemoteApp からデータを移動するのは簡単です。

1. RemoteApp コレクションで[エクスプローラーをアプリとして公開発行します](remoteapp-publish.md) (これは管理タスクであることに注意してください)。
2. 公開したエクスプローラーのアプリを起動し、これを使用してファイルを UPD との間でコピー/貼り付けを行うようにユーザーに指示します。

## <a name="upload-files-and-data-to-a-file-server-by-using-standard-network-file-copy"></a>標準のネットワーク ファイルのコピーを使用して、ファイルとデータをファイル サーバーにアップロードする
組織がファイル サーバーを使用して一般的なデータを格納するのはよくあることです。 サーバー名または場所がわかっている場合、ユーザーは、ローカル ネットワークでサーバーを参照して、先ほど実行したのと同様にそこにファイルをコピーすることができます。 ここでもエクスプローラーを RemoteApp に公開し、ユーザーと共有します。

> [!NOTE]
> ファイル サーバーは、RemoteApp がデプロイされているルーティング可能なネットワークに配置する必要があります。
> 
> 

## <a name="copy-files-to-onedrive-for-business"></a>OneDrive for Business にファイルをコピーする
RemoteApp で OneDrive for Business 同期エージェントを有効にすることはできませんが、ブラウザーを使用して UPD から OneDrive for Business にファイルをコピーすることはできます。 

1. エクスプローラーを RemoteApp に公開し、このアプリを使用してファイルにアクセスするようにユーザーに伝えます。 
2. ファイルの転送は、圧縮されている場合に最も簡単になります。そのため、ユーザーは、OneDrive for Business に移動するすべてのファイルを格納した .zip ファイルを作成する必要があります。
3. ユーザーに、Office 365 ポータルに移動し、OneDrive に移動して .zip ファイルをアップロードするように依頼します。

## <a name="copy-files-by-using-drive-redirection"></a>ドライブ リダイレクトを使用してファイルをコピーする
[ドライブ リダイレクト](remoteapp-redirection.md)を有効にしている場合、ユーザー用にマップされたドライブが既に作成されています。 この場合、ユーザーはリダイレクトされたドライブ上でファイルを zip 圧縮してから、ローカル PC に保存できます。




<!--HONumber=Dec16_HO2-->


