---
title: "Azure RemoteApp イメージの要件 | Microsoft Docs"
description: "Azure RemoteApp で使用するイメージを作成するための要件について説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 7cbb90f4-6dc9-462c-a429-088cdb57414e
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: a37804025cbd298ef2b98c84b5cc59c0cba07dd9


---
# <a name="requirements-for-azure-remoteapp-images"></a>Azure RemoteApp イメージの要件
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

Azure RemoteApp では Windows Server 2012 R2 イメージを使用して、ユーザーと共有するすべてのプログラムをホストします。 カスタム イメージを作成するには、既存のイメージを使用することも、 [新しいイメージを作成する](remoteapp-create-custom-image.md)こともできます。

> [!TIP]
> Azure RemoteApp サブスクリプションを利用すれば、Azure VM ギャラリーにある Windows Server 2012 R2 イメージにアクセスし、独自のテンプレート イメージを作成できることをご存じでしたか。 [ご覧ください](remoteapp-image-on-azurevm.md)。  
> 
> 

Azure RemoteApp で使用するためにアップロードできるイメージの要件は次のとおりです。

* カスタム アプリケーションは、イメージ上にローカルにデータを格納しません。 これらのイメージはステートレスで、アプリケーションのみを含む必要があります。
* イメージには、失われる可能性があるデータは含まれません。
* イメージのサイズは MB の倍数にする必要があります。 正確な倍数ではないイメージをアップロードしようとすると、アップロードは失敗します。
* イメージのサイズは 127 GB 未満でなければなりません。
* VHD ファイルになければなりません (VHDX ファイルは現在サポートされていません)。
* VHD は、第 2 世代仮想マシンであってはなりません。
* VHD は固定サイズにすることも、動的に拡大する容量可変にすることも可能です。 固定サイズの VHD より Azure へのアップロードの所要時間が短いことから、容量可変の VHD が推奨されます。
* ディスクはマスター ブート レコード (MBR) パーティション分割のスタイルを使用して初期化しなければなりません。 GUID パーティション テーブル (GPT) パーティション分割のスタイルはサポートされていません。
* VHD には Windows Server 2012 R2 の単一インストールが含まれていなければなりません。 複数のボリュームを含むことはできますが、Windows がインストールされるのは&1; ボリュームのみです。
* リモート デスクトップ セッション ホスト (RDSH) ロールとデスクトップ エクスペリエンスの機能がインストール済みでなければなりません。
* リモート デスクトップ接続ブローカーのロールをインストール *しないで* ください。
* 暗号化ファイル システム (EFS) は無効にする必要があります。
* イメージはパラメーター **/oobe /generalize /shutdown** を使用して SYSPREP を実施済みでなければなりません (**/mode:vm** パラメーターは使用しないでください)。
* スナップショット チェーンからの VHD のアップロードはサポートされていません。

Azure RemoteApp のイメージ作成の詳細については、「 [Azure RemoteApp イメージの作成](remoteapp-imageoptions.md) 」を参照してください。




<!--HONumber=Dec16_HO2-->


