---
title: 为自定义语音识别准备数据 - 语音服务
titleSuffix: Azure Cognitive Services
description: 在测试 Microsoft 语音识别的准确性或训练自定义模型时，需要音频和文本数据。 本页介绍数据的类型、用法及其管理方式。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 605bae706bbc1db2e008b8d050cbba9eacd16933
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98702196"
---
# <a name="prepare-data-for-custom-speech"></a>准备自定义语音识别的数据

在测试 Microsoft 语音识别的准确性或训练自定义模型时，需要音频和文本数据。 在本页中，我们将介绍自定义语音模型所需的数据的类型。

## <a name="data-diversity"></a>数据多样性

用来测试和训练自定义模型的文本和音频需要包含你的模型需要识别的来自各种说话人和场景的示例。
收集进行自定义模型测试和训练所需的数据时，请考虑以下因素：

* 你的文本和语音音频数据需要涵盖用户在与你的模型互动时所用的各种语言陈述。 例如，一个能升高和降低温度的模型需要针对人们在请求进行这种更改时会用的陈述进行训练。
* 你的数据需要包含模型需要识别的所有语音变型。 许多因素可能会改变语音，包括口音、方言、语言混合、年龄、性别、语音音调、紧张程度和当日时间。
* 你包括的示例必须来自使用模型时所在的各种环境（室内、户外、公路噪音）。
* 必须使用生产系统将要使用的硬件设备来收集音频。 如果你的模型需要识别在不同质量的录音设备上录制的语音，则你提供的用来训练模型的音频数据也必须能够代表这些不同的场景。
* 以后可以向模型中添加更多数据，但要注意使数据集保持多样性并且能够代表你的项目需求。
* 将不在你的自定义模型识别需求范围内的数据包括在内可能会损害整体识别质量，因此请不要包括你的模型不需要转录的数据。

基于部分场景训练的模型只能在这些场景中很好地执行。 请仔细选择能够代表你要求自定义模型识别的全部场景范围的数据。

> [!TIP]
> 请从与模型会遇到的语言和声效相匹配的较小的示例数据集着手。
> 例如，可以采用与模型的生产方案相同的硬件和声效环境录制一小段有代表性的示例音频。
> 具有代表性的数据的小型数据集可能会在你投入精力收集大得多的数据集进行训练之前暴露一些问题。

## <a name="data-types"></a>数据类型

下表列出了接受的数据类型、何时使用每种数据类型，以及建议的数量。 创建模型不一定要用到每种数据类型。 数据要求根据是要创建测试还是训练模型而异。

| 数据类型 | 用于测试 | 建议的数量 | 用于训练 | 建议的数量 |
|-----------|-----------------|----------|-------------------|----------|
| [音频：](#audio-data-for-testing) | 是<br>用于视觉检测 | 5 个以上的音频文件 | 否 | 空值 |
| [音频和人为标记的听录内容](#audio--human-labeled-transcript-data-for-testingtraining) | 是<br>用于评估准确度 | 0.5-5 小时的音频 | 是 | 1-20 小时的音频 |
| [相关文本](#related-text-data-for-training) | 否 | 不适用 | 是 | 1-200 MB 的相关文本 |

训练新模型时，请从[相关文本](#related-text-data-for-training)开始。 这些数据将改善对特殊术语和短语的识别。 利用文本定型比) 的音频 (的培训速度快得多。

文件应按类型分组成数据集，并作为 .zip 文件上传。 每个数据集只能包含一种数据类型。

> [!TIP]
> 若要快速开始使用，请考虑使用示例数据。 请参阅此 GitHub 存储库，了解<a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">自定义语音识别数据示例<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="upload-data"></a>上传数据

若要上传数据，请导航到<a href="https://speech.microsoft.com/customspeech" target="_blank">自定义语音识别门户<span class="docon docon-navigate-external x-hidden-focus"></span></a>。 在门户中，单击“上传数据”启动向导并创建第一个数据集。 在上传数据之前，系统会要求你为数据集选择语音数据类型。

![屏幕截图，突出显示了语音门户中的“音频上传”选项。](./media/custom-speech/custom-speech-select-audio.png)

上传的每个数据集必须符合所选数据类型的要求。 必须先将数据设置为正确格式再上传它。 格式正确的数据可确保自定义语音识别服务对其进行准确处理。 以下部分列出了要求。

上传数据集后，可以使用几个选项：

* 可以导航到“测试”选项卡，并直观地查看仅包含音频的数据，或同时包含音频和人为标记的听录内容的数据。
* 可以导航到“训练”选项卡，并使用音频和人为听录数据或相关文本数据来训练自定义模型。

## <a name="audio-data-for-testing"></a>用于测试的音频数据

音频数据最适合用于测试 Microsoft 基线语音转文本模型或自定义模型的准确度。 请记住，音频数据用于检查语音的准确度，反映特定模型的性能。 若要量化模型的准确度，请使用[音频和人为标记的听录数据](#audio--human-labeled-transcript-data-for-testingtraining)。

参考下表来确保正确设置用于自定义语音识别的音频文件的格式：

| 属性                 | 值                 |
|--------------------------|-----------------------|
| 文件格式              | RIFF (WAV)            |
| 采样速率              | 8,000 Hz 或 16,000 Hz |
| 声道                 | 1（单音）              |
| 每个音频的最大长度 | 2 小时               |
| 示例格式            | PCM，16 位           |
| 存档格式           | .zip                  |
| 最大存档大小     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> 上传训练和测试数据时，.zip 文件大小不能超过 2 GB。 如果需要更多数据来进行训练，请将其划分为多个 .zip 文件并分别上传。 稍后，可选择从多个数据集进行训练。 但是，只能从单个数据集进行测试。

使用 <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX<span class="docon docon-navigate-external x-hidden-focus"></span></a> 来验证音频属性，或将现有音频转换为适当的格式。 下面这些示例演示如何通过 SoX 命令行完成其中的每个活动：

| 活动 | 说明 | SoX 命令 |
|----------|-------------|-------------|
| 检查音频格式 | 使用此命令检查<br>音频文件格式。 | `sox --i <filename>` |
| 转换音频格式 | 使用此命令<br>将音频文件转换为单声道 16 位 16 KHz。 | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>用于测试/训练的音频和人为标记的听录数据

若要在处理音频文件时测量 Microsoft 语音转文本的准确度，必须提供人为标记的听录内容（逐字对照）进行比较。 尽管人为标记的听录往往很耗时，但有必要评估准确度并根据用例训练模型。 请记住，识别能力的改善程度以提供的数据质量为界限。 出于此原因，只能上传优质的听录内容，这一点非常重要。

音频文件在录音开始和结束时可以保持静音。 如果可能，请在每个示例文件中的语音前后包含至少半秒的静音。 录音音量小或具有干扰性背景噪音的音频没什么用，但不应损害你的自定义模型。 收集音频示例之前，请务必考虑升级麦克风和信号处理硬件。

| 属性                 | 值                               |
|--------------------------|-------------------------------------|
| 文件格式              | RIFF (WAV)                          |
| 采样速率              | 8,000 Hz 或 16,000 Hz               |
| 声道                 | 1（单音）                            |
| 每个音频的最大长度 | 2 小时（测试）/ 60 秒（训练） |
| 示例格式            | PCM，16 位                         |
| 存档格式           | .zip                                |
| 最大 zip 大小         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> 上传训练和测试数据时，.zip 文件大小不能超过 2 GB。 只能从单个数据集进行测试，请确保将其保持在适当的文件大小内。 另外，每个训练文件不能超过 60 秒，否则将出错。

若要解决字词删除或替换等问题，需要提供大量的数据来改善识别能力。 通常，我们建议为大约 10 到 20 小时的音频提供逐字对照的听录。 应在单个纯文本文件中包含所有 WAV 文件的听录。 听录文件的每一行应包含一个音频文件的名称，后接相应的听录。 文件名和听录应以制表符 (\t) 分隔。

例如：

<!-- The following example contains tabs. Don't accidentally convert these into spaces. -->

```input
speech01.wav    speech recognition is awesome
speech02.wav    the quick brown fox jumped all over the place
speech03.wav    the lazy dog was not amused
```

> [!IMPORTANT]
> 听录应编码为 UTF-8 字节顺序标记 (BOM)。

听录内容应经过文本规范化，以便可由系统处理。 但是，将数据上传到 Speech Studio 之前，必须完成一些重要的规范化操作。 有关在准备听录内容时可用的适当语言，请参阅[如何创建人为标记的听录内容](how-to-custom-speech-human-labeled-transcriptions.md)

收集音频文件和相应的听录内容后，应将其打包成单个 .zip 文件，然后上传到<a href="https://speech.microsoft.com/customspeech" target="_blank">自定义语音识别门户<span class="docon docon-navigate-external x-hidden-focus"></span></a>。 下面是一个示例数据集，其中包含三个音频文件和一个人为标记的听录文件：

> [!div class="mx-imgBorder"]
> ![从语音门户选择音频](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

有关语音服务订阅的建议区域列表，请参阅[设置 Azure 帐户](custom-speech-overview.md#set-up-your-azure-account)。 在这些区域之一中设置语音订阅将减少训练模型所需的时间。 在这些区域中，培训每日可以处理大约10小时的音频，而不是在其他地区每天一小时处理一次。 如果无法在一周内完成模型训练，则该模型将标记为 "失败"。

并非所有基本模型都支持音频数据培训。 如果基本模型不支持该模型，则该服务将忽略音频，并只训练转录的文本。 在这种情况下，训练将与相关文本定型相同。

## <a name="related-text-data-for-training"></a>用于训练的相关文本数据

唯一的产品名称或功能应包含用于训练的相关文本数据。 相关文本有助于确保正确识别。 可以提供两种类型的相关文本数据来改善识别能力：

| 数据类型 | 这些数据如何改善识别能力 |
|-----------|------------------------------------|
| 句子（言语） | 在识别句子上下文中的产品名称或行业特定的词汇时，可以提高准确度。 |
| 发音 | 改善不常见字词、缩写词或其他未定义发音的单词的发音。 |

可将言语作为单个或多个文本文件提供。 若要提高准确性，请使用较接近预期口头言语的文本数据。 应以单个文本文件的形式提供发音。 可将所有内容打包成单个 zip 文件，并上传到<a href="https://speech.microsoft.com/customspeech" target="_blank">自定义语音识别门户<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

相关文本的定型通常在几分钟内完成。

### <a name="guidelines-to-create-a-sentences-file"></a>有关创建句子文件的指导原则

若要使用句子的自定义模型，需要提供示例言语表。 言语不一定要是完整的或者语法正确的，但必须准确反映生产环境中预期的口头输入。 如果想要增大某些字词的权重，可添加包含这些特定字词的多个句子。

一般原则是，训练文本越接近生产环境中预期的实际文本，模型适应越有效。 应在训练文本中包含要增强的行话和短语。 如果可能，尽量将一个句子或关键字控制在单独的一行中。 对于重要的关键字和短语（例如产品名），可以将其复制几次。 但请记住，不要复制太多次，这可能会影响总体识别率。

参考下表来确保正确设置言语相关数据文件的格式：

| 属性 | 值 |
|----------|-------|
| 文本编码 | UTF-8 BOM |
| 每行的话语数 | 1 |
| 文件大小上限 | 200 MB |

此外，还需要考虑以下限制：

* 避免重复字符、单词或单词组三次以上。 例如： "aaaa"、"是"、"是" 或 "这就是它"。 语音服务可能会删除太多重复的行。
* 请勿使用特殊字符或编码在 `U+00A1` 以后的 UTF-8 字符。
* 将会拒绝 URI。

### <a name="guidelines-to-create-a-pronunciation-file"></a>有关创建发音文件的指导原则

如果用户会遇到或使用没有标准发音的不常见字词，你可以提供自定义发音文件来改善识别能力。

> [!IMPORTANT]
> 建议不要使用自定义发音文件来改变常用字的发音。

下面提供了口述言语的示例，以及每个言语的自定义发音：

| 识别/显示的形式 | 口头形式 |
|--------------|--------------------------|
| 3CPO | three c p o |
| CNTK | c n t k |
| IEEE | i triple e |

口述形式是拼写的拼音顺序。它可以由字母、单词、音节或三者的组合构成。

自定义发音适用于英语 (`en-US`) 和德语 (`de-DE`)。 下表按语言显示了支持的字符：

| 语言 | Locale | 字符 |
|----------|--------|------------|
| 英语 | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| 德语 | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

参考下表来确保相关发音数据文件的格式正确。 发音文件较小，应只占几千字节。

| 属性 | 值 |
|----------|-------|
| 文本编码 | UTF-8 BOM（英语还支持 ANSI） |
| 每行的发音数目 | 1 |
| 文件大小上限 | 1 MB（在免费层中为 1 KB） |

## <a name="next-steps"></a>后续步骤

* [检查数据](how-to-custom-speech-inspect-data.md)
* [评估数据](how-to-custom-speech-evaluate-data.md)
* [训练模型](how-to-custom-speech-train-model.md)
* [部署模型](./how-to-custom-speech-train-model.md)