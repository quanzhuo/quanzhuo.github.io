安卓构建系统
===========
首先导入文件 `build/envsetup.sh`，导入该文件之后，当前环境中就会有一些额外有用的
命令（实际是 shell 函数）可以执行。
+ lunch：
+ tapas
+ croot：切换到 aosp 工程的根目录
+ m：在项目任何子文件夹中执行该命令都相当于在根目录中执行 make
+ mm：构建当前文件夹下的所有模块，不包含依赖
+ mmm：
+ mma：同 mm，包含依赖
+ mmma：同 mmm，包含依赖
+ cgrep：grep on c/c++ 文件
+ ggrep：gradle 文件
+ jgrep：java
+ resgrep：res/\*.xml文件
+ mangrep：AndroidManifest.xml
+ mgrep：Makefile
+ sepgrep：sepolicy 文件
+ sgrep：所有的 source 
+ godir：

所有的命令列表为：

```bash
addcompletions add_lunch_combo build_build_var_cache cgrep check_product 
check_variant choosecombo chooseproduct choosetype choosevariant core 
coredump_enable coredump_setup cproj croot destroy_build_var_cache findmakefile 
get_abs_build_var getbugreports get_build_var getdriver getlastscreenshot 
get_make_command getprebuilt getscreenshotpath getsdcardpath gettargetarch 
gettop ggrep godir hmm is isviewserverstarted jgrep key_back key_home key_menu 
lunch _lunch m make mangrep mgrep mm mma mmm mmma pez pid printconfig 
print_lunch_menu provision qpid rcgrep resgrep runhat runtest sepgrep 
set_java_home setpaths set_sequence_number set_stuff_for_environment settitle 
sgrep smoketest stacks startviewserver stopviewserver systemstack tapas 
tracedmdump treegrep
```