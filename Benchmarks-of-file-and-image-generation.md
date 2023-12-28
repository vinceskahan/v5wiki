## Instructions

See https://github.com/weewx/weewx-benchmark. Follow the directions in the
readme for how to run the benchmark. 

When you're done, add your results to the table below. For architecture on
Debian systems, please run `dpkg --print-architecture`, rather than relying on
`uname -a`. If that does not reveal anything useful, then try `cat
/proc/cpuinfo`.

<hr/>

| Hardware                                                              | WeeWX      | Python | Files (21) | Images (68) | Footnotes |
|-----------------------------------------------------------------------|------------|--------|-----------:|------------:|----------:|
| Mac Mini, M1 2020 (Sonoma 14.0)                                       | 5.0.0b13   | 3.10.9 |      0.60s |       1.06s |           |
| MacBook Air, M1 2020                                                  | 4.10.2     | 3.7.16 |      0.69s |       0.78s |           |
| MacBook Air, M1 2020                                                  | 5.0.0b13   | 3.7.16 |      0.71s |       0.97s |           |
| MacBook Air, M1 2020                                                  | 5.0.0rc1   | 3.7.16 |      0.69s |       0.93s |           |
| NUC i7, 11th gen                                                      | 5.0.0b13   | 3.7.10 |      0.89s |       1.14s |           |
| Xen VM running on i5-9600K 3.7GHz                                     | 5.0.0rc1-1 | 3.11.2 |      1.44s |       1.51s |           |
| RPI 5 Model B Rev 1.0<br>(4GB RaspiOS lite arm64 6.1.0-rpi6-rpi-2712 )| 5.0.0b15   | 3.11.2 |      1.63s |       2.03s |           |
| Macbook Air 5,2 <br>(mid-2012 IvyBridge i5, Ubuntu 22.04.3 LTS)       | 5.0.0b13   | 3.10.12|      2.41s |       2.63s |           |
| RPi 4 Model B Rev 1.1 <br>(4GB, RaspiOS lite arm64 6.1.0-rpi4-rpi-v8) | 5.0.0b13   | 3.11.2 |      6.24s |       6.39s |           |
| RPi 4 Model B Rev 1.1 <br>(4GB, RaspiOS lite armhf 6.1.21-v8+)        | 5.0.0b13   | 3.9.2  |      6.36s |       6.56s |           | 
| RPi 3 Model B Plus Rev 1.3 <br>(RaspiOS lite armhf 6.1.21-v8+)        | 5.0.0b13   | 3.9.2  |     13.06s |      14.07s |           |       
| inovato quadra Allwinner H6<br/>ARMv8 Cortex-A53 @ 1.7GHz             | 5.0.0b13   | 3.9.2  |     14.03s |      11.87s |       (1) |
| RPi 2 (32-bit)                                                        | 5.0.0b13   | 3.9.2  |     24.27s |      25.95s |           |
| Rpi Zero W (32-bit)                                                   | 5.0.0b13   | 3.9.2  |     53.97s |      57.79s |           |

(1) Using the Python version of Cheetah's NameMapper, which can significantly
slow file generation.
