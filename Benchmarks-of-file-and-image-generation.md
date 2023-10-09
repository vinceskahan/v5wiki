## New (realistic) protocol

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

4. Run the benchmark, using `wee_reports`

   ```shell
   wee_reports weewx-benchmark/weewx.conf
   ```
   
5. Look in the log for the generation time of the files and images. There should
   be 21 files and 68 images.

6. Add your results to the table below

| Hardware                                                         | WeeWX     | Python | Files (21) | Images (68) | Footnotes |
|------------------------------------------------------------------|-----------|--------|-----------:|------------:|----------:|
| Mac Mini, M1 2020 (Sonoma 14.0)                                  | 5.0.0b13  | 3.10.9 |      0.60s |       1.06s |           |
| MacBook Air, M1 (2020)                                           | 5.0.0b13  | 3.7.16 |      0.71s |       0.97s |           |
| NUC i7, 11th gen                                                 | 5.0.0b13  | 3.7.10 |      0.89s |       1.14s |           |
| RPi 4 Model B Rev 1.1 <br>(4GB, RaspiOS lite aarch64 6.1.21-v8+) | 5.0.0b13  | 3.9.2  |      6.36s |       6.56s |           | 
| RPi 3 Model B Plus Rev 1.3 <br>(RaspiOS lite aarch64 6.1.21-v8+) | 5.0.0b13  | 3.9.2  |     13.06s |      14.07s |           |       
| inovato quadra Allwinner H6<br/>ARMv8 Cortex-A53 @ 1.7GHz        | 5.0.0b13  | 3.9.2  |     14.03s |      11.87s |       (1) |
| RPi 2 (32-bit)                                                   | 5.0.0b13  | 3.9.2  |     24.27s |      25.95s |           |
| Rpi Zero W (32-bit)                                              | 5.0.0b13  | 3.9.2  |     53.97s |      57.79s |           |


(1) Using the Python version of Cheetah's NameMapper, which can significantly
slow file generation.


## Old (minimal) protocol

### Environment
- Seasons skin
- Simulator
- wview-extended schema

### Steps

1. Wipe out `archive/weewx.sdb`.
2. Run `weewxd` for at least _two_ reporting cycles. It's important to do at 
   least two cycles because the ET calculation needs more than one measurement. 
   However, don't let `weewxd` run for so long that the size of the archive file 
   becomes a factor.
3. Stop `weewxd`.
4. Wipe out `public_html`.
5. Then benchmark using `wee_reports`.
6. Look in the log for generation times for the files and images. There should
   be 8 files and 60 images. If you get only 56 images, it's because you didn't
   let `weewxd` run long enough to generate the needed ET data.
7. Add your results to the table below.

### Results

| Hardware                                                         | WeeWX Version | Python version | Files (8) | Images (60) |
|------------------------------------------------------------------|---------------|----------------|-----------|-------------|
| Mac Mini, M1 2020 (Sonoma 14.0)                                  | 5.0.0b13      | 3.10.9         | 0.23s     | 0.55s       |
| MacBook Air, M1 (2020)                                           | 5.0.0b12      | 3.7.16         | 0.27s     | 0.43s       |
| NUC i7, 11th gen                                                 | 5.0.0b13      | 3.7.10         | 0.31s     | 0.48s       |
| AWS Lightsail                                                    | 5.0.0b12      | 3.8.10         | 0.73 s    | 0.91s       |
| RPi 4 Model B Rev 1.1 <br>(4GB, RaspiOS lite aarch64 6.1.21-v8+) | 5.0.0b13      | 3.9.2          | 1.87s     | 2.42s       |
| RPi 3 Model B Plus Rev 1.3 <br>(RaspiOS lite aarch64 6.1.21-v8+) | 5.0.0b13      | 3.9.2          | 3.89s     | 4.47s       |
| inovato quadra Allwinner H6<br/>ARMv8 Cortex-A53 @ 1.7GHz        | 5.0.0b12      | 3.9.2          | 3.84s     | 5.29s       |
| RPi 2 (32-bit)                                                   | 5.0.0b12      | 3.9.2          | 7.67s     | 8.44s       |
| Rpi Zero W (32-bit)                                              | 5.0.0b12      | 3.9.2          | 20.12s    | 19.67s      |
