This is a benchmark designed to test how fast the files and images can be
generated. Add your results to the end

Environment:
- Seasons skin
- Simulator
- wview-extended schema

Steps:
1. Wipe out archive/weewx.sdb
2. Wipe out public_html
3. Run weewxd for one reporting cycle
4. Then benchmark using wee_reports

| Hardware                                                  | WeeWX Version | Python version | Files (8) | Images (56) |
|-----------------------------------------------------------|---------------|----------------|-----------|-------------|
| NUC i7, 11th gen                                          | 5.0.0b13      | 3.7.10         | 0.31s     | 0.48s       |   
| inovato quadra Allwinner H6<br/>ARMv8 Cortex-A53 @ 1.7GHz | 5.0.0b12      | 3.9.2          | 3.84s     | 5.29s       |
| RPi 2 (32-bit)                                            | 5.0.0b12      | 3.9.2          | 7.67s     | 8.44s       |
| Rpi Zero W (32-bit)                                       | 5.0.0b12      | 3.9.2          | 20.12s    | 19.67s      |