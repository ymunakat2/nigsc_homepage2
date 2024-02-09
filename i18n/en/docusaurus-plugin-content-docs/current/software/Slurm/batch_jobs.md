---
id: batch_jobs
title: Submitting Batch Jobs
---

## How to Submit Batch Jobs

In Slurm, the `sbatch` command is used to submit batch jobs. A batch job execution means describing the job as a script file, submitting it to a partition (queue) with the script file as an argument, and then outputting the job execution results to a file in an asynchronous job execution manner. This is useful for jobs that require long execution times without interactive processing. Job execution results are output to a file. (If the filename is not specified with an option, standard output and standard error output are output in one file named slurm-JOBID.out)

```
sbatch [options] job_submission_script_filename
```

Use the above command to submit jobs.

The main options for the `sbatch` command are as follows:

| Option | Description |
|--------|-------------|
| -o --output path | Output the job's standard output to \<path\> |
| -e --error path | Output the job's error output to \<path\> |
| -J job_name | You can specify a job name |
| -N --nodes | Specify the number of nodes to allocate to the job |
| -n --ntasks | Specify the number of tasks to allocate to the job. By default, one task is launched per node |
| --exclusive | Use (occupy) the compute node exclusively |
| -p partition_name | Specify the partition to submit the job to |
| -mem integer[unit] | Specify the amount of memory to allocate to the job. If unit is omitted, the unit is MB |
| --mem-per-cpu integer[units] | Specify the amount of memory to reserve per CPU. If unit is omitted, the unit is MB |
| -t --time | Set the upper limit of execution time. For detailed instructions on time specification, refer to the [sbatch](https://slurm.schedmd.com/sbatch.html) online manual |

Also, the -o and -e options can utilize the following patterns:

| Pattern | Meaning |
|---------|---------|
| %j | Job ID |
| %A | Master job ID of the job array |
| %a | Job array (index) ID |
| %J | Same as %j.%a |
| %u | Username |
| %x | Job name |

Job execution options can also be specified as directive lines at the beginning of the job script. In practice, this method is often used because it's cumbersome to repeatedly execute with options. Place the option specifying `#SBATCH` at the beginning of the line between the shebang and the first command line of the shell script.

Example:
```
#!/bin/sh
#SBATCH -o /home/aaaa/results/%x-%j.out

```

## Slurm Environment Variables

The following environment variables are set by Slurm in the execution environment to control processing by referring to variables during job execution:

| Slurm Environment Variable Name | Description |
|---------------------------------|-------------|
| SLURM_CPUS_PER_TASK | The number of CPU cores specified with -c |
| SLURM_GPUS | The number of GPUs specified with --gpus |
| SLURM_JOB_ID | Job ID |
| SLURM_JOB_DEPENDENCY | The value set with -d |
| SLURM_JOB_NAME | Job name |
| SLURM_JOB_PARTITION | Partition name |
| SLURM_MEM_PER_CPU | The amount of memory set with --mem-per-cpu |

## Checking Job Status

You can check the execution status of jobs using the `squeue` command. For detailed options, please refer to the [online manual](https://slurm.schedmd.com/squeue.html#SECTION_OPTIONS).

Execution example:

```
squeue

```

The default items displayed by `squeue` are as follows:

| Item Name | Description |
|-----------|-------------|
| JOBID | Displays the job ID assigned to the job |
| PARTITION | Displays the name of the partition (queue) into which the job was submitted |
| NAME | Displays the job name (if not specified, the command string) |
| USER | Displays the username of the user who submitted the job |
| ST | Displays the job status. The main job statuses are shown in the table below |
| TIME | Job execution time (format: days-hh:mm:ss) |
| NODES | Number of nodes used for job execution |
| NODELIST(REASON) | List of host names where the job is executed |

## Checking Detailed Job Information

If you want to check more detailed information about your job, confirm the job ID with the `squeue` command, and then check with `scontrol show job`. For detailed options, please refer to the [online manual](https://slurm.schedmd.com/scontrol.html#SECTION_OPTIONS).

Execution example:

```
scontrol show job

```

## Deleting Jobs

If a job is taking longer

 than expected, or if the intermediate results output to the job's standard output and standard error output suggest that the job is not performing as expected, use the `scancel` command to delete the job.

Execution example:

```
scancel job_ID

```

To cancel multiple submitted jobs at once, the following methods can be used:

```

# To delete by specifying a username
scancel -u user_ID

# To delete by specifying a partition name
scancel -p partition_name

# To delete by specifying a job status
scancel -t job_status (e.g., Pending)

```
For detailed options, please refer to the [online manual](https://slurm.schedmd.com/scancel.html#SECTION_OPTIONS).

## Checking Job Execution Results

The `sacct` command can be used to check the execution history of jobs. For detailed options, please refer to the [online manual](https://slurm.schedmd.com/sacct.html#SECTION_OPTIONS).

## About the Execution Methods for Various Jobs

The methods for writing job submission scripts and specifying directive lines for executing various types of jobs are described below.

### Executing a Standalone Job

To execute a job that is neither inter-node parallel nor intra-node parallel (thread parallel), write the job script as follows:

```
#!/bin/bash
#SBATCH -p partition_name
#SBATCH -n 1  # Specify the number of processes to start for the job. In this case, 1

```

### Executing a Thread-Parallel Job

To submit a multi-threaded execution job using multiple CPU cores in one process:

```
!#/bin/bash
#SBATCH -p partition_name
#SBATCH -n 1               # Specify the number of processes to start for the job. In this case, 1
#SBATCH -c 20              # Specify the number of CPU cores to use for the job (up to the number of cores available on the node)
export OMP_NUM_THREADS=${SLURM_CPUS_PER_TASK}
./a.out
exit $?

```
The OMP_NUM_THREADS environment variable is specified because if a.out is a program written using OpenMP, it indicates how many cores will be used for thread parallel execution.
If you want to consider the number of CPU sockets in the node and the number of cores per physical CPU for thread assignment, specify the following options for thread assignment:


| Option | Meaning |
|--------|---------|
| --ntasks-per-node | The number of tasks within a job allocated to one node |
| --ntasks-per-core | The number of tasks within a job allocated per core |
| --ntasks-per-socket | The number of tasks within a job allocated per socket |
| --sockets-per-node | The number of sockets allocated per node for a job |
| --threads-per-core | The number of threads within a job allocated per core |
| --cores-per-socket | The number of cores within a job allocated per socket |

Consider the number of compute nodes to allocate to the job, the number of CPU sockets per node (2 for Thin compute nodes and GPU compute nodes in the gen-ken supercomputer), and the number of cores supported by one physical CPU, and determine the values to specify for the above options accordingly. If the values specified in the above options are not consistent, an error will occur when submitting the job. In that case, please reconsider whether the specified values are consistent.

### Executing an MPI Program

In the gen-ken supercomputer, OpenMPI and IntelMPI are installed as MPI processing system implementations, and you will use one of these two. Since Slurm supports PMIx in the MPI processing system, srun is recommended as the MPI launch command instead of the mpirun command included with the MPI processing system. The following describes examples of executing MPI programs by calling srun from within the job script.

- For IntelMPI:

Set the environment variable I_MPI_PMI_LIBRARY to specify the directory location of the Slurm PMI library. Other environments are set by loading them with the module command.

```
#!/bin/bash
#SBATCH -p
#SBATCH --job-name mpi-test
#SBATCH --nodes=4
#SBATCH --ntasks=8
#SBATCH -o %x.%J.out

export I_MPI_PMI_LIBRARY=/path/to/slurm/lib/libpmi2.so
srun mpi_test

```

- For OpenMPI:

MPI can be launched directly using Slurm's srun command. It can be started with a job script as follows:

```

#!/bin/bash
#SBATCH -p
#SBATCH --job-name mpi-test
#SBATCH --nodes=4
#SBATCH --ntasks=8
#SBATCH -o %x.%J.out

module load xxxxx
srun mpi_test

```

As described above, request the necessary computing resources from Slurm on the sbatch side, and start processes with srun. If options are specified in the directive line in the script submitted with sbatch, it is not necessary to specify options on the srun side

.

### Executing a Job Using GPU

The GPU environment is defined using Slurm's GRES (Generic Resources). Request the necessary number of GPUs in the job script as follows:

```
#!/bin/bash
#SBATCH --gres=gpu:4
...
```

Specify the resource in --gres as follows:

--gres=name:[type]:count

Here, name is the Generic Resource name, specify gpu. Also, in the GPU nodes of the gen-ken supercomputer, four Volta (v100) GPUs are equipped. Specify v100 for type. And for count, specify the number of GPUs you want to use. For example, if you want to use all four volta100 GPUs on a 1-GPU compute node:

--gres:gpu:v100:4   or   --gres:gpu:4

Other major options are as follows:

| Option | Meaning |
|--------|---------|
| --gpus | Specify the number of GPUs to allocate per job |
| --gpus-per-node | Specify the number of GPUs to allocate per node for that job |
| --gpus-per-socket | Specify the number of GPUs to allocate per CPU socket |
| --cpus-per-gpu | Specify the number of CPU cores to allocate per GPU |
| --mem-per-gpu | Specify the amount of memory to be allocated per GPU |

It is possible to specify detailed operations, but if they are not consistent, an error will occur, so keep it to specifying the number of GPUs to use with --gres, and leave the rest to Slurm.
For details on the options, please refer to the description in [Generic Resource (GRES) Scheduling](https://slurm.schedmd.com/gres.html).
