## Benchmark protocol

This is a benchmark designed to test how fast WeeWX generates the files and
images of the Seasons skin, using one year of data. Add your results to the
table at the end.

1. Make sure you have installed WeeWX V5.0.0b13 or later. _Earlier versions will
   not work!_
 
2. Download and unpack the test station data
 
    ```shell
    cd /var/tmp
    wget https://weewx.com/benchmarks/weewx-benchmark.tar.gz
    tar xvf weewx-benchmark.tar.gz
    ```

3. The test is of how long it takes to create and populate the subdirectory
   `weewx-benchmark/public_html`, so make sure it is _not_ present before
   continuing.

   ```shell
   rm -r weewx-benchmark/public_html
   ```

4. Run the benchmark, using `weectl report`

   ```shell
   weectl report run --config=weewx-benchmark/weewx.conf
   ```
   
5. The benchmark will print the generation time for the files and images
   to standard output. Make sure there are 21 files and 68 images. If not, there
   may have been something in `weewx-benchmark/public_html`. Go back and do
   step 3 again.

6. Add your results to the table below. For architecture on Debian systems,
   please run `dpkg --print-architecture`, rather than relying on `uname -a`.

| Hardware                                                              | WeeWX    | Python | Files (21) | Images (68) | Footnotes |
|-----------------------------------------------------------------------|----------|--------|-----------:|------------:|----------:|
| Mac Mini, M1 2020 (Sonoma 14.0)                                       | 5.0.0b13 | 3.10.9 |      0.60s |       1.06s |           |
| MacBook Air, M1 2020                                                  | 5.0.0b13 | 3.7.16 |      0.71s |       0.97s |           |
| NUC i7, 11th gen                                                      | 5.0.0b13 | 3.7.10 |      0.89s |       1.14s |           |
| RPI 5 Model B Rev 1.0<br>(4GB RaspiOS lite arm64 6.1.0-rpi6-rpi-2712 )| 5.0.0b15 | 3.11.2 |      1.63s |       2.03s |           |
| Macbook Air 5,2 <br>(mid-2012 IvyBridge i5, Ubuntu 22.04.3 LTS)       | 5.0.0b13 | 3.10.12|      2.41s |       2.63s |           |
| RPi 4 Model B Rev 1.1 <br>(4GB, RaspiOS lite arm64 6.1.0-rpi4-rpi-v8) | 5.0.0b13 | 3.11.2 |      6.24s |       6.39s |           |
| RPi 4 Model B Rev 1.1 <br>(4GB, RaspiOS lite armhf 6.1.21-v8+)        | 5.0.0b13 | 3.9.2  |      6.36s |       6.56s |           | 
| RPi 3 Model B Plus Rev 1.3 <br>(RaspiOS lite armhf 6.1.21-v8+)        | 5.0.0b13 | 3.9.2  |     13.06s |      14.07s |           |       
| inovato quadra Allwinner H6<br/>ARMv8 Cortex-A53 @ 1.7GHz             | 5.0.0b13 | 3.9.2  |     14.03s |      11.87s |       (1) |
| RPi 2 (32-bit)                                                        | 5.0.0b13 | 3.9.2  |     24.27s |      25.95s |           |
| Rpi Zero W (32-bit)                                                   | 5.0.0b13 | 3.9.2  |     53.97s |      57.79s |           |

(1) Using the Python version of Cheetah's NameMapper, which can significantly
slow file generation.
