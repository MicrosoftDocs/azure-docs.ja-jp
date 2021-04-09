---
title: StorSimple デバイスの更新 | Microsoft Docs
description: StorSimple の更新プログラムの機能を使用して、通常またはメンテナンス モードの更新プログラムや修正プログラムをインストールする方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: faf06775b78c5a7c90cea000ac0a1eb768107ef4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956842"
---
# <a name="update-your-storsimple-8000-series-device"></a>StorSimple 8000 シリーズ デバイスの更新
> [!NOTE]
> StorSimple のクラシック ポータルは非推奨となる予定です。 ご使用の StorSimple デバイス マネージャーは、廃止スケジュールに従い、自動的に新しい Azure Portal に移行されます。 この移行に関しては、メールとポータル通知でお知らせします。 このドキュメントも間もなく廃止されます。 この移行についてご質問があれば、[Azure Portal への移行に関する FAQ](./index.yml) のページを参照してください。

## <a name="overview"></a>概要
StorSimple の更新プログラムの機能を使用すると、StorSimple デバイスを簡単に最新の状態に保つことができます。 更新プログラムの種類に応じて、Azure クラシック ポータルまたは Windows PowerShell インターフェイスを使用して、デバイスに更新プログラムを適用できます。 このチュートリアルでは、更新プログラムの種類とそれぞれのインストール方法について説明します。

次の 2 種類のデバイス更新プログラムを適用することができます。 

* 通常 (または標準モード) の更新プログラム
* メンテナンス モードの更新プログラム

通常の更新プログラムは、Azure クラシック ポータルまたは Windows PowerShell を使用してインストールできます。ただし、メンテナンス モードの更新プログラムをインストールするには、Windows PowerShell を使用する必要があります。 

各更新プログラムの種類について、以下で個別に説明します。

### <a name="regular-updates"></a>通常の更新プログラム
通常の更新プログラムは中断を伴わないため、デバイスが通常モードのときにインストールできます。 通常の更新プログラムは、Microsoft Update Web サイトを通じて各デバイス コントローラーに適用されます。 

> [!IMPORTANT]
> 更新プロセス中にコントローラーのフェールオーバーが発生する場合があります。 ただし、このフェールオーバーはシステムの可用性や操作に影響しません。
> 
> 

* Azure クラシック ポータルで通常の更新プログラムをインストールする方法の詳細については、「[Azure クラシック ポータルを使用した通常の更新プログラムのインストール](#install-regular-updates-via-the-azure-classic-portal)」を参照してください。
* 通常の更新プログラムのインストールには、StorSimple 用 Windows PowerShell も使用できます。 詳細については、「 [StorSimple 用 Windows PowerShell を使用した通常の更新プログラムのインストール](#install-regular-updates-via-windows-powershell-for-storsimple)」をご覧ください。

### <a name="maintenance-mode-updates"></a>メンテナンス モードの更新プログラム
メンテナンス モードの更新プログラムとは、ディスク ファームウェアのアップグレードなど、中断を伴う更新プログラムです。 更新するには、デバイスをメンテナンス モードにする必要があります。 詳細については、「 [手順 2: メンテナンス モードを開始する](#step2)」をご覧ください。 Azure クラシック ポータルを使用してメンテナンス モードの更新プログラムをインストールすることはできません。 代わりに、StorSimple 用 Windows PowerShell を使用する必要があります。 

メンテナンス モードの更新プログラムをインストールする方法の詳細については、「 [StorSimple 用 Windows PowerShell を使用したメンテナンス モードの更新プログラムのインストール](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple)」を参照してください。

> [!IMPORTANT]
> メンテナンス モードの更新プログラムは、各コントローラーに個別に適用する必要があります。 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Azure クラシック ポータルを使用した通常の更新プログラムのインストール
Azure クラシック ポータルを使用して、StorSimple デバイスに更新プログラムを適用できます。

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell を使用した通常の更新プログラムのインストール
また、StorSimple 用 Windows PowerShell を使用して、通常 (標準モード) の更新プログラムを適用できます。

> [!IMPORTANT]
> StorSimple 用 Windows PowerShell を使用して定期的に更新をインストールできますが、Azure クラシック ポータルから定期的に更新プログラムをインストールすることをお勧めします。 Update 1 以降では、ポータルから更新プログラムをインストールする前に事前チェックが実行されるようになります。 こうした事前チェックにより、エラーを回避してより滑らかなエクスペリエンスを実現します。 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell を使用したメンテナンス モードの更新プログラムのインストール
StorSimple 用 Windows PowerShell を使用して、メンテナンス モードの更新プログラムを StorSimple デバイスに適用します。 このモードでは、すべての I/O 要求が一時停止します。 非揮発性ランダム アクセス メモリ (NVRAM) などのサービスやクラスター化サービスも停止します。 このモードを開始または終了するときに、両方のコントローラーが再起動されます。 このモードを終了するときに、すべてのサービスが再開され、正常な状態になります  (これには数分かかることがあります)。

メンテナンス モードの更新プログラムを適用する必要がある場合は、インストールが必要な更新プログラムがあることを示すアラートを Azure クラシック ポータルから受信します。 このアラートには、StorSimple 用 Windows PowerShell を使用して更新プログラムをインストールする手順が含まれます。 デバイスを更新した後、同じ手順を使用してデバイスを通常モードに変更します。 手順については、「 [手順 4: メンテナンス モードを終了するには](#step4)」を参照してください。

> [!IMPORTANT]
> * メンテナンス モードに入る前に、Azure クラシック ポータルの **[メンテナンス]** ページで **[ハードウェアの状態]** を確認して、両方のデバイス コントローラーが正常な状態であることを確かめます。 コントローラーが正常な状態でない場合は次の手順を Microsoft サポートにお問い合わせください。 詳細については、[Microsoft サポートに問い合わせる] を参照してください。 
> * メンテナンス モードを使用するとき、1 つのコントローラーに最初に更新プログラムを適用してから、その他のコントローラーに適用する必要があります。
> 
> 

### <a name="step-1-connect-to-the-serial-console"></a>手順 1: シリアル コンソールに接続する <a name="step1"></a>
まず、PuTTY などのアプリケーションを使用して、シリアル コンソールにアクセスします。 次の手順は、PuTTY を使用してシリアル コンソールに接続する方法について説明します。

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode"></a>手順 2: メンテナンス モードを開始する <a name="step2"></a>
コンソールに接続した後は、インストールする更新プログラムがあるかどうかを確認し、インストールするためにメンテナンス モードを開始します。

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates"></a>手順 3: 更新プログラムをインストールする <a name="step3"></a>
次に、更新プログラムをインストールします。

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode"></a>手順 4: メンテナンス モードを終了する <a name="step4"></a>
最後に、メンテナンス モードを終了します。

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell を使用した修正プログラムのインストール
Microsoft Azure StorSimple の更新プログラムとは異なり、修正プログラムは共有フォルダーからインストールされます。 更新プログラムと同様に、修正プログラムも 2 種類あります。 

* 通常の修正プログラム 
* メンテナンス モードの修正プログラム  

次の手順は、StorSimple 用 Windows PowerShell を使用して通常の修正プログラムとメンテナンス モードの修正プログラムをインストールする方法について説明します。

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>デバイスを工場出荷時の状態にリセットした場合、更新プログラムはどうなりますか。
デバイスを工場出荷時の設定にリセットした場合、すべての更新プログラムが失われます。 出荷時の設定にリセットしたデバイスを登録し、構成した後で、Azure クラシック ポータルまたは StorSimple 用 Windows PowerShell から更新プログラムを手動でインストールする必要があります。 出荷時の設定へのリセットの詳細については、「 [デバイスの出荷時設定へのリセット](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)」をご覧ください。

## <a name="next-steps"></a>次のステップ
* [StorSimple 用 Windows PowerShell を使用した StorSimple デバイスの管理の詳細](./storsimple-8000-windows-powershell-administration.md)
* [StorSimple Manager サービスを使用した StorSimple デバイスの管理](./storsimple-8000-manager-service-administration.md)の詳細を確認する。