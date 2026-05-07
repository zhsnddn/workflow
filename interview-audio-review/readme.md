# 前言

失业选手的idea，最近几个月一直在寻找实习，但是一直无果，面试经验积累了一些，但是面试复盘是一个低效的状态，其中几点原因：一是需要重复去听录音（这里不是指一次不听，不然无法发现问题），二是就像用ai自己cr自己的代码一样不能很好的发现自己的问题，最近codex更新了computer use体验下来十分好用，现在也是发现了他适合的场景，所有做了一个codex + obsidain的复盘工作流来复盘自己的面试情况。

# 需求分析

我最开始的想法就是把我面试的录音整理一下提取出面试官的问题，随着不断思考我觉得是以下几点需求和难点

1.  面试的录音转文字

往往需要openai-whisper这样的模型之类的东西，且不说需要花钱购买api或者自己部署模型，他的准确度不见得非常高。这个时候通过icloud同步过来的语音备忘录的录音可以直接看到转文字版本，这是十分契合第一个难点，通过computer-use的准确查找，直接提取关键问题，看一下他找录音的截图，只能说非常准确了

<img width="769" height="187" alt="image" src="https://github.com/user-attachments/assets/66ad0464-1a49-446b-ba80-1900e2c0cea2" />


2.  面试语气以及改进分析

这就没啥好说的了，拿到文字随便找个模型都可以直接好了，只要写好提示词去掉口语重复、停顿和无意义表达，把问题规范化

3.  分类面试官问题

为了搞懂自己的问题出在哪里还有哪些问题是高频问题，在提取问题的时候做好归一化处理，然后通过obsidian的dataview插件进行自动排序，每次只需要往对应文件夹去添加问题md，直接自动自动排序

# 最终效果

最后烧掉了一周的20%token终于总结出了一个目前看来算是可用的1.0版本
效果就是输入总结录音：

\*\*- question.md

<img width="792" height="727" alt="image" src="https://github.com/user-attachments/assets/38d2e020-297a-4aeb-8b99-5f4eba3cb7c7" />


\*\*-review\.md

<img width="736" height="782" alt="image" src="https://github.com/user-attachments/assets/dc0c7ead-7fd7-46d1-8a9f-a99ae6452402" />

一堆对应的question文件和interview-question-stats.md

<img width="736" height="782" alt="image" src="https://github.com/user-attachments/assets/75d0f2dd-4087-4773-bcef-b57d1a526793" />
