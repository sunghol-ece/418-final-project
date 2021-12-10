## Installation

1. Download the target program - We chose 'readelf' in [binutils](https://ftp.gnu.org/gnu/binutils/)
2. Install the requirements given from this repository - [AFL++](https://github.com/AFLplusplus/AFLplusplus)
3. Compile the target program with the compiler instrumentations in AFL++
4. Create an input directory (in_dir), and an output directory (out_dir)
5. Add some user inputs to the input directory - We added /bin/ps to in_dir

## Running

0. Run the following
```
sudo su
echo core /proc/sys/kernel/core_pattern
exit
```

SINGLE 

1. 
```
afl-fuzz -i in_dir -o out_dir .{directory of readelf}/readelf -a @@
```
MULTIPLE

1. 
```
afl-fuzz -i in_dir -o out_dir -M fuzzer0 .{directory of readelf}/readelf -a @@
afl-fuzz -i in_dir -o out_dir -S fuzzer1 .{directory of readelf}/readelf -a @@
afl-fuzz -i in_dir -o out_dir -S fuzzer2 .{directory of readelf}/readelf -a @@
...

```

## Compiling Results

1. Generate results into a plot
```
afl-plot out_dir {folder_for_plot}
```
2. Details of the execution of fuzzing can be viewed in the folders under the ```out_dir``` folder in the ```hangs, fuzzer_stats, plot_data``` files and folders.
