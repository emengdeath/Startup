# Shell Environment

## Initialize Environment
Append the following commands to `~/.bashrc`

```
if [ -z $ascinited ]; then
  # Intel MPI
  export MPI_HOME=/opt/intel/impi/5.0.2.044
  export PATH=$MPI_HOME/bin64:$PATH
  export MANPATH=$MPI_HOME/man:$MANPATH
  # Intel C++ Compiler
  source /opt/intel/bin/compilervars.sh intel64
  #VTune
  source /opt/intel/vtune_amplifier_xe/amplxe-vars.sh
  # flag
  export ascinited=true
fi


```

## X11 Forwording via SSH
ssh with `-X` option.

This feature allows remote user to start X11 application in local X11 server.

Example: Execute `gnome-system-monitor` in remote shell.

\* To suppress the Warning of accessibility bus, append `export NO_AT_BRIDGE=1` to `~/.bashrc`

## Directly Connect to Nodes
`ssh cu01`

\* See /etc/hosts

\* Currently only cu01-cu05 are alive. (`pbsnodes -l all`)



# Intel MPI

## Help

### View Complete Documentation
Prerequisit: `X11 Forwording enabled`
```
cd /opt/intel/impi/5.0.2.044/doc
firefox get_started.html
evince User_Guide.pdf
```

### MPI tools
`$mpitool --help` where $mpitool is one of tools in `/opt/intel/impi/5.0.2.044/intel64/bin`

### MPI API Manpage
`man $MPI_FUNCTIONS` where $MPI_FUNCTIONS is one of MPI APIs in `/opt/intel/impi/5.0.2.044/man/man3`

## Compile
`mpicc -o hello  hello.c`

`mpicxx -o hello  hello.cpp`

## Execute
Refer to [PBS](#PBS)


# PBS

[PBS Script Tutorial](http://www.doc88.com/p-3874247732913.html)

[PBS Arguments](https://wenku.baidu.com/view/625bcc4ea2161479171128c4.html)

## Template


test.pbs
```
#PBS -N optest
#PBS -l nodes=4:ppn=4:walltime=24:00:00
#PBS -V
#PBS -o ~/test/out
#PBS -e ~/test/err
#PBS -q batch
#PBS -S /bin/bash

mpirun -np 4 ~/test/hello
```

* -N  Job Name
* -V: Pass env to nodes. (initasc)
* -o: Standard output path
* -e: Error output path
* -q: Queue to be put into. Default: batch
* nodes: Node Num
* ppn: Core Num per Node
* walltime: Max time estimated

`qsub test.pbs`

## Other commands
`qstat -a`

`qdel $JobId`

`qalter $ARGS $JobId` where $ARGS is like `-l walltime=36:00:00`

`qorder $job1 $job2` switches order of two jobs

`pbsnodes -a`


# VTune


## Documentation
```
cd /opt/intel/vtune_amplifier_xe/documentation/en
firefox documentation_amplifier.htm
```

## Run VTune
`amplxe-gui`

## Notice

VTune Remote Debug feature is broken now due to scp error (which might be caused by sharing home).

To profile a program, an avaliable method is to `ssh -X` to the specified node and run `amplxe-gui` locally.


### Quick start

1. Prepare Binary
 * Add `-g` option for debug symbol when compile with `mpicxx` or `mpicc`

2. New Project
  * Target system: Local
  * Target type: Launch Application
  * Application: binary_path_with_debug_symbol

  OK

3. New Analysis

## Hardware Performance Analysis
Prerequisit: `Current user belongs to vtune group.`

New Analysis -> ...
