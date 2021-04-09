---
title: Windows Virtual Desktop の MSIX アプリのアタッチの用語集 - Azure
description: MSIX アプリのアタッチの用語と概念の用語集。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7132eae073f3d53a104536076ae801ec9ff93e5f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96518670"
---
# <a name="msix-app-attach-glossary"></a>MSIX アプリのアタッチの用語集

この記事では、MSIX アプリのアタッチに関連する主な用語と概念の定義の一覧を示します。

## <a name="msix-container"></a>MSIX コンテナー

MSIX のコンテナーは、MSIX アプリが実行される場所です。 詳細については、「[MSIX コンテナー](/windows/msix/msix-container)」を参照してください。

## <a name="msix-application"></a>MSIX アプリケーション 

.MSIX ファイルに格納されているアプリケーション。

## <a name="msix-package"></a>MSIX パッケージ 

MSIX パッケージは、MSIX ファイルまたはアプリケーションです。

## <a name="msix-share"></a>MSIX 共有

MSIX 共有は、拡張された MSIX パッケージを保持するネットワーク共有です。 MSIX 共有では SMB 3 以降がサポートされています。 アプリケーション ファイルをシステム ドライブに移動することなく、この MSIX 共有からアプリケーションがステージングされます。

## <a name="msix-image"></a>MSIX イメージ

MSIX イメージは、VHD、VHDx、または CIM ファイルであり、1 つ以上の MSIX パッケージ アプリケーションが含まれます。 各アプリケーションは、MSIXMGR ツールを使用して、MSIX イメージで配信されます。

## <a name="repackage"></a>再パッケージ化

再パッケージ化では、MSIX Packaging Tool (MPT) の使用により、MSIX 以外のアプリケーションが取得され、MSIX に変換されます。 詳細については、「[MSIX Packaging Tool の概要](/windows/msix/packaging-tool/tool-overview)」を参照してください。

## <a name="expand-an-msix-package"></a>MSIX パッケージを展開する

MSIX パッケージの展開は、複数の手順から成るプロセスです。 展開により MSIX ファイルが取得され、その内容が VHD (x) または CIM ファイルに格納されます。 

MSIX パッケージを展開するには、次のようにします。

1. MSIX パッケージ (MSIX ファイル) を取得します。
2. MSIX ファイルの名前を .zip ファイルに変更します。
3. その zip ファイルをフォルダーに解凍します。
4. フォルダーと同じサイズの VHD を作成します。
5. VHD をマウントします。
6. ディスクを初期化します。
7. パーティションを作成します。
8. パーティションをフォーマットします。
9. 解凍した内容を VHD にコピーします。
10. MSIXMGR ツールを使用して、VHD の内容に ACL を適用します。
11. VHD(x) または [CIM](#cim) のマウントを解除します。

## <a name="upload-an-msix-package"></a>MSIX パッケージをアップロードする 

MSIX パッケージをアップロードには、拡張された MSIX パッケージを含む VHD(x) または [CIM](#cim) を MSIX 共有にアップロードすることが含まれます。

Windows Virtual Desktop では、アップロードは MSIX 共有ごとに 1 回行われます。 パッケージをアップロードすると、同じサブスクリプション内のすべてのホスト プールから参照できるようになります。

## <a name="add-an-msix-package"></a>MSIX パッケージを追加する

Windows Virtual Desktop で、MSIX パッケージは、追加されるとホスト プールにリンクされます。

## <a name="publish-an-msix-package"></a>MSIX パッケージを公開する 

Windows Virtual Desktop では、公開された MSIX パッケージは、Active Directory Domain Services (AD DS) または Azure Active Directory (Azure AD) のユーザーまたはユーザー グループに割り当てられる必要があります。

## <a name="staging"></a>ステージング

ステージングには、次の 2 つの点が含まれます。

- VHD(x) または [CIM](#cim) を VM にマウントする。
- MSIX パッケージが登録に使用できることを OS に通知する。

## <a name="registration"></a>登録

登録すると、ステージングされた MSIX パッケージをユーザーが使用できるようになります。 登録はユーザーごとに行われます。 特定のユーザーに対してアプリを明示的に登録していない場合、アプリを実行することはできません。

登録には、通常と遅延の 2 種類があります。

### <a name="regular-registration"></a>通常登録

通常登録では、ユーザーに割り当てられた各アプリケーションは完全に登録されます。 登録は、ユーザーがセッションにサインインするときに行われます。これは、Windows Virtual Desktop の使用を開始するのにかかる時間に影響を与える可能性があります。

### <a name="delayed-registration"></a>遅延登録

遅延登録では、ユーザーに割り当てられた各アプリケーションは部分的にのみ登録されます。 部分的な登録とは、[スタート] メニューのタイルとファイルのダブルクリックの関連付けが登録されることを意味します。 登録は、ユーザーがセッションにサインインする間に行われます。そのため、Windows Virtual Desktop の使用を開始するのにかかる時間への影響は最小限に抑えられます。 登録が完了するのは、ユーザーが MSIX パッケージ内のアプリケーションを実行した場合のみです。

遅延登録は現在、MSIX アプリのアタッチの既定の構成です。

## <a name="deregistration"></a>登録解除

登録を解除すると、ユーザーに対して登録されているが実行されていない MSIX パッケージが削除されます。 登録解除は、ユーザーが自分のセッションからサインアウトするときに発生します。 登録を解除するときに、MSIX アプリのアタッチによって、ユーザーに固有のアプリケーション データがローカルのユーザー プロファイルにプッシュされます。

## <a name="destage"></a>ステージング解除

ステージング解除により、現在実行されておらず、どのユーザーにもステージングされていない MSIX パッケージまたはアプリケーションをマウント解除できることが OS に通知されます。 これにより、それに対する OS 内のすべての参照が削除されます。

## <a name="cim"></a>CIM

.CIM は、複合イメージ ファイル システム (CimFS) に関連付けられた新しいファイル拡張子です。 CIM ファイルのマウントとマウント解除は、VHD ファイルよりも高速です。 また、CIM の場合、CPU とメモリの消費量が VHD よりも少なくなります。

CIM ファイルは .CIM という拡張子を持ち、メタデータと、実際のデータが含まれた 6 つ以上の追加ファイルを含むファイルです。 CIM ファイル内のファイルには拡張子がありません。 次の表は、CIM 内で見つかるファイルの例を示しています。

| ファイル名 | 拡張機能 | サイズ |
|-----------|-----------|------|
| VSC | CIM | 1 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 27 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 20 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 42 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 428 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 217 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 264,132 KB |

次の表は、VHD と CimFS のパフォーマンスを比較したものです。 これらの数値は、DSv4 マシンで実行される各形式で、500 個の 300 MB のファイルを使用したテスト実行の結果です。

|  仕様                          | VHD                    | CimFS   |
|---------------------------------|--------------------------|-----------|
| 平均マウント時間     | 356 ミリ秒                     | 255 ミリ秒      |
| 平均マウント解除時間   | 1615 ミリ秒                    | 36 ミリ秒       |
| メモリ消費量 | 6% (8 GB 中)                      | 2% (8 GB 中)       |
| CPU (スパイクのカウント)          | 複数回限界に達する | 影響なし |

## <a name="next-steps"></a>次のステップ

MSIX アプリ アタッチの詳細については、[概要](what-is-app-attach.md)と[よくあるご質問](app-attach-faq.md)に関するページを参照してください。 それ以外の場合、[アプリのアタッチの設定](app-attach.md)を開始してください。
