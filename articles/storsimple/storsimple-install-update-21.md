---
title: "StorSimple デバイスへの Update 2.2 のインストール | Microsoft Docs"
description: "StorSimple 8000 シリーズのデバイスに StorSimple 8000 シリーズの Update 2.2 をインストールする方法について説明します。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 047c7a4b-73d0-45ea-8d51-c54d71871392
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/02/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c60b4de88d60f0373d69fe2f3cee5ccf888b8d8c


---
# <a name="install-update-22-on-your-storsimple-device"></a>StorSimple デバイスへの Update 2.2 のインストール
## <a name="overview"></a>概要
このチュートリアルでは、Update 2.2 より前のソフトウェア バージョンを実行している StorSimple デバイスに、Azure クラシック ポータル経由で、および修正プログラムで Update 2.2 をインストールする方法について説明します。 修正プログラムによる方法は、ゲートウェイが StorSimple デバイスの DATA 0 以外のネットワーク インターフェイスで構成されており、Update 1 より前のソフトウェア バージョンから更新する場合に使用します。

Update 2.2 には、デバイスのソフトウェア、WMI、および iSCSI の更新プログラムが含まれています。 バージョン 2.1 から更新する場合は、デバイス ソフトウェアの更新のみを適用する必要があります。 Update 2 より前のバージョンから更新する場合、LSI ドライバー、Spaceport、Storport、およびディスクのファームウェア更新も適用する必要があります。 デバイス ソフトウェア、WMI、iSCSI、LSI ドライバー、Spaceport、Storport の修正プログラムは中断を伴わない更新プログラムであるため、Azure クラシック ポータルを使用して適用できます。 ディスク ファームウェアの更新プログラムは中断を伴う更新プログラムであるため、デバイスの Windows PowerShell インターフェイスでのみ適用できます。 

> [!IMPORTANT]
> * インストールの前に、ハードウェアの状態とネットワーク接続の点からデバイスの正常性を判断するための手動と自動の一連の事前チェックが行われます。 これらの事前チェックは、Azure クラシック ポータルから更新プログラムを適用する場合にのみ実行されます。
> * ソフトウェアとドライバーの更新プログラムのインストールには Azure クラシック ポータルを使用することをお勧めします。 ポータルで更新前のゲートウェイのチェックに失敗した場合のみ、(更新プログラムをインストールする) デバイスの Windows PowerShell インターフェイスに移動してください。 更新するバージョンによっては、更新プログラムのインストールに 1.5 ～ 2.5 時間かかる場合があります。 メンテナンス モードの更新プログラムは、デバイスの Windows PowerShell インターフェイスからインストールする必要があります。 メンテナンス モードの更新プログラムは中断を伴う更新プログラムであるため、デバイスにダウンタイムが発生します。
> * オプションの StorSimple Snapshot Manager を実行している場合は、デバイスを更新する前に Snapshot Manager のバージョンを Update 2.2 にアップグレードしたことを確認します。
> 
> 

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-22-via-the-azure-classic-portal"></a>Azure クラシック ポータルを使用して Update 2.2 をインストールする
デバイスを [Update 2.2](storsimple-update21-release-notes.md)に更新するには、次の手順を実行します。

> [!NOTE]
> Update 2 以降 (Update 2.1 を含む) を適用する場合、Microsoft はデバイスから追加の診断情報を取得することができます。 その結果、Microsoft の運用チームが問題のあるデバイスを識別したときに、デバイスから情報を収集して問題を診断する能力が向上します。 Update 2 以降を受け入れると、このプロアクティブ サポートの提供に同意することになります。
> 
> 

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

1. デバイスで **StorSimple 8000 Series Update 2.2 (6.3.9600.17708)**が実行されていることを確認します。 **[最終更新日]** も変更されています。 
   
   Update 2 より前のバージョンから更新する場合、メンテナンス モードの更新プログラムも利用できることが示されます (このメッセージは、更新プログラムをインストールした後、最大 24 時間表示され続ける可能性があります)。
   
   メンテナンス モードの更新プログラムは、デバイスのダウンタイムを発生させる更新プログラムであり、デバイスの Windows PowerShell インターフェイス経由でのみ適用できます。 Update 1.2 を実行している場合は、ディスクのファームウェアが既に最新になっている可能性があります。その場合、メンテナンス モードの更新プログラムをインストールする必要はありません。
   
   Update 2 から更新する場合、デバイスは最新の状態です。 残りの手順を省略できます。
2. 「[修正プログラムをダウンロードするには](#to-download-hotfixes)」で示された手順を使用して KB3121899 を検索してメンテナンス モードの更新プログラムをダウンロードします。ディスク ファームウェアの更新プログラムがインストールされます (他の更新プログラムが既にインストールされている必要があります)。
3. メンテナンス モードの更新プログラムをインストールするには、「 [メンテナンス モードの修正プログラムをインストールして確認するには](#to-install-and-verify-maintenance-mode-hotfixes) 」に記載されている手順に従います。 

## <a name="install-update-22-as-a-hotfix"></a>Update 2.2 を修正プログラムとしてインストールする
この手順は、Azure クラシック ポータルから更新プログラムをインストールしようとしたときにゲートウェイのチェックに失敗した場合に使用してください。 このチェックは、DATA 0 以外のネットワーク インターフェイスに割り当てられているゲートウェイがある場合に、デバイスが Update 1 より前のソフトウェア バージョンを実行していると、失敗します。

修正プログラムによる方法でアップグレードできるソフトウェアのバージョンは、次のとおりです。

* Update 0.1、0.2、0.3
* Update 1、1.1、1.2
* Update 2、2.1 

> [!IMPORTANT]
> * デバイスがリリース (GA) バージョンを実行している場合、この更新プログラムの使用については、 [Microsoft サポート](storsimple-contact-microsoft-support.md) にお問い合わせください。
> 
> 

修正プログラムを使用する方法には、次の 3 つの手順が含まれます。

* Microsoft Update カタログから修正プログラムをダウンロードします。
* 通常モードの修正プログラムをインストールして確認します。
* メンテナンス モードの修正プログラムをインストールして確認します (Update 2 より前のソフトウェアから更新する場合のみ)。

#### <a name="download-updates-for-a-device-running-update-21-software"></a>Update 2.1 のソフトウェアを実行しているデバイスの更新プログラムをダウンロードする
**ご使用のデバイスで Update 2.1 を実行している場合**、そのデバイスのソフトウェア更新プログラム KB3179904 のみをダウンロードする必要があります。 'all-hcsmdssoftwareudpate' で始まるバイナリ ファイルのみをインストールします。 `all-cismdsagentupdatebundle` で始まる Cis と MDS のエージェントの更新プログラムはインストールしないでください。 間違ってインストールすると、エラーが発生します。 これは中断を伴わない更新プログラムです。IO の中断およびデバイスのダウンタイムはありません。

#### <a name="download-updates-for-a-device-running-update-2-software"></a>Update 2 のソフトウェアを実行しているデバイスの更新プログラムをダウンロードする
**デバイスが Update 2 を実行している場合は**、指定の順序で次の修正プログラムをダウンロードしてインストールする必要があります。

| 順序 | KB | 説明 | 更新の種類 | インストール時間 |
| --- | --- | --- | --- | --- |
| 1. |KB3179904 |ソフトウェア更新 &#42; |通常  <br></br>中断なし |～ 45 分 |
| 2. |KB3146621 |iSCSI パッケージ |通常  <br></br>中断なし |～ 20 分 |
| 3. |KB3103616 |WMI パッケージ |通常  <br></br>中断なし |～ 12 分 |

 &#42; *ソフトウェア更新プログラムは、デバイスのソフトウェア更新プログラム (`all-hcsmdssoftwareupdate` で始まる) と Cis と Mds のエージェント (`all-cismdsagentupdatebundle` で始まる) の 2 つのバイナリ ファイルで構成されます。Cis と Mds のエージェントをインストールする前に、デバイスのソフトウェア更新プログラムをインストールする必要があります。また、Cis と MDS のエージェント更新プログラムを適用したら (残りの更新プログラムを適用する前に)、`Restart-HcsController` コマンドレットを使用してアクティブなコントローラーを再起動する必要があります。* 

#### <a name="download-updates-for-a-device-running-pre-update-2-software"></a>Update 2 より前のソフトウェアを実行しているデバイスの更新プログラムをダウンロードする
**デバイスがバージョン 0.2、0.3、1.0、1.1 を実行している場合は**、ソフトウェア、iSCSI、WMI の各更新プログラムに加え、LSI ドライバーとファームウェアの更新プログラムもダウンロードしてインストールする必要があります。 この更新プログラムは、Update 1.2 または 2 を実行している場合は既にインストールされています。 

| 順序 | KB | 説明 | 更新の種類 | インストール時間 |
| --- | --- | --- | --- | --- |
| 4. |KB3121900 |LSI ドライバーおよびファームウェア |通常  <br></br>中断なし |～ 20 分 |

<br></br>
**デバイスがバージョン 0.2、0.3、1.0、1.1、1.2 を実行している場合は**、Spaceport および Storport の修正プログラムをダウンロードしてインストールする必要があります。 これらの修正プログラムは、Update 2 を実行している場合は既にインストールされています。

| 順序 | KB | 説明 | 更新の種類 | インストール時間 |
| --- | --- | --- | --- | --- |
| 5. |KB3090322 |Spaceport 修正プログラム  </br>  Windows Server 2012 R2 |通常  <br></br>中断なし |～ 20 分 |
| 6. |KB3080728 |Storport 修正プログラム  </br>  Windows Server 2012 R2 |通常  <br></br>中断なし |～ 20 分 |

<br></br>
 ディスク ファームウェアの更新プログラムもインストールすることが必要な場合があります。 `Get-HcsFirmwareVersion` コマンドレットを実行すると、ディスク ファームウェアの更新プログラムが必要かどうかを確認できます。 `XMGG`、`XGEG`、`KZ50`、`F6C2`、`VR08` の各ファームウェア バージョンを実行している場合は、これらの更新プログラムをインストールする必要はありません。

| 順序 | KB | 説明 | 更新の種類 | インストール時間 |
| --- | --- | --- | --- | --- |
| 7. |KB3121899 |ディスク ファームウェア |メンテナンス  <br></br>中断あり |～ 30 分 |

<br></br>

> [!IMPORTANT]
> * この手順は、Update 2.2 を適用するために 1 回だけ実行する必要があります。 以降の更新プログラムは、Azure クラシック ポータルを使用して適用できます。
> * Update 2 から更新する場合、合計のインストール時間は 1.5 時間ほどです。
> * この手順を使用して更新プログラムを適用する前に、両方のデバイス コント ローラーがオンラインであり、すべてのハードウェア コンポーネントが正常な状態であることを確認します。
> 
> 

この修正プログラムをダウンロードしてインポートするには、次のステップを実行します。

[!INCLUDE [storsimple-install-update21-hotfix](../../includes/storsimple-install-update21-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>次のステップ
[Update 2.1 リリース](storsimple-update21-release-notes.md)の詳細を確認します。




<!--HONumber=Nov16_HO3-->


