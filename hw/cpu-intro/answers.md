# OSTEP Ch.4 Homework Answers

## Q1
- Prediction / 预测:
```
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2        RUN:cpu         READY             1          
  3        RUN:cpu         READY             1          
  4        RUN:cpu         READY             1          
  5        RUN:cpu         READY             1          
  6           DONE       RUN:cpu             1          
  7           DONE       RUN:cpu             1          
  8           DONE       RUN:cpu             1          
  9           DONE       RUN:cpu             1          
 10           DONE       RUN:cpu             1          

Stats: Total Time 10
Stats: CPU Busy 10 (100.00%)
Stats: IO Busy  0 (0.00%)
```
- Reasoning / 理由:
```
-l 5:100,5:100：创建两个进程，每个进程有5条指令。100 表示每条指令都是CPU指令，不会发起I/O操作。 
由于两个进程都只执行CPU指令，没有I/O等待造成的CPU空闲时间。所以CPU利用率为100%
```
- Verified result / 验证结果:
- Analysis / 分析:
```
因为两个进程都只执行 CPU 指令，不涉及 I/O 等待，所以 CPU 始终处于忙碌状态，利用率为 100%。
```

## Q2
- Prediction / 预测:
```
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2        RUN:cpu         READY             1          
  3        RUN:cpu         READY             1          
  4        RUN:cpu         READY             1          
  5           DONE        RUN:io             1          
  6           DONE       BLOCKED                           1
  7           DONE       BLOCKED                           1
  8           DONE       BLOCKED                           1
  9           DONE       BLOCKED                           1
 10           DONE       BLOCKED                           1
 11*          DONE   RUN:io_done             1          

Stats: Total Time 11
Stats: CPU Busy 6 (54.55%)
Stats: IO Busy  5 (45.45%)
```
- Reasoning / 理由:
```
完成这两个进程需要 11 个时间单位。
分析如下：
首先 PID 0 连续执行 4 条 CPU 指令，耗时 4 个时间单位。之后 PID 0 执行完毕，PID 1 开始发出 I/O 请求，PID 1 处于 BLOCKED 状态，等待 I/O 完成。此时 CPU 空闲。最后 PID 1 执行 RUN:io_done，I/O 操作完成，进程结束。
```
- Verified result / 验证结果:
- Analysis / 分析:
```
两个进程总共需要 11 个时间单位。由于 PID 1 执行 I/O 时没有其他进程可以使用 CPU，导致 CPU 在 I/O 等待期间空闲，降低了整体利用率。
```

## Q3
- Prediction / 预测:
```
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED       RUN:cpu             1             1
  3        BLOCKED       RUN:cpu             1             1
  4        BLOCKED       RUN:cpu             1             1
  5        BLOCKED       RUN:cpu             1             1
  6        BLOCKED          DONE                           1
  7*   RUN:io_done          DONE             1          

Stats: Total Time 7
Stats: CPU Busy 6 (85.71%)
Stats: IO Busy  5 (71.43%)
```
- Reasoning / 理由:
```
程序命令: -l 1:0,4:100
重要,  I/O 先启动，PID 0 等待期间，CPU 可以执行 PID 1 的 4 条指令。这样 CPU 工作与 I/O 操作发生重叠，总耗时降为 7。
```
- Verified result / 验证结果:
- Analysis / 分析:
```
进程顺序确实重要。合理地安排 I/O 密集型进程和 CPU 密集型进程，可以提高 CPU 利用率，并缩短整个工作负载的完成时间。
```

## Q4
- Prediction / 预测:
```
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED         READY                           1
  3        BLOCKED         READY                           1
  4        BLOCKED         READY                           1
  5        BLOCKED         READY                           1
  6        BLOCKED         READY                           1
  7*   RUN:io_done         READY             1          
  8           DONE       RUN:cpu             1          
  9           DONE       RUN:cpu             1          
 10           DONE       RUN:cpu             1          
 11           DONE       RUN:cpu             1          

Stats: Total Time 11
Stats: CPU Busy 6 (54.55%)
Stats: IO Busy  5 (45.45%)
```
- Reasoning / 理由:
```
使用 -S SWITCH_ON_END 后, PID 0 执行 I/O 时 CPU 不会切换到 PID 1, 等 PID 0 完成后 PID 1 再执行.
```
- Verified result / 验证结果:
- Analysis / 分析:
```
SWITCH_ON_END 会让系统在 I/O 操作完成之前不切换到另一个进程。这种策略使 I/O 等待期间的 CPU 资源得不到充分利用，降低了系统的整体效率。
```

## Q5
- Prediction / 预测:
```
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED       RUN:cpu             1             1
  3        BLOCKED       RUN:cpu             1             1
  4        BLOCKED       RUN:cpu             1             1
  5        BLOCKED       RUN:cpu             1             1
  6        BLOCKED          DONE                           1
  7*   RUN:io_done          DONE             1          

Stats: Total Time 7
Stats: CPU Busy 6 (85.71%)
Stats: IO Busy  5 (71.43%) 
  ```
- Reasoning / 理由:
```
使用 -S SWITCH_ON_IO 后，当进程发出 I/O 请求并进入等待状态时，操作系统会立即切换到另一个就绪进程，让 CPU 在 I/O 执行期间继续工作。
```
- Verified result / 验证结果:
- Analysis / 分析:
```
SWITCH_ON_IO 能提高 CPU 利用率，并缩短两个进程的总运行时间。
当一个进程因 I/O 而阻塞时，应尽可能让其他就绪进程使用 CPU，而不是让 CPU 空等 I/O 完成。
```

## Q6
- Prediction / 预测:
```
Time        PID: 0        PID: 1        PID: 2        PID: 3           CPU           IOs
  1         RUN:io         READY         READY         READY             1          
  2        BLOCKED       RUN:cpu         READY         READY             1             1
  3        BLOCKED       RUN:cpu         READY         READY             1             1
  4        BLOCKED       RUN:cpu         READY         READY             1             1
  5        BLOCKED       RUN:cpu         READY         READY             1             1
  6        BLOCKED       RUN:cpu         READY         READY             1             1
  7*         READY          DONE       RUN:cpu         READY             1          
  8          READY          DONE       RUN:cpu         READY             1          
  9          READY          DONE       RUN:cpu         READY             1          
 10          READY          DONE       RUN:cpu         READY             1          
 11          READY          DONE       RUN:cpu         READY             1          
 12          READY          DONE          DONE       RUN:cpu             1          
 13          READY          DONE          DONE       RUN:cpu             1          
 14          READY          DONE          DONE       RUN:cpu             1          
 15          READY          DONE          DONE       RUN:cpu             1          
 16          READY          DONE          DONE       RUN:cpu             1          
 17    RUN:io_done          DONE          DONE          DONE             1          
 18         RUN:io          DONE          DONE          DONE             1          
 19        BLOCKED          DONE          DONE          DONE                           1
 20        BLOCKED          DONE          DONE          DONE                           1
 21        BLOCKED          DONE          DONE          DONE                           1
 22        BLOCKED          DONE          DONE          DONE                           1
 23        BLOCKED          DONE          DONE          DONE                           1
 24*   RUN:io_done          DONE          DONE          DONE             1          
 25         RUN:io          DONE          DONE          DONE             1          
 26        BLOCKED          DONE          DONE          DONE                           1
 27        BLOCKED          DONE          DONE          DONE                           1
 28        BLOCKED          DONE          DONE          DONE                           1
 29        BLOCKED          DONE          DONE          DONE                           1
 30        BLOCKED          DONE          DONE          DONE                           1
 31*   RUN:io_done          DONE          DONE          DONE             1          

Stats: Total Time 31
Stats: CPU Busy 21 (67.74%)
Stats: IO Busy  15 (48.39%)
```
- Reasoning / 理由:
```
-S SWITCH_ON_IO：进程发出 I/O 请求并阻塞后，立即切换到另一个就绪进程。
-I IO_RUN_LATER：I/O 完成后，发出请求的进程不会立即抢占当前正在运行的进程，而是进入就绪状态，等待当前进程运行结束或主动让出 CPU 后再获得执行机会。
```
- Verified result / 验证结果:
- Analysis / 分析:
```
3个 CPU 密集型进程会先后完成，而 PID 0 的 I/O 完成后不会立即恢复运行。由于 PID 0 还有剩余的 I/O 指令，后续就会出现 CPU 空闲、I/O 继续执行的情况。
IO_RUN_LATER 让 I/O 完成的进程延迟恢复运行，错过了及时发起下一次 I/O 请求的机会，导致 CPU 与 I/O 的并行程度降低。
系统资源没有得到充分利用。CPU 利用率只有约 67.74%，说明约三分之一的总运行时间里 CPU 没有执行工作。
```

## Q7
- Prediction / 预测:
```
Time        PID: 0        PID: 1        PID: 2        PID: 3           CPU           IOs
  1         RUN:io         READY         READY         READY             1          
  2        BLOCKED       RUN:cpu         READY         READY             1             1
  3        BLOCKED       RUN:cpu         READY         READY             1             1
  4        BLOCKED       RUN:cpu         READY         READY             1             1
  5        BLOCKED       RUN:cpu         READY         READY             1             1
  6        BLOCKED       RUN:cpu         READY         READY             1             1
  7*   RUN:io_done          DONE         READY         READY             1          
  8         RUN:io          DONE         READY         READY             1          
  9        BLOCKED          DONE       RUN:cpu         READY             1             1
 10        BLOCKED          DONE       RUN:cpu         READY             1             1
 11        BLOCKED          DONE       RUN:cpu         READY             1             1
 12        BLOCKED          DONE       RUN:cpu         READY             1             1
 13        BLOCKED          DONE       RUN:cpu         READY             1             1
 14*   RUN:io_done          DONE          DONE         READY             1          
 15         RUN:io          DONE          DONE         READY             1          
 16        BLOCKED          DONE          DONE       RUN:cpu             1             1
 17        BLOCKED          DONE          DONE       RUN:cpu             1             1
 18        BLOCKED          DONE          DONE       RUN:cpu             1             1
 19        BLOCKED          DONE          DONE       RUN:cpu             1             1
 20        BLOCKED          DONE          DONE       RUN:cpu             1             1
 21*   RUN:io_done          DONE          DONE          DONE             1          

Stats: Total Time 21
Stats: CPU Busy 21 (100.00%)
Stats: IO Busy  15 (71.43%)
```
- Reasoning / 理由:
```
1. 这种行为有什么不同？
使用 IO_RUN_LATER：I/O 完成后，发起 I/O 的进程进入就绪状态，当前进程继续运行。
使用 IO_RUN_IMMEDIATE：I/O 完成后，发起 I/O 的进程立即运行，当前进程被暂停，之后可以继续执行。
2. 为什么立即运行刚完成 I/O 的进程是个好主意？
主要有三个原因。
2.1. 提高 I/O 与 CPU 的并行程度。 PID 0 可以立即执行下一条 I/O 指令，再次发出 I/O 请求。此时 PID 0 阻塞，CPU 就能继续执行 PID 1、PID 2 或 PID 3 的 CPU 工作。
2.2. 减少资源闲置。 如果一直推迟 PID 0 的执行，它可能无法及时发起下一次 I/O 请求。当其他 CPU 密集型进程都完成时，CPU 就可能空闲等待 I/O。立即运行 PID 0 可以减少这种情况。
2.3. 提高整体吞吐量。 让 I/O 密集型进程及时继续运行，可以更好地重叠 CPU 计算与 I/O 操作，通常能缩短所有进程完成所需的总时间。
```
- Verified result / 验证结果:
- Analysis / 分析:
```
IO_RUN_IMMEDIATE 的优势在于：I/O 一完成，就尽快让发起它的进程继续执行，以便它能及时启动下一次 I/O。
这并不意味着 CPU 指令本身执行得更快，而是通过更合理地安排进程执行顺序，提高 CPU 和 I/O 设备的协同利用率，减少资源空闲时间。
```

## Q8
- Prediction / 预测:
```
s1-1. python3 process-run.py -s 1 -l 3:50,3:50
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED       RUN:cpu             1             1
  4        BLOCKED       RUN:cpu             1             1
  5        BLOCKED       RUN:cpu             1             1
  6        BLOCKED          DONE                           1
  7        BLOCKED          DONE                           1
  8*   RUN:io_done          DONE             1          
  9         RUN:io          DONE             1          
 10        BLOCKED          DONE                           1
 11        BLOCKED          DONE                           1
 12        BLOCKED          DONE                           1
 13        BLOCKED          DONE                           1
 14        BLOCKED          DONE                           1
 15*   RUN:io_done          DONE             1          

Stats: Total Time 15
Stats: CPU Busy 8 (53.33%)
Stats: IO Busy  10 (66.67%) 

s1-2. python3 process-run.py -s 1 -l 3:50,3:50 -I IO_RUN_IMMEDIATE
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED       RUN:cpu             1             1
  4        BLOCKED       RUN:cpu             1             1
  5        BLOCKED       RUN:cpu             1             1
  6        BLOCKED          DONE                           1
  7        BLOCKED          DONE                           1
  8*   RUN:io_done          DONE             1          
  9         RUN:io          DONE             1          
 10        BLOCKED          DONE                           1
 11        BLOCKED          DONE                           1
 12        BLOCKED          DONE                           1
 13        BLOCKED          DONE                           1
 14        BLOCKED          DONE                           1
 15*   RUN:io_done          DONE             1          

Stats: Total Time 15
Stats: CPU Busy 8 (53.33%)
Stats: IO Busy  10 (66.67%) 

s1-3. python3 process-run.py -s 1 -l 3:50,3:50 -S SWITCH_ON_END
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED         READY                           1
  4        BLOCKED         READY                           1
  5        BLOCKED         READY                           1
  6        BLOCKED         READY                           1
  7        BLOCKED         READY                           1
  8*   RUN:io_done         READY             1          
  9         RUN:io         READY             1          
 10        BLOCKED         READY                           1
 11        BLOCKED         READY                           1
 12        BLOCKED         READY                           1
 13        BLOCKED         READY                           1
 14        BLOCKED         READY                           1
 15*   RUN:io_done         READY             1          
 16           DONE       RUN:cpu             1          
 17           DONE       RUN:cpu             1          
 18           DONE       RUN:cpu             1          

Stats: Total Time 18
Stats: CPU Busy 8 (44.44%)
Stats: IO Busy  10 (55.56%)

s2-1. python3 process-run.py -s 2 -l 3:50,3:50
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED       RUN:cpu             1             1
  3        BLOCKED        RUN:io             1             1
  4        BLOCKED       BLOCKED                           2
  5        BLOCKED       BLOCKED                           2
  6        BLOCKED       BLOCKED                           2
  7*   RUN:io_done       BLOCKED             1             1
  8         RUN:io       BLOCKED             1             1
  9*       BLOCKED   RUN:io_done             1             1
 10        BLOCKED        RUN:io             1             1
 11        BLOCKED       BLOCKED                           2
 12        BLOCKED       BLOCKED                           2
 13        BLOCKED       BLOCKED                           2
 14*   RUN:io_done       BLOCKED             1             1
 15        RUN:cpu       BLOCKED             1             1
 16*          DONE   RUN:io_done             1          

Stats: Total Time 16
Stats: CPU Busy 10 (62.50%)
Stats: IO Busy  14 (87.50%)

s2-2. python3 process-run.py -s 2 -l 3:50,3:50 -I IO_RUN_IMMEDIATE
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED       RUN:cpu             1             1
  3        BLOCKED        RUN:io             1             1
  4        BLOCKED       BLOCKED                           2
  5        BLOCKED       BLOCKED                           2
  6        BLOCKED       BLOCKED                           2
  7*   RUN:io_done       BLOCKED             1             1
  8         RUN:io       BLOCKED             1             1
  9*       BLOCKED   RUN:io_done             1             1
 10        BLOCKED        RUN:io             1             1
 11        BLOCKED       BLOCKED                           2
 12        BLOCKED       BLOCKED                           2
 13        BLOCKED       BLOCKED                           2
 14*   RUN:io_done       BLOCKED             1             1
 15        RUN:cpu       BLOCKED             1             1
 16*          DONE   RUN:io_done             1          

Stats: Total Time 16
Stats: CPU Busy 10 (62.50%)
Stats: IO Busy  14 (87.50%)

s2-3. python3 process-run.py -s 2 -l 3:50,3:50 -S SWITCH_ON_END
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED         READY                           1
  3        BLOCKED         READY                           1
  4        BLOCKED         READY                           1
  5        BLOCKED         READY                           1
  6        BLOCKED         READY                           1
  7*   RUN:io_done         READY             1          
  8         RUN:io         READY             1          
  9        BLOCKED         READY                           1
 10        BLOCKED         READY                           1
 11        BLOCKED         READY                           1
 12        BLOCKED         READY                           1
 13        BLOCKED         READY                           1
 14*   RUN:io_done         READY             1          
 15        RUN:cpu         READY             1          
 16           DONE       RUN:cpu             1          
 17           DONE        RUN:io             1          
 18           DONE       BLOCKED                           1
 19           DONE       BLOCKED                           1
 20           DONE       BLOCKED                           1
 21           DONE       BLOCKED                           1
 22           DONE       BLOCKED                           1
 23*          DONE   RUN:io_done             1          
 24           DONE        RUN:io             1          
 25           DONE       BLOCKED                           1
 26           DONE       BLOCKED                           1
 27           DONE       BLOCKED                           1
 28           DONE       BLOCKED                           1
 29           DONE       BLOCKED                           1
 30*          DONE   RUN:io_done             1          

Stats: Total Time 30
Stats: CPU Busy 10 (33.33%)
Stats: IO Busy  20 (66.67%)

s3-1. python3 process-run.py -s 3 -l 3:50,3:50
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED        RUN:io             1             1
  4        BLOCKED       BLOCKED                           2
  5        BLOCKED       BLOCKED                           2
  6        BLOCKED       BLOCKED                           2
  7        BLOCKED       BLOCKED                           2
  8*   RUN:io_done       BLOCKED             1             1
  9*       RUN:cpu         READY             1          
 10           DONE   RUN:io_done             1          
 11           DONE        RUN:io             1          
 12           DONE       BLOCKED                           1
 13           DONE       BLOCKED                           1
 14           DONE       BLOCKED                           1
 15           DONE       BLOCKED                           1
 16           DONE       BLOCKED                           1
 17*          DONE   RUN:io_done             1          
 18           DONE       RUN:cpu             1          

Stats: Total Time 18
Stats: CPU Busy 9 (50.00%)
Stats: IO Busy  11 (61.11%)

s3-2. python3 process-run.py -s 3 -l 3:50,3:50 -I IO_RUN_IMMEDIATE
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED        RUN:io             1             1
  4        BLOCKED       BLOCKED                           2
  5        BLOCKED       BLOCKED                           2
  6        BLOCKED       BLOCKED                           2
  7        BLOCKED       BLOCKED                           2
  8*   RUN:io_done       BLOCKED             1             1
  9*         READY   RUN:io_done             1          
 10          READY        RUN:io             1          
 11        RUN:cpu       BLOCKED             1             1
 12           DONE       BLOCKED                           1
 13           DONE       BLOCKED                           1
 14           DONE       BLOCKED                           1
 15           DONE       BLOCKED                           1
 16*          DONE   RUN:io_done             1          
 17           DONE       RUN:cpu             1          

Stats: Total Time 17
Stats: CPU Busy 9 (52.94%)
Stats: IO Busy  11 (64.71%)

s3-3. python3 process-run.py -s 3 -l 3:50,3:50 -S SWITCH_ON_END
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED         READY                           1
  4        BLOCKED         READY                           1
  5        BLOCKED         READY                           1
  6        BLOCKED         READY                           1
  7        BLOCKED         READY                           1
  8*   RUN:io_done         READY             1          
  9        RUN:cpu         READY             1          
 10           DONE        RUN:io             1          
 11           DONE       BLOCKED                           1
 12           DONE       BLOCKED                           1
 13           DONE       BLOCKED                           1
 14           DONE       BLOCKED                           1
 15           DONE       BLOCKED                           1
 16*          DONE   RUN:io_done             1          
 17           DONE        RUN:io             1          
 18           DONE       BLOCKED                           1
 19           DONE       BLOCKED                           1
 20           DONE       BLOCKED                           1
 21           DONE       BLOCKED                           1
 22           DONE       BLOCKED                           1
 23*          DONE   RUN:io_done             1          
 24           DONE       RUN:cpu             1          

Stats: Total Time 24
Stats: CPU Busy 9 (37.50%)
Stats: IO Busy  15 (62.50%)
```
- Reasoning / 理由:
```
1. IO_RUN_IMMEDIATE 与 IO_RUN_LATER 的区别
IO_RUN_IMMEDIATE：I/O 完成时，立即运行发起 I/O 的进程。即使其他进程正在运行，也可能发生切换。
IO_RUN_LATER：I/O 完成时，发起 I/O 的进程进入就绪状态，当前进程继续运行。
因此，同一组随机生成的进程，在这两种设置下可能出现不同的执行顺序、CPU 利用率和总运行时间。
2. SWITCH_ON_IO 与 SWITCH_ON_END 的区别
这两个设置通常对执行轨迹的影响更明显。
SWITCH_ON_IO：进程发出 I/O 请求后立即切换到其他就绪进程。这样可以在一个进程等待 I/O 时，让另一个进程继续使用 CPU。
SWITCH_ON_END：当前进程只有执行结束后才切换。即使它发出 I/O 请求，也不会立即切换，因此其他进程可能一直无法运行，导致 CPU 空闲。
```
- Verified result / 验证结果:
- Analysis / 分析:
```
对随机生成的进程而言，SWITCH_ON_IO 往往是更关键的设置，因为它决定了 CPU 能否在其他进程等待 I/O 时继续工作；IO_RUN_IMMEDIATE 则决定 I/O 完成后是否立即恢复相关进程。两者共同决定 CPU 与 I/O 的重叠程度，以及整个系统的资源利用率。
```
