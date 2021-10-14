# linux性能分析工具



## 一、perf+火焰图

针对CPU，测试发现更适合C++代码，python代码不易与分析

1. 安装perf https://ubuntuqa.com/article/1622.html

```
	sudo apt-get install linux-source
```

2. 安装火焰图生成器

    git clone https://github.com/brendangregg/FlameGraph

3. 使用perf生成分析文件

```c++
perf record -F 99 -a -g -- sleep 60               
/*对CPU所有进程以99Hz采集,它的执行频率是 99Hz（每秒99次），如果99次都返回同一个函数名，那就说明 CPU 这一秒钟都在执行同一个函数，可能存在性能问题。执行60秒后会弹出f提示表示采集完成，在当前目录会生成一个perf.data的文件*/

perf record -F 99 -p 181 -g -- sleep 60        
/*对进程ID为181的进程进行采集，采集时间为60秒，执行期间不要退出上述代码中perf record表示记录，-F 99表示每秒99次，-p 13204是进程号，即对哪个进程进行分析，-g表示记录调用栈，sleep 30则是持续30秒，-a 表示记录所有cpu调用。更多参数可以执行perf --help查看。

perf.data文件生成后，表示采集完成。最好是在火焰图的目录下进行采集，方便转换成SVG图形。*/
```

4. 生成火焰图

```c++
 perf script -i /root/perf.data  | ./stackcollapse-perf.pl --all |  ./flamegraph.pl > ksoftirqd.svg
```

5. 打开浏览器查看ksoftirqd.svg



## 二、Profile

Python 性能分析工具——函数耗时

 https://zhuanlan.zhihu.com/p/110105273

1. 运行py文件（ IDR_main.py）并生成性能测试文件（IDR_main.out）保存

```c
python -m cProfile -o IDR_main.out IDR_main.py
```

2. 排序并打印测试文件结果（按照）

```
python -c "import pstats; p=pstats.Stats('IDR_main.out'); p.sort_stats('time').print_stats(30)"
```

说明：

```
sort_stats支持一下参数：
calls, cumulative, file, line, module, name, nfl, pcalls, stdname, time其中输出每列的具体解释如下：

●ncalls：表示函数调用的次数；
●tottime：表示指定函数的总的运行时间，除掉函数中调用子函数的运行时间；
●percall：（第一个 percall）等于 tottime/ncalls；
●cumtime：表示该函数及其所有子函数的调用运行的时间，即函数开始调用到返回的时间；
●percall：（第二个 percall）即函数运行一次的平均时间，等于 cumtime/ncalls；●filename:lineno(function)：每个函数调用的具体信息；
如果需要将输出以日志的形式保存，只需要在调用的时候加入另外一个参数。如 profile.run(“profileTest()”,”testprof”)。
```

3. 可视化snakeviz

pip 安装 snakeviz 后，在 Anaconda prompt 里运行如下命令：

```
snakeviz IDR_main.out
```

## 三、Vtune（收费）

https://software.intel.com/content/www/cn/zh/develop/articles/book-programming-on-intel-platform_vtune_introduction.html

Vtune可视化性能分析器（Intel VTune Performance Analyzer）是一个用于分析和优化程序性能的工具，作为Intel为众多开发者们提供的专门针对寻找软硬件性能瓶颈的一款分析工具，它能帮助你确定程序的热点（hotspot），帮助你找到导致性能不理想的原因，从而让你能据此对程序进行优化。