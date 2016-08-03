title: 用 R 做 t 检验
link: http://songchunlin.net/t-test-with-r/
author: sclyeah
description: 
post_id: 1388
created: 2014/06/05 23:25:48
created_gmt: 2014/06/05 15:25:48
comment_status: open
post_name: t-test-with-r
status: publish
post_type: post

# 用 R 做 t 检验

研究者对研究对象总体按一定方法抽样，为了尽可能准确地根据这一样本的参数估计总体，验证某些假设，这就需要用到假设检验，而t检验就是假设检验中最常用的一种检验方法，可以通过R软件非常方便地实现。 第一种情况是单样本t检验（one sample t-test），目的是检验一个正态分布的总体均值是否满足零假设的已知均值。例如：已知A地家蝇翅膀长度均值47mm，为研究B地家蝇翅膀长度和A地有无差异，随机抽取B地100只家蝇，测量其翅膀长度。注意，研究对象是B地所有的家蝇，这个数目是远大于100的，如果直接把这100只的数据平均然后比较是不准确的，统计学上用T检验解决了这个问题。已知B地家蝇翅膀长度符合正态分布，样本符合随机原则。零假设H0：μ=47，即B地家蝇翅膀长度与A地一致；备择假设Ha：μ≠47，即B地家蝇翅膀长度与A地不一致，显著性水平α为0.05。R下的做法是： [sourcecode language="plain"] #输入数据 wl <\- c(36,37,38,38,39,39,40,40,40,40,41,41,41,41,41,41,42,42,42,42,42,42,42,43,43,43,43,43,43,43,43,44,44,44,44,44,44,44,44,44,45,45,45,45,45,45,45,45,45,45,46,46,46,46,46,46,46,46,46,46,47,47,47,47,47,47,47,47,47,48,48,48,48,48,48,48,48,49,49,49,49,49,49,49,50,50,50,50,50,50,51,51,51,51,52,52,53,53,54,55) #t检验 t.test(wl, mu = 47, var.equal = FALSE) #mu表示平均值，var.equal = FALSE表示方差未知 One Sample t-test data: wl t = -3.8269, df = 99, p-value = 0.0002274 alternative hypothesis: true mean is not equal to 47 95 percent confidence interval: 44.72226 46.27774 sample estimates: mean of x 45.5 [/sourcecode] 从上述结果可以看出，基于100个样本的p-value = 0.0002274 < 0.05，因此有充足的理由拒绝H0假设，即B地家蝇翅膀长度与A地不一致，95%置信区间为（44.72226,46.27774），B地家蝇翅膀长度比A地短。 第二种情况是两样本t检验（two-sample t-test），目的是检验两个正态分布的总体均值之间是否有差异（是否相等）。例如：为验证1和2两地家蝇翅膀长度是否有差异，随机在1和2两地抽取家蝇，测量翅膀长度，一共40个样本，数字特征分别为μ1和μ2。零假设H0：μ1=μ2，即两地家蝇翅膀长度无显著差异；备择假设Ha：μ1≠μ2，即两地家蝇翅膀长度有显著差异，显著性水平α为0.05。R下的做法是： [sourcecode language="plain"] # 输入、整理数据 lines <\- "ID length site\n1 45 1\n2 44 1\n3 45 2\n4 43 1\n5 48 2\n6 47 2\n7 47 1\n8 48 1\n9 43 2\n10 44 1\n11 46 2\n12 46 1\n13 44 1\n14 47 2\n15 40 1\n16 48 2\n17 45 1\n18 46 2\n19 42 1\n20 43 2\n21 40 1\n22 43 1\n23 49 2\n24 46 1\n25 47 1\n26 46 2\n27 43 2\n28 45 1\n29 46 1\n30 47 2\n31 46 2\n32 47 2\n33 45 1\n34 46 2\n35 43 1\n36 45 2\n37 44 1\n38 46 2\n39 44 2\n40 45 2" wl <\- read.table(con <\- textConnection(lines), header = TRUE) close(con) x = wl[wl$site == 1, 2] # 样地1数据 y = wl[wl$site == 2, 2] # 样地2数据 # 检查前提条件 shapiro.test(x) # 样地1数据正态性检验 Shapiro-Wilk normality test data: x W = 0.9522, p-value = 0.4017 #P值为0.4017，说明没有足够的证据拒绝样地1数据来自正态分布 > shapiro.test(y) # 样地2数据正态性检验 Shapiro-Wilk normality test data: y W = 0.9397, p-value = 0.2363 #P值为0.2363，说明没有足够的证据拒绝样地2数据来自正态分布 # Bartlett Test方差齐性检验（参数） bartlett.test(length ~ site, data = wl) Bartlett test of homogeneity of variances data: length by site Bartlett's K-squared = 0.9775, df = 1, p-value = 0.3228 #t检验 t.test(x, y) Welch Two Sample t-test data: x and y t = -2.4616, df = 36.141, p-value = 0.01874 alternative hypothesis: true difference in means is not equal to 0 95 percent confidence interval: -2.7356655 -0.2643345 sample estimates: mean of x mean of y 44.35 45.85 [/sourcecode] 基于1和2个地点t检验的p-value = 0.01874 < 0.05，因此拒绝H0假设，即认为两地地家蝇翅膀长度有显著差异。 第三种情况是配对t检验或称重复测量t检验（ "paired" or "repeated measures" t-test），用于检验同一统计量的两次测量值之间的差异是否为零。例如测量病人治疗前和治疗后的某种指标水平，检验其差异是否显著；或者要比较同一河流上下游两地污染情况，同一指标在上下游各测量一次，共有N个指标，这都要用到配对T检验。R中配对t检验的代码为t.test(x, y, paired=TRUE)，x和y为两组变量。懒得找数据举具体例子了。

## Comments

**[simon](#41794 "2015-08-26 12:26:31"):** 请问你的例子不是都是2 tail 的么？那么不是p-value 应该和0.025比较么 我是初学者，想请教一下你。 thx。

**[宋春林](#42023 "2015-09-01 13:37:25"):** 你说得对，双尾检验 _P_ 值应该是和0.025比较，谢谢指出。

**[苹果姐姐](#47473 "2015-12-31 05:43:17"):** 通过搜索看到您的两篇R的文章，说明简洁清晰，例子也用的很好，正好适合我这种知其然不知其所以然还要给人家讲R的统计学应用的半吊子，非常感谢，期待更多

**[ZhaoJie](#86783 "2016-05-05 15:00:20"):** 请问双侧检验P值与0.025比较，但显著水平还是0.05，是这样吗？
