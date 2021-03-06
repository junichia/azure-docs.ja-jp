---
title: Azure Media Services のライブ イベントとクラウド DVR | Microsoft Docs
description: この記事では、ライブ出力の概要とクラウド DVR の使用方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: a10c76dd7fb4ef1e9a45666ff3a3ca0d937d2c94
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231231"
---
# <a name="using-a-cloud-digital-video-recorder-dvr"></a>クラウドにおけるデジタル ビデオ レコーダー (DVR) の使用

Azure Media Services の[ライブ出力](https://docs.microsoft.com/rest/api/media/liveoutputs)オブジェクトは、ライブ ストリームをキャッチして Media Services アカウントのアセットに記録するデジタル ビデオ レコーダーのようなものです。 記録されたコンテンツは、[アセット](https://docs.microsoft.com/rest/api/media/assets) リソースによって定義されたコンテナーに保持されます (コンテナーはアカウントに接続されている Azure Storage アカウントにあります)。 また、ライブ出力を使用すると、アーカイブの記録に残すストリームの量 (たとえば、クラウド DVR の容量) や視聴者がライブ ストリームの視聴を開始することができるかどうかなど、送信ライブ ストリームのいくつかのプロパティを制御することもできます。 ディスク上のアーカイブは、ライブ出力の **archiveWindowLength** プロパティで指定されているコンテンツ量のみを保持する循環アーカイブ "期間" です。 このウィンドウの範囲外のコンテンツは、ストレージ コンテナーから自動的に破棄され、復旧できません。 archiveWindowLength 値は、ISO-8601 形式で表した期間 (PTHH:MM:SS など) であり、DVR の容量を指定し、最短で 3 分から最長で 25 時間まで設定できます。

ライブ イベントとそのライブ出力との関係は、従来のテレビの放送と似ており、チャンネル (ライブ イベント) はビデオの連続したストリームを表し、録画 (ライブ出力) は特定の時間セグメント (たとえば、午後 6 時 30 分から午後 7 時 00 分までの夕方のニュース) を対象としています。 ストリームがライブ イベントに流れ始めると、アセット、ライブ出力、ストリーミング ロケーターを作成することにより、ストリーミング イベントを開始できます。 ライブ出力により、ストリームがアーカイブされ、[ストリーミング エンドポイント](https://docs.microsoft.com/rest/api/media/streamingendpoints)を介して視聴者がストリームを使用できるようになります。 1 つのライブ イベントに、アーカイブの長さと設定の異なる複数のライブ出力 (最大 3 つ) を作成できます。 ライブ ストリーミング ワークフローの詳細については、「[一般的な手順](live-streaming-overview.md#general-steps)」のセクションを参照してください。

## <a name="using-a-dvr-during-an-event"></a>イベント中の DVR の使用 

このセクションでは、イベント中に DVR を使用して、"巻き戻し" 機能を使用できるストリームの部分を制御する方法について説明します。

archiveWindowLength 値で、視聴者が現在のライブ位置からどのくらい戻ることができるかが決まります。 archiveWindowLength で、クライアント マニフェストが拡大できる期間も決まります。

たとえば、フットボールの試合をストリーミングしていて、ArchiveWindowLength がわずか 30 分だとします。 試合の開始から 45 分にイベントの視聴を開始した視聴者は、最高で 15 分の時点まで戻ることができます。 試合のライブ出力はライブ イベントが停止するまで継続されますが、archiveWindowLength の範囲から外れるコンテンツは連続的にストレージから破棄され、回復できません。 この例では、イベントの開始から 15 分までのビデオは DVR とアセットの BLOB ストレージ内のコンテナーから消去されています。 このアーカイブは回復できず、Azure BLOB ストレージ内のコンテナーから削除されます。

ライブ イベントではライブ出力の同時実行が最大 3 つまでサポートされます (1 つのライブ ストリームから最大 3 つの記録/アーカイブを同時に作成できます)。 これにより、1 つのイベントのさまざまな部分を必要に応じて発行したりアーカイブしたりできます。 たとえば、24 時間 365 日のライブ リニア フィードを配信し、顧客が見逃したコンテンツをオンデマンド視聴できるよう、終日プログラムの "記録" を作成するとします。 このシナリオではまず、プライマリ ライブ出力を、1 時間以下の短いアーカイブ ウィンドウで作成します。これは、視聴者が受信するプライマリ ライブ ストリームです。 このライブ出力のストリーミング ロケーターを作成し、アプリケーションまたは Web サイトに "ライブ" フィードとして発行することになります。 ライブ イベントの実行中、同時実行される 2 つ目のライブ出力を、プログラムの開始 (または後でトリミングしやすいよう 5 分前) の時点でプログラムから作成できます。 この 2 つ目のライブ出力は、プログラムが終了して 5 分後に削除できます。 この 2 つ目のアセットを使用すると、新しいストリーミング ロケーターを作成して、アプリケーションのカタログでオンデマンド アセットとしてこのプログラムを公開することができます。 オンデマンド ビデオとして共有したい他のプログラム範囲やハイライトについても、このプロセスを繰り返します。このプロセスはすべて、1 つ目のライブ出力の "ライブ" フィードがリニア フィードを配信し続ける最中に行うことができます。 

## <a name="creating-an-archive-for-on-demand-playback"></a>オンデマンド再生用のアーカイブの作成

ライブ出力がアーカイブされるアセットは、ライブ出力が削除されると、自動的にオンデマンド アセットになります。 ライブ イベントを停止するには、すべてのライブ出力を削除する必要があります。 省略可能なフラグ [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) を使用すると、停止時にライブ出力を自動的に削除できます。 

イベントを停止して削除した後も、アセットを削除していなければ、アーカイブ済みコンテンツをオンデマンドでのビデオとしてストリーミングできます。 イベントがアセットを使用している場合はアセットを削除しないでください。まずイベントを削除する必要があります。

ストリーミング ロケーターを使用してライブ出力のアセットを発行した場合、ライブ イベント (DVR ウィンドウの長さまで) は、ストリーミング ロケーターの有効期限まで、または削除するまで、どちらか早い方のタイミングまで引き続き表示できます。

詳細については、次を参照してください。

- [ライブ ストリーミングの概要](live-streaming-overview.md)
- [ライブ ストリーミングのチュートリアル](stream-live-tutorial-with-api.md)

> [!NOTE]
> ライブ出力を削除しても、基になるアセットとアセット内のコンテンツは削除されません。 

## <a name="next-steps"></a>次の手順

* [ビデオをサブクリップします](subclip-video-rest-howto.md)。
* [アセットのフィルターを定義します](filters-dynamic-manifest-rest-howto.md)。
