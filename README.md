[![Package Quality](http://npm.packagequality.com/shield/v8-cpu-analysis.svg)](http://packagequality.com/#?package=v8-cpu-analysis)
# 每天掌握一个新知识点

* 2017.3.3：
 * 1.了解了一致性hash算法 
 * 2.初步学习了Go语言的基本语法

* 2017.3.4-3.5：周末有私事未学习

* 2017.3.6：
    * 1.尝试编写Node扩展，借助于 ```uv_queue_work``` 和 ```uv_async``` 构建了一个简单的tcp通信模块，并且该模块借助于信号量可以在js阻塞时继续工作。

* 2017.3.7
	* 1.工作比较忙，只有空闲了解了下RSA加密算法的原理

* 2017.3.8
	* 1.女神节陪老婆了，未学习

* 2017.3.9
	* 1.仔细了解下v8的两种GC算法，针对新生代的Scavenge算法（分成From和To两个部分，空间换时间）；以及针对老生代的Mark-Sweep/Mark-Compact算法（三色标记，由每一个内存页上的空洞情况决定是单纯清除死亡对象还是把存活对象复制到新的内存页上）

* 2017.3.10
	* 1.研究了在regexp的贪婪匹配模式下，长正则运算时v8提供的cpu-profiler时不时无法正常工作的原因，尚未找到结果

* 2017.3.11~3.12: 周末私事未学习

* 2017.3.13：定位编写的c++扩展模块运行偶尔会出现 **Check failed: AllowJavascriptExecution::IsAllowed(isolate).** 的错误。