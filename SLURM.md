# Slurm

[Slurm](https://slurm.schedmd.com/overview.html) is "an open source, fault-tolerant, and highly scalable cluster management and job scheduling system for large and small Linux clusters." Practically speaking, it's how we can share compute resources with many people, while ensuring no one uses the same resources at the same time. This is especially important for this class where we will be writing high-performance code and timing it very precisely. 

This tutorial is meant to be more like a cheat sheet than a tutorial. There are many other Slurm tutorials and videos if you want to learn more or understand how Slurm works under the hood. Here we will just show you what commands you should be familiar with, and let you experiment with things yourself. Documentation is linked as a reference guide, but you should not have to read it in detail. The linked examples, however, are quite useful. 

## Class Reservation

We have one node called `agate-1` that is "reserved" for our class. You must pass in `--reservation=eec289q` to run jobs on this node. We will go over this in more detail in HW1.

## Managing Jobs

- On HPC2 you should have access to the following
  - Partitions: low/med/high
  - Nodes: agate0 - agate63
- Docs:
  - [sinfo](https://slurm.schedmd.com/sinfo.html), [examples](https://slurm.schedmd.com/sinfo.html#SECTION_EXAMPLES)
  - [squeue](https://slurm.schedmd.com/squeue.html), [examples](https://slurm.schedmd.com/squeue.html#SECTION_EXAMPLES)
  - [scontrol](https://slurm.schedmd.com/scontrol.html), [examples](https://slurm.schedmd.com/scontrol.html#SECTION_EXAMPLES)
  - [sacct](https://slurm.schedmd.com/sacct.html), [examples](https://slurm.schedmd.com/sacct.html#SECTION_EXAMPLES)
  - [sattach](https://slurm.schedmd.com/sattach.html), [examples](https://slurm.schedmd.com/sattach.html#SECTION_EXAMPLES)
  - [scancel](https://slurm.schedmd.com/scancel.html), [examples](https://slurm.schedmd.com/scancel.html#SECTION_EXAMPLES)

```bash
# View partitions, nodes and states
sinfo

# Get more info about partition
scontrol show partition low

# Get more info about node
scontrol show node agate-2

# View current jobs
squeue

# View jobs on node `agate-2`
squeue --nodelist agate-2
squeue -w agate-2

# View your jobs
squeue --user $USER
squeue -u $USER

# Watch your jobs
watch -n 1 squeue -u $USER

# Get high-level info about job, e.g. `scontrol show job 512721`
scontrol show job <JOB_ID>

# Get low-level info about job, e.g. `sacct -j 512721`
# NOTE: On agate-1 (our reserved node) 'AllocCPUS' are physical CPUs, otherwise logical CPUs
sacct -j <JOB_ID>

# Attach to running job step, e.g. `sattach 512721.0` or `sattach 512721.1`
sattach <JOB_ID>.<JOB_STEP>

# Cancel job, e.g. `scancel 512721` for whole job or `scancel 512721.0` for step
scancel <JOB_ID>
```

## Running Single-Threaded Jobs

- Plan to always use `srun`/`sbatch` for jobs
  - Use `srun` for single-task jobs / short command lines
    - Output to console (blocking further commands)
  - Use `sbatch` for multi-task jobs / longer command lines
    - Output to file (allowing further commands)
  - Use `salloc` to debug sbatch scripts, if necessary
    - Manually enter commands after allocation, output is shown in console

- Required arguments
  - Time limit (minutes): `--time` or `-t`, e.g. `-t 1`

- Optional arguments
  - Partition: `--partition` or `-p`, e.g. `-p med`
    - Use `-p med` to allocate >128 CPUs, but you can be preempted
    - Use `-p high` (default) to allocate up to 128 CPUs, and you can't be preempted
  - Node list: `--nodelist` or `-w`, e.g. `-w agate-1` 
    - Use `-w agate-1` for performance testing on our class-specific node
    - Omit `-w` to use any node for compiling, debugging, etc.

### Using `srun`

- [Docs: srun](https://slurm.schedmd.com/srun.html)
- **Warning: Do not use --pty bash unless you absolutely have to, as you can _easily_ forget to close the session and block resources. If you do have to, set a very reasonable timeout, such as a few minutes, e.g. `-t 5`, and re-connect as necessary.**

```bash
# In a separate window, watch your jobs (`--interval 1` = `-n 1` = 1 second)
watch --interval 1 squeue -u $USER

# Run srun "hello world" on any node (for debugging/development/testing)
srun --time 1 echo "hello world"

# Run srun "hello world" on agate-1 (for final performance evaluation)
srun --nodelist agate-1 --reservation=eec289q --time 1 echo "hello world"

# Run `srun --pty bash` to get interactive session if necesssary 
# Use a small --time value in case you forget to close the session!
srun --time 1 --pty bash
exit
```

### Using `sbatch` and `salloc`

- [Docs: sbatch](https://slurm.schedmd.com/sbatch.html)
- [Docs: salloc](https://slurm.schedmd.com/salloc.html)

```bash
# Create simple sbatch script with hello world
echo '#!/bin/bash
echo "$HOSTNAME: hello world"' > ~/hello_world.sh && chmod +x ~/hello_world.sh
cat ~/hello_world.sh

# Run sbatch "hello world" with default output
sbatch --time 1 ~/hello_world.sh
ls | grep .out  # output file: "slurm-<JOBID>.out"

# Run sbatch "hello world" with custom output
sbatch --time 1 --output ~/hello_world.out ~/hello_world.sh
cat ~/hello_world.out  # e.g. `agate-0: hello world`

# Create new sbatch script with some parameters inside (note the filename)
# We can use this with srun but #SBATCH parameters are ignored
echo '#!/bin/bash
#SBATCH --time=1
#SBATCH --output=hello_world-%j.out
echo "$HOSTNAME: hello world"' > ~/hello_world_v2.sh && chmod +x ~/hello_world_v2.sh 
cat ~/hello_world_v2.sh

# Run ~/hello_world_v2.sh with sbatch
sbatch ~/hello_world_v2.sh
ls | grep .out  # output file: "hello_world-<JOBID>.out"

# Run ~/hello_world_v2.sh with srun (need --time again, and no file output)
srun ~/hello_world_v2.sh  # ERROR: Missing time limit (#SBATCH value not read in with srun)
srun --time 1 ~/hello_world_v2.sh

# Run equivalent with salloc
# We first allocate with salloc (without --output), then we use `srun` to run commands
# Some clusters use a "default" command for salloc that runs `srun --pty bash` but HPC2 doesn't
salloc --time 1
srun bash -c "echo \$HOSTNAME: hello world"
```

- Some "gotchas"
  - Must create your output file's parent directory ahead of time
    - Otherwise there will be _no output_, no error
  - Cannot use variables in #SBATCH directives are not interpreted by shell 
    - No $VAR variables like $USER, etc.
    - No short-hand paths like `~`
    - See https://unix.stackexchange.com/a/285812 for details

## Running Multi-threaded Jobs

- Will be discussed in detail in HW2
- For now, you can experiment with the following

- Optional arguments
  - CPU threads: `--cpus-per-task` or `-c`, e.g. `-c 4` (default: 1 if no `--ntasks`)
    - Omit `--cpus-per-task`/`-c` and `--ntasks`/`-n` for single-threaded / debugging
    - Recommend providing _both_ `-c` and `-n` if using either one
      - See [WARNING: --cpus-per-task](https://slurm.schedmd.com/srun.html#OPT_cpus-per-task)
  - CPU cores: `-c` combined with `--hint=nomultithread `
    - See [Docs: Multi-Core Application Hints](https://slurm.schedmd.com/mc_support.html#srun_hints) for details
  - Specific CPUs: `--cpu-bind`
    - See [Docs: Multi-Core Architectures](https://slurm.schedmd.com/mc_support.html) for details

```bash
# Create new sbatch script with some parameters inside (note the filename)
# Echo env vars, see https://slurm.schedmd.com/srun.html#SECTION_OUTPUT-ENVIRONMENT-VARIABLES
# We can use this with srun but #SBATCH parameters are ignored
echo '#!/bin/bash
#SBATCH --time=1
#SBATCH --output=slurm_stat-%j.out
echo "Host: $HOSTNAME"
echo "Job id: $SLURM_JOBID"
echo "Num tasks: $SLURM_NPROCS"
echo "CPUs per task: $SLURM_CPUS_PER_TASK"
echo "CPUs allocated: $SLURM_CPUS_ON_NODE"
echo "CPU bind: ${SLURM_CPU_BIND_TYPE}${SLURM_CPU_BIND_LIST}"' > ~/slurm_stat.sh && chmod +x ~/slurm_stat.sh 
cat ~/slurm_stat.sh

# Run slurm_stat with different allocations
srun -t 1 -c 2 ~/slurm_stat.sh
srun -t 1 -c 4 ~/slurm_stat.sh
srun -t 1 -c 16 ~/slurm_stat.sh

# Add another version with lstopo to see the CPUs better
echo '#!/bin/bash
#SBATCH --time=1
#SBATCH --output=slurm_stat-%j.out
~/slurm_stat.sh

FILEPATH="$(pwd)/lstopo_${HOSTNAME}_cpus${SLURM_CPUS_ON_NODE}_job${SLURM_JOBID}.svg"
if [ "$#" -eq 1 ]; then
  FILEPATH=$1
fi

echo "Outputting lstopo to file: $FILEPATH"
lstopo -v $FILEPATH' > ~/slurm_stat_topo.sh && chmod +x ~/slurm_stat_topo.sh 
cat ~/slurm_stat_topo.sh

# Output the CPU allocations to an SVG with lstopo
srun -t 1 -c 2 ~/slurm_stat_topo.sh
srun -t 1 -c 4 ~/slurm_stat_topo.sh
srun -t 1 -c 8 ~/slurm_stat_topo.sh

# Try it on our reserved node
srun -t 1 -w agate-1 --reservation=eec289q -c 2 ~/slurm_stat_topo.sh
srun -t 1 -w agate-1 --reservation=eec289q -c 4 ~/slurm_stat_topo.sh
srun -t 1 -w agate-1 --reservation=eec289q -c 8 ~/slurm_stat_topo.sh
```

- For more details on the output of `lstopo` on our reserved `agate-1` node and the others, see the [AMD EPYC 7002 Tuning Guide](https://www.amd.com/content/dam/amd/en/documents/epyc-technical-docs/tuning-guides/amd-epyc-7002-tg-hpc-56827.pdf)
  - The setting is "NPS" as described in Chapter 3
    - The `agate-1` node is configured with "NPS=4" for four NUMA domains per socket
    - Other nodes are configured as "NPS=1" for one NUMA domain per socket
  - On page 46 they give examples of the configurations' impact on performance
  - For predictable and consistent performance, CPUs must access memory connected to the same NUMA node, otherwise they incur memory latency when accessing data from other nodes
  - The BIOS option allows one to hide these nodes on the CPU and expose it as a single NUMA node, but the underlying performance degradation does not go away due to the architecture of the CPU.
