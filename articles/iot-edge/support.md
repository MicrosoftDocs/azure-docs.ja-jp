---
title: サポートされているオペレーティング システム、コンテナー エンジン - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge デーモンとランタイムを実行できるオペレーティング システム、運用デバイス用にサポートされるコンテナー エンジンについて説明します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 81d19552b56de540f235960c498c64e7b276320c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030962"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge のサポートされるシステム

この記事では、公式またはプレビューの IoT Edge によってサポートされるシステムおよびコンポーネントについて、詳しく説明します。 

Azure IoT Edge サービスの使用中に問題が発生した場合は、いくつかの方法でサポートを求めることができます。 サポートについては、次のいずれかのチャネルをお試しください。

**バグの報告** – Azure IoT Edge 製品に関する開発の大多数は、IoT Edge のオープン ソース プロジェクトで発生します。 バグはプロジェクトの[問題ページ](https://github.com/azure/iotedge/issues)で報告できます。 修正プログラムはプロジェクトが製品の更新プログラムになるまでの時間を早めます。

**Microsoft カスタマー サポート チーム** – [サポート プラン](https://azure.microsoft.com/support/plans/)に加入しているユーザーは、[Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) から直接サポート チケットを作成することで、Microsoft カスタマー サポート チームとやり取りをすることができます。

**機能の要望** – Azure IoT Edge 製品はその製品の[ユーザーの声のページ](https://feedback.azure.com/forums/907045-azure-iot-edge)を介して機能の要望を追跡します。

## <a name="container-engines"></a>コンテナー エンジン

Azure IoT Edge モジュールはコンテナーとして実装されているため、IoT Edge にはモジュールを起動するためのコンテナー エンジンが必要です。 Microsoft には、この要件を満たすために、moby-engine というコンテナー エンジンが用意されています。 このコンテナー エンジンは、Moby オープンソース プロジェクトをベースとします。 他にも有名なコンテナー エンジンとして、Docker CE や Docker EE が挙げられます。 これらも Moby オープンソース プロジェクトをベースとし、Azure IoT Edge と互換性があります。 Microsoft はこれらのコンテナー エンジンを使用するシステムに対してベスト エフォート サポートを提供しています。ただし、Microsoft はそれらで発生した問題の修正プログラムを配布することができません。 この理由から、Microsoft では運用システムで moby-engine を使用することを推奨しています。

<br>
<center>

![コンテナー ランタイムとしての Moby](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>オペレーティング システム
Azure IoT Edge はコンテナーを実行できるほとんどのオペレーティング システムで実行できます。ただし、それらのすべてのシステムが均等にサポートされているわけではありません。 オペレーティング システムは、ユーザーが受けられるサポートのレベルを表す階層別にグループ化されています。
* レベル 1 のシステムはサポートされています。 レベル 1 のシステムでは、
    * Microsoft がそのオペレーティング システムに対して自動テストを実施している
    * Microsoft がそれらのインストール パッケージを提供している
* レベル 2 のシステムは Azure IoT Edge と互換性があり、比較的簡単に使用できます。 レベル 2 のシステムでは、
    * Microsoft がそのプラットフォームでアドホック テストを実施している、またはパートナーがそのプラットフォーム上で Azure IoT Edge を正常に実行していることを把握している
    * 他のプラットフォーム用のインストール パッケージがそれらのプラットフォームで機能することがある
    
ホスト OS のファミリは、モジュールのコンテナー内で使用されるゲスト OS のファミリと常に一致する必要があります。 つまり、Linux コンテナーは Linux 上でのみ、Windows コンテナーは Windows 上でのみ使用できます。 Windows を使用している場合は、プロセス分離コンテナーのみがサポートされます (Hyper-V 分離コンテナーはサポートされません)。  

<br>
<center>

![ホスト OS がゲスト OS に一致する](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>レベル 1

次の表に示すシステムは、一般提供またはパブリック プレビューにおいて、Microsoft によってサポートされており、新しいリリースごとにテストされています。 

| オペレーティング システム | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian-stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 16.04 | ![Ubuntu Server 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | パブリック プレビュー  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | パブリック プレビュー |
| Windows 10 IoT Enterprise ビルド 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019 ビルド 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019 ビルド 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows 10 IoT Core ビルド 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |


上記の Windows オペレーティング システムは、Windows 上で Windows コンテナーを実行するデバイスの要件です。これは、運用環境でサポートされる唯一の構成です。 Windows 用の Azure IoT Edge インストール パッケージを使用すると、Windows 上で Linux コンテナーを使用できます。ただし、この構成は開発およびテスト専用です。 詳細については、「[Windows で IoT Edge を使用し、Linux コンテナーを実行する](how-to-install-iot-edge-windows-with-linux.md)」をご覧ください。

### <a name="tier-2"></a>レベル 2

次の表に示すシステムは、Azure IoT Edge と互換性があると見なされますが、アクティブにテストまたは管理されてはいません。 

| オペレーティング システム | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| CentOS 7.5 | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 8 | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 9 | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 10<sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| RHEL 7.5 | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| Ubuntu 16.04 | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| Ubuntu 18.04 | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| Wind River 8 | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Yocto | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster<sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10 システム (Raspian Buster を含む) は、IoT Edge がサポートしていないバージョンの OpenSSL を使用します。 IoT Edge をインストールする前に、次のコマンドを実行して以前のバージョンをインストールしてください。 

```bash
sudo apt-get install libssl1.0.2
```

## <a name="virtual-machines"></a>Virtual Machines
Azure IoT Edge は仮想マシンで実行できます。 仮想マシンを IoT Edge デバイスとして使用することは、エッジ インテリジェンスで既存のインフラストラクチャを拡張しようとする場合によく行われます。 ホスト VM OS のファミリは、モジュールのコンテナー内で使用されるゲスト OS のファミリと一致する必要があります。 この要件は、Azure IoT Edge がデバイス上で直接実行されるときと同じです。 Azure IoT Edge は基盤となる仮想化テクノロジに依存しており、Hyper-V や vSphere などのプラットフォームを使用した VM で動作します。

<br>
<center>

![VM 内の Azure IoT Edge](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>最小システム要件
Azure IoT Edge は、Raspberry Pi3 のような小規模なデバイスから、サーバー グレード ハードウェアまで、幅広いデバイスで快適に動作します。 シナリオに適したハードウェアの選択は、実行するワークロードによって決まります。 デバイスを最終的に決定するまでには複雑なプロセスを要する場合もありますが、従来型のラップトップやデスクトップ上でソリューションのプロトタイプを作成することは簡単に開始できます。

プロトタイプ作成の過程で得られた経験は、最終的なデバイスの選択に役立ちます。 考慮すべき質問としては次のようなものがあります。 

* ワークロード内のモジュールはいくつありますか。
* モジュールのコンテナーが共有するレイヤーはいくつありますか。
* モジュールはどの言語で作成されていますか。 
* モジュールで処理されるデータはどのくらいですか。
* モジュールには、ワークロードを加速するための特殊なハードウェアが必要ですか。
* ソリューションの望ましいパフォーマンス特性はどのようなものですか。
* ハードウェア予算はどのくらいですか。
