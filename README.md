# Experiment 2 — Multithreaded Programming with Pthreads and OpenMP

## Aim
Develop multithreaded C programs using Pthreads and OpenMP to study thread creation, work distribution, race conditions, synchronization, thread coordination, and performance as the number of threads changes.

## Environment
- OS/environment: Windows with WSL Ubuntu
- Compiler: GCC (Ubuntu GCC 13.3.0 shown in the terminal screenshots)
- Editor: Nano
- Libraries: POSIX Threads (Pthreads) and OpenMP
- Working directory: `~/parallel_lab`

## Procedure followed

### 1. Prepare the environment
1. Open PowerShell and start/install Ubuntu through WSL.
2. Create the lab folder and enter it:
   ```bash
   mkdir -p ~/parallel_lab
   cd ~/parallel_lab
   pwd
   ```
3. Check the compiler and OpenMP support:
   ```bash
   gcc --version
   gcc -fopenmp --version
   ```
4. Install GCC if it is missing:
   ```bash
   sudo apt update
   sudo apt install gcc
   ```

### 2. Pthreads programs
Pthreads uses explicit thread creation and joining with `pthread_create()` and `pthread_join()`.

| Program | Purpose | Compile and run |
|---|---|---|
| `thread1.c` | Create one additional thread | `gcc thread1.c -o thread1 -pthread` then `./thread1` |
| `thread2.c` | Create four threads | `gcc thread2.c -o thread2 -pthread` then `./thread2` |
| `thread_sum.c` | Divide an array sum among threads | `gcc thread_sum.c -o thread_sum -pthread` then `./thread_sum` |
| `race.c` | Demonstrate a race condition on a shared counter | `gcc race.c -o race -pthread` then `./race` |
| `mutex.c` | Protect the counter using a mutex | `gcc mutex.c -o mutex -pthread` then `./mutex` |
| `pthread_perf.c` | Measure a large calculation with different thread counts | `gcc pthread_perf.c -o pthread_perf -pthread` then `./pthread_perf` |

The race-condition program may produce an actual count below 400,000 because simultaneous `counter++` operations can overwrite each other. The mutex version protects the critical section and should produce 400,000.

### 3. OpenMP programs
OpenMP uses compiler directives to manage a group of threads and share loop work.

| Program | Purpose | Compile and run |
|---|---|---|
| `omp1.c` | Parallel region and thread IDs | `gcc omp1.c -o omp1 -fopenmp` then `./omp1` |
| `omp_sum.c` | Share array-sum loop iterations with `reduction` | `gcc omp_sum.c -o omp_sum -fopenmp` then `./omp_sum` |
| `omp_race.c` | Demonstrate a race condition | `gcc omp_race.c -o omp_race -fopenmp` then `./omp_race` |
| `omp_critical.c` | Protect the counter with `critical` | `gcc omp_critical.c -o omp_critical -fopenmp` then `./omp_critical` |
| `omp_barrier.c` | Coordinate Stage 1 and Stage 2 | `gcc omp_barrier.c -o omp_barrier -fopenmp` then `./omp_barrier` |
| `omp_perf.c` | Measure the same large calculation using OpenMP | `gcc omp_perf.c -o omp_perf -fopenmp` then `./omp_perf` |

The array-sum programs use the values `10, 20, 30, 40, 50, 60, 70, 80`; the expected total is `360`. In the synchronization examples, the mutex and `critical` section protect shared updates, while a barrier makes threads wait until all have completed the first stage.

### 4. Performance measurement
The sequential program and the Pthreads/OpenMP performance programs calculate the same sum for `N = 1,000,000,000`. The parallel programs were run with 1, 2, 4, 6, and 16 threads. The following values are transcribed from the user's experiment screenshots.

**Sequential baseline:** `3.973170 seconds`

#### Execution-time comparison

| Threads | Sequential (s) | Pthreads (s) | OpenMP (s) |
|---:|---:|---:|---:|
| Baseline | 3.973170 | — | — |
| 1 | — | 3.710670 | 3.455363 |
| 2 | — | 2.163951 | 2.207980 |
| 4 | — | 1.218696 | 1.163483 |
| 6 | — | 0.810221 | 0.797492 |
| 16 | — | 0.618349 | 0.696315 |

#### Speedup comparison

Formula: `Speedup = Sequential execution time / Parallel execution time`

| Threads | Pthreads speedup (×) | OpenMP speedup (×) |
|---:|---:|---:|
| 1 | 1.071 | 1.150 |
| 2 | 1.836 | 1.800 |
| 4 | 3.260 | 3.415 |
| 6 | 4.904 | 4.982 |
| 16 | 6.424 | 5.705 |

#### Efficiency comparison

Formula: `Efficiency (%) = (Speedup / Number of threads) × 100`

| Threads | Pthreads efficiency (%) | OpenMP efficiency (%) |
|---:|---:|---:|
| 1 | 107.07 | 114.92 |
| 2 | 91.80 | 89.98 |
| 4 | 81.49 | 85.38 |
| 6 | 81.73 | 83.04 |
| 16 | 40.15 | 35.66 |

> Note: The efficiency values above 100% at one thread result from comparing separate program runs and timings. They do not mean that a single thread provides more than 100% parallel efficiency. Measurements can vary with system load and timing overhead.

### 5. Graphs

#### Execution time
![Execution time comparison](graphs/execution_time_comparison.png)

#### Speedup
![Speedup comparison](graphs/speedup_comparison.png)

#### Efficiency
![Efficiency comparison](graphs/efficiency_comparison.png)

## Observations
- Execution time generally decreased as the thread count increased for both Pthreads and OpenMP.
- At 16 threads, the measured Pthreads time was `0.618349 s`; OpenMP took `0.696315 s`.
- Speedup improved with more threads, but it did not increase linearly with thread count.
- Efficiency fell at 16 threads, showing that additional threads introduce overhead and do not guarantee proportional gains.
- Pthreads offers explicit control over thread creation and joining. OpenMP offers a higher-level model through directives such as `parallel`, `parallel for`, `critical`, and `barrier`.

## Conclusion
The experiment demonstrated thread creation, work distribution, race conditions, synchronization, and coordination with Pthreads and OpenMP. The performance measurements showed reduced execution time for the tested parallel versions compared with the sequential baseline. The results also showed that speedup is not perfectly linear and that performance depends on the workload and execution environment.

## Screenshot evidence
Only terminal/PowerShell screenshots are retained in `screenshots/`. The screenshots that originally showed comparison tables and graphs have been removed from that folder; the results are presented as Markdown tables above, and the three graphs are recreated in `graphs/` from the recorded measurements.
