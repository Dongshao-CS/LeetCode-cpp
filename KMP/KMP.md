---
link: https://juejin.cn/post/7202796308608254011
title: KMP算法学习
description: 最近在做字符串匹配类问题的时候接触到了KMP算法，这个算法比较难，而且很多讲解没有讲清楚背后的原理，读者只能做到知其然而不知其所以然。这篇文章是我自己对KMP算法的一点理解，并且附上了c++代码的实现
keywords: 算法
author: 首页 首页 沸点 课程 直播 活动 竞赛 商城 App 插件 搜索历史 清空 创作者中心 写文章发沸点写笔记写代码草稿箱 会员 登录
date: 2023-02-22T03:59:32.000Z
publisher: 稀土掘金
---
# KMP算法学习

## 前言

最近在做字符串匹配类问题的时候接触到了KMP算法，这个算法比较难，于是搜了一下讲解，但基本上都是讲如何做题如何写代码的，没有讲清楚背后的原理，只能做到知其然而不知其所以然。我自己花了一些时间，把我自己对KMP算法的一些理解记录于此，希望这个世界上能少一个被KMP摧残的孩子

## kmp算法介绍

KMP算法是一种 **字符串匹配**算法，可以在 O(n+m) 的时间复杂度内实现两个字符串的匹配，最经典的就是用来实现实现strStr()函数。

所谓字符串匹配，是这样一种问题："字符串 P 是否为字符串 S 的子串？如果是，它出现在 S 的哪些位置？" 其中 S 称为 **主串**；P 称为 **模式串**。

## 字符串问题的暴力解法

### 暴力解法的思路

要想真正搞懂KMP算法，还是得从最原始的暴力解法开始入手。

现在举一个例子：对主串 "AAAAAABC" 和模式串 "AAAB" 做匹配。

暴力解法就是最朴素的思想，从前往后逐字符比较，一旦遇到不相同的字符，就返回False；如果两个字符串都结束了，仍然没有出现不对应的字符，则返回True。示意图如下：

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d0a0c96f681348b5ac2196a12fdde695~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.image?) c++代码如下：

```int
        int n = s.size(), m = p.size();
        for(int i = 0; i 复制代码
```

现在讨论暴力解法的时间复杂度。记n和m分别是字符串s和p的长度，很容易看出，暴力解法的时间复杂度是O(mn)的。

### 暴力解法的改进思路

暴力解法的速度很慢，因为它需要从前往后比较O(n）趟才能比较出结果，如下图所示：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/71707a8c43724d3397f9aa9f7cb98aac~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.image?)

我们不可能去降低字符串比较本身的复杂度，因为如果要比较两个字符串是否相等，我们真的只能去挨个比较每个字符。

所以，降低时间复杂度的思路就是，看看能不能降低比较的"趟数"。

要优化一个算法，首先要看已经掌握了什么信息，利用已知的信息来做优化， **尽可能利用残余的信息，是KMP算法的思想所在**。

在暴力解法当中，如果从 S[i] 开始的那一趟比较失败了，算法会直接开始尝试从 S[i+1] 开始比较这种行为，属于典型的"没有从之前的错误中学到东西"。我们应当注意到，一次失败的匹配，会给我们提供宝贵的信息——如果 S[i : i+len(P)] 与 P 的匹配是在第 r 个位置失败的，那么从 S[i] 开始的 (r-1) 个连续字符，一定与 P 的前 (r-1) 个字符一模一样！ 如下图所示：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f6c46d53eb334137b234ac2656903c99~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.image)

也就是说，每次失败都会给我们带来一点信息：主串的某一个子串等于模式串的某一个前缀，而KMP算法就是要利用好这个信息，然后来做优化。

刚刚提到了，优化暴力解法的思路就是减少比较的趟数。在暴力比较的过程中，有些比较是有可能成功的，有些则绝无可能。 **如果我们能跳过那些绝无可能的比较趟数**，把比较的趟数降低到一定程度，时间复杂度可能就降下来了。

## KMP算法思路

假设一个例子，模式串是"abcabd"，然后和主串在p[5]开始失配。 既然是在 P[5] 失配的，那么说明 S[0:5] 等于 P[0:5]，即"abcab". 现在我们来考虑：从 S[1]、S[2]、S[3] 开始的匹配尝试，有没有可能成功？
从 S[1] 开始肯定没办法成功，因为 S[1] = P[1] = 'b'，和 P[0] 并不相等。从 S[2] 开始也是没戏的，因为 S[2] = P[2] = 'c'，并不等于P[0]. 但是从 S[3] 开始是有可能成功的。既然有可能，就可以试一试。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/df25f0f614c947b4a35ede17a0a9eb5e~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.image?)

带着"跳过不可能成功的尝试"的思想，我们来看[next数组]。

### next数组的意义

我们先来了解一下什么是前缀和后缀： **前缀是指不包含最后一个字符的所有以第一个字符开头的连续子串**。而后缀是指 **以最后一个字符结尾，但是不包含第一个字符的所有连续子串**。

next数组是对于模式串而言的。P 的 [next 数组]定义为：next[i] 表示 P[0] ~ P[i] 这一个子串，使得 **前k个字符**恰等于 **后k个字符** 的最大的k。特别地，k不能取i + 1,也就是说前缀不能包含最后一个字符，后缀不能包含第一个字符。

如果把模式串视为一把标尺，在主串上移动，那么暴力解法就是每次失配之后只右移一位；改进算法则是 **每次失配之后，移很多位**，跳过那些不可能匹配成功的位置。 **但是该如何确定要移多少位呢？** 就是next数组要干的事情。

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bcac5e7061a2480caf1c4ffae075ace9~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.image?)

如上图：在 S[0] 尝试匹配，失配于 S[3] ！= p[3]之后，我们直接把模式串往右移了两位，让 S[3] 对准 P[1]. 接着继续匹配，失配于 S[8] != P[6], 接下来我们把 P 往右平移了三位，把 S[8] 对准 P[3]. 此后继续匹配直到成功。

那么如何移动模式串这把标尺呢？如图所示，可以看出，移动模式串的时候，要保证模式串旧的后缀要与新的前缀一致，否则就肯定匹配不上了。

回忆next数组的性质：P[0] 到 P[i] 这一段子串中，前next[i]个字符与后next[i]个字符一模一样。既然如此，如果失配在 P[r], 那么P[0]~P[r-1]这一段里面， **前next[r-1]个字符恰好和后next[r-1]个字符相等**——也就是说，我们可以拿长度为 next[r-1] 的那一段前缀，来顶替当前后缀的位置，让匹配继续下去！

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8b6e36a5646e413282d79e38767b49d2~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.image?)

可以观察上面的手绘示意图，绿色部分是成功匹配，失配于红色部分。深绿色手绘线条标出了相等的前缀和后缀， **其长度为next[右端]** . 由于[手绘线条](https://link.juejin.cn?target=https%3A%2F%2Fwww.zhihu.com%2Fsearch%3Fq%3D%25E6%2589%258B%25E7%25BB%2598%25E7%25BA%25BF%25E6%259D%25A1%26search_source%3DEntity%26hybrid_search_source%3DEntity%26hybrid_search_extra%3D%257B%2522sourceType%2522%253A%2522answer%2522%252C%2522sourceId%2522%253A%25221032665486%2522%257D "https://www.zhihu.com/search?q=%E6%89%8B%E7%BB%98%E7%BA%BF%E6%9D%A1&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%221032665486%22%7D")部分的字符是一样的，所以直接把前面那条移到后面那条的位置。因此说， **next数组为我们如何移动标尺提供了依据**。

### next数组的求解

好了，说了这么多，那么next数组到底该怎么求解呢？

快速构建next数组是kmp算法的精髓所在，核心思想就是 **"P自己与自己做匹配"**。

再来回顾一下next数组的定义：

* 定义 "k-前缀" 为一个字符串的前 k 个字符； "k-后缀" 为一个字符串的后 k 个字符。k 必须小于字符串长度。
* next[x] 定义为： P[0]~P[x] 这一段字符串，使得 **k-前缀恰等于k-后缀**的最大的k.

我们考虑采用[递推]的方式求出next数组。如果next[0], next[1], ... next[x-1]均已知，如何求出next[x]呢？

这个需要分类讨论：

**情况1：** P[x]和P[ next[x - 1] ]相等。如下图所示：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aa26bba8a97446108e6b4ea98d7c113d~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.image?)

此时，最长相等前后缀的长度就可以拓展一位，显然，next[x] = next[x - 1] + 1。

**情况2：** P[x]和P[ next[x - 1] ]不相等。

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/190258cabc3e48b1a74670deb531680b~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.image?)

如上图所示：如图。长度为 now 的子串 A 和子串 B 是 P[0]~P[x-1] 中最长的公共前后缀。可惜 A 右边的字符和 B 右边的那个字符不相等，next[x]不能改成 now+1 了。因此，我们应该 **缩短这个now**，把它改成小一点的值， **再来试试 P[x] 是否等于 P[now].**（now就是next[x - 1]).

now该缩小到多少呢？显然，我们不想让now缩小太多,要尽可能的少缩小一点，或者说，要一点一点地往前缩。 因此我们决定，在保持"P[0]~P[x-1]的now-前缀仍然等于[now-后缀]"的前提下，让这个新的now尽可能大一点。 P[0]~P[x-1] 的公共前后缀，前缀一定落在串A里面、后缀一定落在串B里面。换句话讲：接下来now应该改成：使得 **A的[k-前缀] 等于 B的k-后缀** 的最大的k。

这个时候我们要注意到一个非常重要的事实： **子串A和子串B是一样的！！** 就是说， **B的后缀就等于A的后缀**。那么， **使得使得A的k-前缀等于B的k-后缀的最大的k，其实就是A的前后缀的公共最大长度**。

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8afb6d89085b43f08375f359ff3829fb~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.image?)

来看上面的例子。当P[next[x - 1]]与P[x]不相等的时候，我们需要缩小now——把now变成next[now-1],也就是next[next[x - 1] - 1]，直到P[now]=P[x]为止。P[now]=P[x]时，就和情况1是一样的了。此时，就可以继续向右拓展了。

到此为止，KMP算法的思想基本上就已经阐述完毕了，经数学证明可以得知，KMP算法的时间复杂度是O(m + n)。

## KMP算法的c++代码实现

### next数组的c++代码实现

```ini
void buildNext(int* next,const string s)
    {
        int len = s.size()
        next[0] = 0
        int j = 0
        for(int i = 1
        {
            while(j > 0 && s[i] != s[j])//前后缀不相等
            {
                j = next[j - 1]
            }
            if(s[i] == s[j])//如果找到了相同前后缀，那就i++，j ++，都往后拓展一位
            {
                j ++
            }
            next[i] = j
        }
    }
复制代码
```

### strStr主函数的c++代码实现

```ini
int strStr(string haystack, string needle) {
        int size_1 = haystack.size()
        int size_2 = needle.size()
        if(size_1 < size_2)
            return -1
        int next[size_2]
        buildNext(next,needle)
        int j = 0
                  //不断根据最长公共前后缀的长度（next数组）来更新j的数值，其实就是不断改变needle字符串的对比位置
        for(int i = 0
        {
            while(j > 0 && haystack[i] != needle[j])//失配了，那就根据next数组往前回退，也就是相当于移动模式串这个标尺
            {
                j = next[j - 1]
            }
            //在这里跳出循环有两个可能，j == 0或者haystack[i] == needle[j]，或者两者都有。
            //如果haystack[i] == needle[j]了，那么就j++，i++各进一步然后继续比较
            //如果是j == 0，那么就说明此时已经回退到模式串的起始位置了，此时还是要看haystack[i]和needle[j]是否相等
            //如果不相等的话，就只有i向前移动一位，i ++，j就等于0不变，相当于模式串这个标尺只往前移动了一位，从头开始比较
            //如果相等，那么就各自都往右拓展一位

            //下面的写法只不过是把所有情况都综合在一起考虑了
            if(haystack[i] == needle[j])//主串和模式串的当前字符相等，那就j和i都各进一步，都++（i在for循环里++了）
            {
                j ++
            }
            if(j == size_2)//匹配成功，返回第一个匹配的位置下标
            {
                return (i - size_2 + 1)
            }
        }
        return -1
    }
复制代码
```
