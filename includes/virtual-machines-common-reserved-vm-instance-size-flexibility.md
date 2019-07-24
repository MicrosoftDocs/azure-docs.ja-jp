---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 05820cc5f7b7d61d83f73ea5b62b05f8712e0997
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551629"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Reserved VM Instances での仮想マシン サイズの柔軟性

インスタンス サイズの柔軟性に向けて最適化された予約仮想マシン インスタンスを使用して、購入した予約を同じサイズ シリーズのグループ内の仮想マシン (VM) サイズに適用できます。 たとえば、(Standard_DS5_v2 などの) DSv2 シリーズの表に記載されている VM サイズの予約を購入した場合、同じテーブルに記載されているその他の 4 つのサイズに予約割引を適用できます。

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

ただし、別のテーブルに記載されている VM サイズにその予約割引を適用することはできません。たとえば、DSv2 シリーズのハイ メモリの表に記載されている Standard_DS11_v2、Standard_DS12_v2 などです。

サイズ シリーズのグループ内で予約割引が適用される VM の数は、予約を購入するときに選択する VM サイズによって異なります。 また、これは実行させている VM のサイズにも依存します。 次の表に記載されている比率の列では、そのグループ内の各 VM サイズの相対的なフットプリントが比較されています。 比率の値を使用して、実行させている VM に対して予約割引がどのように適用されるのか計算します。

## <a name="examples"></a>例

次の例では、DSv2 シリーズの表にあるサイズと比率を使用します。

 予約仮想マシン インスタンスをサイズ Standard_DS4_v2 で購入するとします。ここで、比率、またはこのシリーズ内の他のサイズと比較した相対的なフットプリントは、8 です。

- シナリオ 1:Standard_DS1_v2 サイズの VM を、比率 1 で 8 つ実行します。 これらの 8 つの VM すべてに、予約割引が適用されます。
- シナリオ 2:Standard_DS2_v2 サイズの VM を、各比率 2 で2 つ実行します。 また、Standard_DS3_v2 サイズの VM を、比率 4 で実行します。 フットプリントの合計は 2 + 2 + 4 = 8 です。 したがって、これらの 3 つの VM すべてに、予約割引が適用されます。
- シナリオ 3:Standard_DS5_v2 を、比率 16 で 1 つ実行します。 その VM のコンピューティング コストの半分に、予約割引が適用されます。

次のセクションでは、インスタンス サイズの柔軟性に向けて最適化された予約 VM インスタンスを購入する際に、同じサイズ シリーズのグループ内にあるサイズを示します。

## <a name="b-series"></a>B シリーズ

| Size | 比率|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

詳しくは、「[負荷の急増に対応できる B シリーズ仮想マシンのサイズ](../articles/virtual-machines/windows/b-series-burstable.md)」をご覧ください。

## <a name="b-series-high-memory"></a>B シリーズのハイ メモリ

| Size | 比率|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

詳しくは、「[負荷の急増に対応できる B シリーズ仮想マシンのサイズ](../articles/virtual-machines/windows/b-series-burstable.md)」をご覧ください。

## <a name="d-series"></a>D シリーズ

| Size | 比率|
|---|---|
| Standard_D1|1|
|Standard_D2|2|
|Standard_D3|4|
|Standard_D4|8|

詳しくは、「[旧世代の仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-previous-gen.md)」をご覧ください。

## <a name="d-series-high-memory"></a>D シリーズのハイ メモリ

| Size | 比率|
|---|---|
| Standard_D11|1|
|Standard_D12|2|
|Standard_D13|4|
|Standard_D14|8|

詳しくは、「[旧世代の仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-previous-gen.md)」をご覧ください。

## <a name="ds-series"></a>DS シリーズ

| Size | 比率|
|---|---|
|Standard_DS1|1|
|Standard_DS2|2|
|Standard_DS3|4|
|Standard_DS4|8|

詳しくは、「[旧世代の仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-previous-gen.md)」をご覧ください。

## <a name="ds-series-high-memory"></a>DS シリーズのハイ メモリ

| Size | 比率|
|---|---|
|Standard_DS11|1|
|Standard_DS12|2|
|Standard_DS13|4|
|Standard_DS14|8|

詳しくは、「[旧世代の仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-previous-gen.md)」をご覧ください。

## <a name="dsv2-series"></a>DSv2 シリーズ

| Size | 比率|
|---|---|
|Standard_DS1_v2|1|
|Standard_DS2_v2|2|
|Standard_DS3_v2|4|
|Standard_DS4_v2|8|
|Standard_DS5_v2|16|

詳しくは、「[旧世代の仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-previous-gen.md)」をご覧ください。

## <a name="dsv2-series-high-memory"></a>DSv2 シリーズのハイ メモリ

| Size | 比率|
|---|---|
|Standard_DS11_v2|1|
|Standard_DS11-1_v2|1|
|Standard_DS12_v2|2|
|Standard_DS12-1_v2|2|
|Standard_DS12-2_v2|2|
|Standard_DS13_v2|4|
|Standard_DS13-2_v2|4|
|Standard_DS13-4_v2|4|
|Standard_DS14_v2|8|
|Standard_DS14-4_v2|8|
|Standard_DS14-8_v2|8|
|Standard_DS15_v2|10|

詳しくは、「[旧世代の仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-previous-gen.md)」をご覧ください。

## <a name="dsv3-series"></a>DSv3 シリーズ

| Size | 比率|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standard_D8s_v3|4|
|Standard_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

詳しくは、「[汎用仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-1)」をご覧ください。

## <a name="dv2-series"></a>Dv2 シリーズ

| Size | 比率|
|---|---|
|Standard_D1_v2|1|
|Standard_D2_v2|2|
|Standard_D3_v2|4|
|Standard_D4_v2|8|
|Standard_D5_v2|16|

詳しくは、「[旧世代の仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-previous-gen.md)」をご覧ください。

## <a name="dv2-series-high-memory"></a>Dv2 シリーズのハイ メモリ

| Size | 比率|
|---|---|
|Standard_D11_v2|1|
|Standard_D12_v2|2|
|Standard_D13_v2|4|
|Standard_D14_v2|8|
|Standard_D15_v2|10|

詳しくは、「[旧世代の仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-previous-gen.md)」をご覧ください。

## <a name="dv3-series"></a>Dv3 シリーズ

| Size | 比率|
|---|---|
| Standard_D2_v3|1|
|Standard_D4_v3|2|
|Standard_D8_v3|4|
|Standard_D16_v3|8|
|Standard_D32_v3|16|
|Standard_D64_v3|32|

詳しくは、「[汎用仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)」をご覧ください。

## <a name="esv3-series"></a>ESv3 シリーズ

| Size | 比率|
|---|---|
|Standard_E2s_v3|1|
|Standard_E4s_v3|2|
|Standard_E4-2s_v3|2|
|Standard_E8s_v3|4|
|Standard_E8-2s_v3|4|
|Standard_E8-4s_v3|4|
|Standard_E16s_v3|8|
|Standard_E16-4s_v3|8|
|Standard_E16-8s_v3|8|
|Standard_E20s_v3|10|
|Standard_E32s_v3|16|
|Standard_E32-8s_v3|16|
|Standard_E32-16s_v3|16|
|Standard_E64s_v3|28.8|
|Standard_E64-16s_v3|28.8|
|Standard_E64-32s_v3|28.8|

詳しくは、「[メモリ最適化済み仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-memory.md#esv3-series)」をご覧ください。

## <a name="ev3-series"></a>Ev3 シリーズ

| Size | 比率|
|---|---|
| Standard_E2_v3|1|
|Standard_E4_v3|2|
|Standard_E8_v3|4|
|Standard_E16_v3|8|
|Standard_E20_v3|10|
|Standard_E32_v3|16|
|Standard_E64_v3|32|

詳しくは、「[メモリ最適化済み仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-memory.md#ev3-series)」をご覧ください。

## <a name="f-series"></a>F シリーズ

| Size | 比率|
|---|---|
| Standard_F1|1|
|Standard_F2|2|
|Standard_F4|4|
|Standard_F8|8|
Standard_F16|16|

詳しくは、「[旧世代の仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-previous-gen.md)」をご覧ください。

## <a name="fs-series"></a>FS シリーズ

| Size | 比率|
|---|---|
| Standard_F1s|1|
|Standard_F2s|2|
|Standard_F4s|4|
|Standard_F8s|8|
|Standard_F16s|16|

詳しくは、「[旧世代の仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-previous-gen.md)」をご覧ください。

## <a name="fsv2-series"></a>Fsv2 シリーズ

| Size | 比率|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

詳しくは、「[コンピューティング最適化済み仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-1)」をご覧ください。

## <a name="h-series"></a>H シリーズ

| Size | 比率|
|---|---|
| Standard_H8|1|
|Standard_H16|2|

詳しくは、「[ハイ パフォーマンス コンピューティング VM のサイズ](../articles/virtual-machines/windows/sizes-hpc.md)」をご覧ください。

## <a name="h-series-high-memory"></a>H シリーズのハイ メモリ

| Size | 比率|
|---|---|
| Standard_H8m|1|
|Standard_H16m|2|

詳しくは、「[ハイ パフォーマンス コンピューティング VM のサイズ](../articles/virtual-machines/windows/sizes-hpc.md)」をご覧ください。

## <a name="ls-series"></a>Ls シリーズ

| Size | 比率|
|---|---|
| Standard_L4s|1|
|Standard_L8s|2|
|Standard_L16s|4|
|Standard_L32s|8|

詳しくは、「[ストレージ最適化済み仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-storage.md)」をご覧ください。

## <a name="m-series"></a>M シリーズ

| Size | 比率|
|---|---|
| Standard_M64s|1|
|Standard_M128s|2|

詳しくは、「[メモリ最適化済み仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-memory.md#m-series)」をご覧ください。

## <a name="m-series-fractional"></a>M シリーズの小数部

| Size | 比率|
|---|---|
| Standard_M16s|1|
|Standard_M32s|2|

詳しくは、「[メモリ最適化済み仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-memory.md#m-series)」をご覧ください。

## <a name="m-series-fractional-high-memory"></a>M シリーズの小数部のハイ メモリ

| Size | 比率|
|---|---|
|Standard_M8ms|1|
|Standard_M8-2ms|1|
|Standard_M8-4ms|1|
|Standard_M16ms|2|
|Standard_M16-4ms|2|
|Standard_M16-8ms|2|
|Standard_M32ms|4|
|Standard_M32-8ms|4|
|Standard_M32-16ms|4|

詳しくは、「[メモリ最適化済み仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-memory.md#m-series)」をご覧ください。

## <a name="m-series-fractional-large"></a>M シリーズの小数部 (大)

| Size | 比率|
|---|---|
| Standard_M32ls|1|
|Standard_M64ls|2|

詳しくは、「[メモリ最適化済み仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-memory.md#m-series)」をご覧ください。

## <a name="m-series-high-memory"></a>M シリーズのハイ メモリ

| Size | 比率|
|---|---|
| Standard_M64ms|1|
|Standard_M64-16ms|1|
|Standard_M64-32ms|1|
|Standard_M128ms|2|
|Standard_M128-32ms|2|
|Standard_M128-64ms|2|

詳しくは、「[メモリ最適化済み仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-memory.md#m-series)」をご覧ください。

## <a name="nc-series"></a>NC シリーズ

| Size | 比率|
|---|---|
| Standard_NC6|1|
|Standard_NC12|2|
|Standard_NC24|4|

詳しくは、「[GPU 最適化済み仮想マシンのサイズ](../articles/virtual-machines/windows/sizes-gpu.md)」をご覧ください。

## <a name="ncv2-series"></a>NCv2 シリーズ

| Size | 比率|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

詳しくは、「[GPU 最適化済み仮想マシンのサイズ](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series)」をご覧ください。

## <a name="ncv3-series"></a>NCv3 シリーズ

| Size | 比率|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

詳しくは、「[GPU 最適化済み仮想マシンのサイズ](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series)」をご覧ください。

## <a name="nd-series"></a>ND シリーズ

| Size | 比率|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

詳しくは、「[GPU 最適化済み仮想マシンのサイズ](../articles/virtual-machines/windows//sizes-gpu.md#nd-series)」をご覧ください。

## <a name="nv-series"></a>NV シリーズ

| Size | 比率|
|---|---|
| Standard_NV6|1|
|Standard_NV12|2|
|Standard_NV24|4|

詳しくは、「[GPU 最適化済み仮想マシンのサイズ](../articles/virtual-machines/windows//sizes-gpu.md#nv-series)」をご覧ください。


