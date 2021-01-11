---
title: Windows Virtual Desktop の MSIX アプリ アタッチに関するよくあるご質問 - Azure
description: Windows Virtual Desktop の MSIX アプリ アタッチに関するよくあるご質問。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a63d7f067665836910b91b2911db522f0a92bbb1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556166"
---
# <a name="msix-app-attach-faq"></a>MSIX アプリ アタッチに関するごよくある質問

この記事では、Windows Virtual Desktop の MSIX アプリ アタッチに関するよくあるご質問に回答します。

## <a name="does-msix-app-attach-use-fslogix"></a>MSIX アプリ アタッチでは FSLogix は使用されますか?

MSIX アプリ アタッチでは、FSLogix は使用されません。 ただし、アプリ アタッチと FSLogix は、ユーザーがシームレスなエクスペリエンスを得られるよう連携して動作するように設計されています。

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>MSIX アプリ アタッチは Windows Virtual Desktop 外で使用できますか?

はい。MSIX アプリ アタッチは、Windows 10 Enterprise に含まれた機能であり、Windows Virtual Desktop 外で使用できます。 ただし、Windows Virtual Desktop 外には MSIX アプリ アタッチの管理プレーンはありません。

## <a name="how-do-i-get-an-msix-package"></a>MSIX パッケージはどのように入手できますか?

MSIX パッケージは、ご自分のソフトウェア ベンダーから提供されます。 非 MSIX パッケージを MSIX に変換することもできます。 詳細については、「[既存のお使いのインストーラーを MSIX に移行する方法](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)」を参照してください。

## <a name="which-operating-systems-support-msix-app-attach"></a>MSIX アプリ アタッチはどのオペレーティング システムでサポートされていますか?

Windows 10 Enterprise および Windows 10 Enterprise マルチセッションでサポートされています。

## <a name="next-steps"></a>次のステップ

MSIX アプリ アタッチの詳細については、[概要](what-is-app-attach.md)と[用語集](app-attach-glossary.md)に関するページを参照してください。 それ以外の場合、[アプリのアタッチの設定](app-attach.md)を開始してください。