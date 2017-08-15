---
ms.date: 2017-06-05
keywords: powershell,cmdlet
title: "使用选项卡扩展"
ms.assetid: c8730471-bf6a-43b8-ab1d-f9ef5a74f04e
ms.openlocfilehash: c158e544d79bf6010690160eea71630a1981e8a5
ms.sourcegitcommit: 598b7835046577841aea2211d613bb8513271a8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2017
---
# <a name="using-tab-expansion"></a><span data-ttu-id="456ff-103">使用选项卡扩展</span><span class="sxs-lookup"><span data-stu-id="456ff-103">Using Tab Expansion</span></span>
<span data-ttu-id="456ff-104">Command-line Shell 通常提供一种方法来自动完成长文件或命令的名称，从而加快命令输入并给出提示。</span><span class="sxs-lookup"><span data-stu-id="456ff-104">Command-line shells often provide a way to complete the names of long files or commands automatically, speeding up command entry and providing hints.</span></span> <span data-ttu-id="456ff-105">Windows PowerShell 允许通过按下 **Tab** 键来填充文件名称和 cmdlet 名称。</span><span class="sxs-lookup"><span data-stu-id="456ff-105">Windows PowerShell allows you to fill in file names and cmdlet names by pressing the **Tab** key.</span></span>

> [!NOTE]
> <span data-ttu-id="456ff-106">选项卡扩展由内部函数 TabExpansion 或 TabExpansion2 控制。</span><span class="sxs-lookup"><span data-stu-id="456ff-106">Tab expansion is controlled by the internal function TabExpansion or TabExpansion2.</span></span> <span data-ttu-id="456ff-107">由于此函数可被修改或覆盖，所以此讨论可用作针对默认 Windows PowerShell 配置的行为的指导。</span><span class="sxs-lookup"><span data-stu-id="456ff-107">Since this function can be modified or overridden, this discussion is a guide to the behavior of the default Windows PowerShell configuration.</span></span>

<span data-ttu-id="456ff-108">若要从可用选择中自动填充文件名或路径，键入部分名称并按下 **Tab** 键。</span><span class="sxs-lookup"><span data-stu-id="456ff-108">To fill in a filename or path from the available choices automatically, type part of the name and press the **Tab** key.</span></span> <span data-ttu-id="456ff-109">Windows PowerShell 将自动将名称扩展至找到的第一个匹配项。</span><span class="sxs-lookup"><span data-stu-id="456ff-109">Windows PowerShell will automatically expand the name to the first match that it finds.</span></span> <span data-ttu-id="456ff-110">重复按下 **Tab** 将循环浏览所有可用选择。</span><span class="sxs-lookup"><span data-stu-id="456ff-110">Pressing the **Tab** key repeatedly will cycle through all of the available choices.</span></span>

<span data-ttu-id="456ff-111">cmdlet 名称的选项卡扩展稍有不同。</span><span class="sxs-lookup"><span data-stu-id="456ff-111">The tab expansion of cmdlet names is slightly different.</span></span> <span data-ttu-id="456ff-112">若要在 cmdlet 名称上使用选项卡扩充，键入名称的整个第一部分（即谓词），然后是后跟的连字符。</span><span class="sxs-lookup"><span data-stu-id="456ff-112">To use tab expansion on a cmdlet name, type the entire first part of the name (the verb) and the hyphen that follows it.</span></span> <span data-ttu-id="456ff-113">你可以填入名称的更多内容以进行部分匹配。</span><span class="sxs-lookup"><span data-stu-id="456ff-113">You can fill in more of the name for a partial match.</span></span> <span data-ttu-id="456ff-114">例如，如果你键入 **get-co** 然后按下 **Tab** 键，Windows PowerShell 将自动将其扩展为 **Get-Command** cmdlet（注意，还会将字母的大小写改为标准形式）。</span><span class="sxs-lookup"><span data-stu-id="456ff-114">For example, if you type **get-co** and then press the **Tab** key, Windows PowerShell will automatically expand this to the **Get-Command** cmdlet (notice that it also changes the case of letters to their standard form).</span></span> <span data-ttu-id="456ff-115">如果你再次按下 **Tab**，Windows PowerShell 会将此替换为另一个唯一的匹配的 cmdlet 名称，**Get-Content**。</span><span class="sxs-lookup"><span data-stu-id="456ff-115">If you press **Tab** key again, Windows PowerShell replaces this with the only other matching cmdlet name, **Get-Content**.</span></span>

<span data-ttu-id="456ff-116">你可以在同一行上重复使用选项卡扩展。</span><span class="sxs-lookup"><span data-stu-id="456ff-116">You can use tab expansion repeatedly on the same line.</span></span> <span data-ttu-id="456ff-117">例如，你可以通过输入以下内容在 **Get-Content** cmdlet 的名称上使用选项卡扩展：</span><span class="sxs-lookup"><span data-stu-id="456ff-117">For example, you can use tab expansion on the name of the **Get-Content** cmdlet by entering:</span></span>

```
PS> Get-Con<Tab>
```

<span data-ttu-id="456ff-118">当你按下 **Tab** 键时，该命令扩展为：</span><span class="sxs-lookup"><span data-stu-id="456ff-118">When you press the **Tab** key, the command expands to:</span></span>

```
PS> Get-Content
```

<span data-ttu-id="456ff-119">然后你可以部分指定“智能安装”日志文件的路径并再次使用选项卡扩展：</span><span class="sxs-lookup"><span data-stu-id="456ff-119">You can then partially specify the path to the Active Setup log file and use tab expansion again:</span></span>

```
PS> Get-Content c:\windows\acts<Tab>
```

<span data-ttu-id="456ff-120">当你按下 **Tab** 键时，该命令扩展为：</span><span class="sxs-lookup"><span data-stu-id="456ff-120">When you press the **Tab** key, the command expands to:</span></span>

```
PS> Get-Content C:\windows\actsetup.log
```

> [!NOTE]
> <span data-ttu-id="456ff-121">选项卡扩展进程的一个限制是选项卡始终解释为尝试完成单词。</span><span class="sxs-lookup"><span data-stu-id="456ff-121">One limitation of the tab expansion process is that tabs are always interpreted as attempts to complete a word.</span></span> <span data-ttu-id="456ff-122">如果你将命令示例复制并粘贴到 Windows PowerShell 控制台，请确保该示例不包含选项卡；如果包含选项卡，则结果不可预测并且几乎不会得到你预期的结果。</span><span class="sxs-lookup"><span data-stu-id="456ff-122">If you copy and paste command examples into a Windows PowerShell console, make sure that the sample does not contain tabs; if it does, the results will be unpredictable and will almost certainly not be what you intended.</span></span>
