# CPU-intro Homework
## Q1
**预测**：两个进程依次完整执行，不会中途切换，一个跑完才跑下一个。
**理由**：默认调度策略 SWITCH_ON_END，进程执行完所有指令结束才切换。
**验证结果**：Process 0
cpu
cpu
cpu
cpu
cpu

Process 1
cpu
cpu
cpu
cpu
cpu

Important behaviors:
System will switch when the current process is FINISHED or ISSUES AN IO
After IOs, the process issuing the IO will run LATER (when it is its turn)
**分析**：SWITCH_ON_END属于非抢占调度。仅当进程执行完毕或者主动发起I/O时才切换；I/O完成后不会抢占CPU，需要等待调度轮到该进程才继续运行。CPU密集型进程会持续占用CPU直到结束。

## Q2
**预测**：进程发起I/O时，让出CPU，调度其他进程运行。
**理由**：参数 SWITCH_ON_IO，一旦发起I/O操作，立刻切换进程。
**验证结果**：Process 0
io
io_done
cpu
cpu
……
cpu
**分析**：SWITCH_ON_IO，进程发起I/O就让出CPU，调度其他进程。可以利用I/O等待的时间执行别的任务，实现I/O和CPU计算重叠，提高CPU利用率。

## Q3
**预测**：I/O完成后，不会抢占CPU，等当前正在运行进程结束，才执行这个进程。
**理由**：IO_RUN_LATER，I/O完成仅标记就绪，不打断当前进程。
**验证结果**：Process 0
io
Process 1
cpu
cpu
……
cpu
Process 0
io_done
**分析**：SWITCH_ON_IO + IO_RUN_LATER。发起I/O时会切换进程；I/O完成不抢占，必须等当前进程结束，才调度之前等待I/O的进程。实现简单，但交互响应慢。
## Q4
**预测**：I/O完成瞬间，立刻抢占CPU，切回刚完成I/O的进程。
**理由**：IO_RUN_IMMEDIATE，I/O结束马上切换。
**验证结果**：Process 0
io
Process 1
cpu
Process 0
io_done
Process 1
cpu
……
cpu
**分析**：SWITCH_ON_IO + IO_RUN_IMMEDIATE，属于抢占策略。I/O一旦完成就立刻抢占CPU，恢复I/O进程运行，交互式程序响应更快；但频繁切换会带来少量系统开销。

## Q5
**预测**：多个进程I/O等待时间重叠，CPU利用率高。
**理由**：SWITCH_ON_IO，发起I/O就切换，利用I/O等待时间跑别的任务。
**验证结果**：Process 0
io
Process 1
cpu
Process 0
io_done
Process 0
io
Process 1
cpu
Process 0
io_done
Process 0
io
Process 1
cpu
Process 0
io_done
**分析**：SWITCH_ON_IO + IO_RUN_IMMEDIATE。进程发起I/O就切换，I/O等待期间CPU运行其他任务，实现I/O与计算重叠，最大化CPU利用率。

## Q6
**预测**：进程A发起I/O，切换到B；I/O完成立刻打断B，切回A。
**理由**：SWITCH_ON_IO + IO_RUN_IMMEDIATE，I/O完成优先抢占。
**验证结果**：Process 0
io
Process 1
cpu
Process 1
cpu
Process 0
io_done
Process 0
io
Process 0
io_done
**分析**：SWITCH_ON_IO + IO_RUN_IMMEDIATE。发起I/O就切换进程；I/O完成触发抢占，立刻切回该进程，I/O密集程序交互响应速度快。

## Q7
**预测**：进程A发起I/O，切到B；I/O完成，B继续跑完，B结束才运行A。
**理由**：SWITCH_ON_IO + IO_RUN_LATER，I/O完成不抢占。
**验证结果**：Process 0
io
Process 1
cpu
Process 0
io_done
**分析**：SWITCH_ON_IO + IO_RUN_LATER。发起I/O会切换进程；但I/O完成不会抢占CPU，必须等待当前进程执行结束，才能调度I/O进程，交互性能较差。

## Q8
**预测**：IO_RUN_IMMEDIATE对I/O密集程序更友好，响应快，CPU利用率高。
**理由**：每次I/O完成就抢占CPU，适合频繁I/O的交互程序。
**验证结果**：Process 0
io
Process 1
cpu
Process 1
cpu
Process 0
io_done
Process 0
io
Process 0
io_done
**分析**：IO_RUN_IMMEDIATE抢占策略适合交互式、I/O密集型程序，I/O完成立刻切换，响应更快，但进程切换会带来少量开销；IO_RUN_LATER非抢占策略简单，只有进程主动放弃CPU才切换。S
作业答案已完成