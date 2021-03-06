# Async HTTP benchmark

A microbenchmark for asyncio, uvloop and trio. Uses multiple worker processes to
keep the game fair because asyncio secretly runs worker threads.

This program runs a minimal HTTP server implemented for asyncio and trio,
in each using direct socket API as well as the highest level streams API.

Asyncio's "low level" protocol API is not tested directly but the streams API
uses it internally. Asyncio is also tested with uvloop, if available.

## Install & run

````
pip3 install git+https://github.com/Tronic/async-http-benchmark.git
````

Install [wrk](https://github.com/wg/wrk/wiki/Installing-Wrk-on-Linux) for fully
automatic benchmarking, or run some other tool while ahbench is waiting.

Optional deps: `pip3 install trio uvloop`

Run all tests: `python3 -m ahbench` or
run only matching tests: `python3 -m ahbench uv` (or any other searchword).

## Bugs

Asyncio and uvloop usually die with random exceptions when KeyboardInterrupted
after each test. I couldn't fix that.

Pull requests are welcome.

## Testrun on Macbook Pro 2015

````
# python3 -m ahbench

asyncio_sockets >>> wrk -t8 -c100 http://127.0.0.1:62157/
Running 10s test @ http://127.0.0.1:62157/
  8 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     2.01ms    1.51ms  13.49ms   66.47%
    Req/Sec     6.33k     3.24k   15.12k    87.38%
  503829 requests in 10.00s, 42.76MB read
Requests/sec:  50361.09
Transfer/sec:      4.27MB

asyncio_streams >>> wrk -t8 -c100 http://127.0.0.1:62255/
Running 10s test @ http://127.0.0.1:62255/
  8 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     1.69ms    1.25ms  18.65ms   62.67%
    Req/Sec     7.55k     4.12k   18.13k    60.37%
  604546 requests in 10.10s, 51.31MB read
Requests/sec:  59835.56
Transfer/sec:      5.08MB

uvloop_sockets >>> wrk -t8 -c100 http://127.0.0.1:62353/
Running 10s test @ http://127.0.0.1:62353/
  8 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     1.04ms  647.89us  14.98ms   91.18%
    Req/Sec    11.96k     1.82k   19.72k    59.18%
  958882 requests in 10.10s, 81.39MB read
Requests/sec:  94932.63
Transfer/sec:      8.06MB

uvloop_streams >>> wrk -t8 -c100 http://127.0.0.1:62451/
Running 10s test @ http://127.0.0.1:62451/
  8 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     1.22ms  662.25us  22.92ms   92.98%
    Req/Sec     9.99k     1.24k   11.96k    67.20%
  803490 requests in 10.10s, 68.20MB read
Requests/sec:  79526.86
Transfer/sec:      6.75MB
Exception ignored in: <function WeakValueDictionary.__init__.<locals>.remove at 0x10dd6cdd0>
Traceback (most recent call last):
  File "/.../lib/python3.7/weakref.py", line 117, in remove
    _atomic_removal(d, wr.key)
KeyboardInterrupt

trio_sockets >>> wrk -t8 -c100 http://127.0.0.1:62550/
Running 10s test @ http://127.0.0.1:62550/
  8 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     2.17ms    6.68ms 124.43ms   98.75%
    Req/Sec     8.34k     2.69k   16.40k    79.92%
  659649 requests in 10.01s, 55.99MB read
Requests/sec:  65923.66
Transfer/sec:      5.60MB

trio_streams >>> wrk -t8 -c100 http://127.0.0.1:62648/
Running 10s test @ http://127.0.0.1:62648/
  8 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     2.61ms    5.66ms 108.00ms   97.56%
    Req/Sec     6.15k     1.40k   10.63k    86.42%
  488273 requests in 10.01s, 41.44MB read
Requests/sec:  48792.63
Transfer/sec:      4.14MB
````