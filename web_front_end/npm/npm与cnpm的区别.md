### npm与cnpm的区别
说到`npm`与`cnpm`的区别，可能大家都知道，但大家容易忽视的一点，是`cnpm`装的各种`node_module`，这种方式下所有的包都是扁平化的安装。一下子`node_modules`展开后有非常多的文件。导致了electron在打包的过程中非常慢。但是如果改用`npm`来安装`node_modules`的话，所有的包都是树状结构的，层级变深。