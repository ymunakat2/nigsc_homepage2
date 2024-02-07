---
id: Slurm
Title: Overview of Slurm
---

At the Institute of Genetics Supercomputer (遺伝研スパコン), in addition to AGE (Altia Grid Engine), Slurm (Simple Linux Utility for Resource Management) is available as a selectable job management system for individual genome analysis tasks. Slurm is an open-source job management system widely used in large-scale cluster-based supercomputing systems around the world, including its original developer, the Lawrence Livermore National Laboratory (LLNL) in the United States. It is also suitable for HPC (High-Performance Computing) job management on multiple public cloud platforms and is commonly used as a job management system.

While AGE and Slurm share many fundamental features, there are some differences in terminology, concepts, and command systems, which we will outline below.

## Overview of Slurm

### Jobs

A job is the unit of work requested by a user to be managed by the job management system (Slurm), where computing resources like CPUs and memory are allocated by the system for execution. Each job is assigned a unique ID by the job management system (Slurm) within the system, allowing users to query the job's execution status based on this ID. The concept of a job remains the same between AGE and Slurm.

### Partitions

Slurm defines logical units called "partitions" to manage sets of computing resources, such as compute servers, according to system requirements. System operators can configure each partition with specific attributes, including available computing resources, maximum execution times, and maximum concurrent jobs per user. Users can rely on Slurm (the job management system) to handle job execution without needing to manually check the availability of computing resources. While AGE uses the concept of "queues" for similar purposes, Slurm refers to these as "partitions." Consider these two terms to be almost identical in meaning.

In the context of individual genome analysis, the following partitions (queues) have been defined and configured by the system:

| Partition Name | Maximum Execution Time | Maximum Jobs per User | Maximum Usable Cores per User | Maximum Usable Memory per User |
| -------------- | ---------------------- | --------------------- | ---------------------------- | ---------------------------- |
|                |                        |                       |                              |                              |

If no specific partition is specified when submitting a job, the default partition is set to "?". If no resource requests are specified as options during job submission, the job is registered in Slurm with the following default settings:

| Resource Request Item | Default | Options for Changing | Remarks |
| --------------------- | ------- | ------------------- | ------- |
|                       |         |                     |         |

### Job Submission Scripts

When submitting a job to Slurm for execution, users provide the desired task as a script. Job scripts can include Slurm options and can control the execution based on Slurm environment variables passed during runtime. Details on how to write job submission scripts can be found in the [Batch Job Submission](/software/Slurm/batch_jobs.md) documentation.

### Terminology Definitions for Understanding Multi-Core and Multi-Thread Operation in Slurm

For those who need to submit multi-threaded programs or MPI parallel programs to the system, here are definitions for some terminology:

| Term     | Definition/Explanation |
| -------- | ----------------------- |
| Task     | In the context of thread operation, "task" is often used interchangeably with "job." However, when describing array jobs, a "task" may refer to a child job launched from a parent job. |
| Socket   | Depending on system configuration, consider that a Thin compute node at the Institute of Genetics Supercomputer has 2 sockets. |
| CPU      | In Slurm terminology, when specifying options, "CPU" does not refer to physical CPUs but rather to CPU cores. Consider "CPU = core" in Slurm terminology. |
| Core     | In Slurm terminology, when specifying options, "CPU" does not refer to physical CPUs but rather to CPU cores. Consider "CPU = core" in Slurm terminology. |
| Thread   | A unit of execution. If Hyper-Threading is disabled, the number of threads is equivalent to the number of cores. At the Institute of Genetics Supercomputer, threads are equivalent to cores.

## Workflow for Job Execution

The workflow for using Slurm as the job management system for individual genome analysis at the Institute of Genetics Supercomputer can be summarized as follows:

1. Log in to the login node of the compute node or the GPU-specific Slurm login node at the gateway of the individual genome analysis section using SSH.
2. Create a job submission script on the login node for the desired processing.
3. Submit the job to a partition using the `sbatch` command from the login node.
4. Observe the job's execution status using various Slurm commands.
5. If the job's execution proceeds as expected, wait for the job to complete. If the job's execution does not proceed as expected, use the `scancel` command or similar to abort the job. Debug the job script while reviewing the contents of the error output file and resubmit the job using the modified job submission script with the `sbatch` command.
6. Once the job completes successfully, review the execution results from the output files.

## Introduction to Basic Operations

For detailed information on basic operations, please refer to the following pages:

- [Batch Job Submission](/software/Slurm/batch_jobs.md)
- [Interactive Job Submission](/software/Slurm/interactive_jobs)
- [Array Job Submission](/software/Slurm/array_jobs)

## Comparison of AGE/Slurm Commands

Here is a simplified comparison of basic commands between AGE and Slurm, categorized by their purpose:

| Command          | AGE                | Slurm                    | Slurm Online Manual Reference          |
| ---------------- | ------------------ | ------------------------ | -------------------------------------- |
| Job Submission   | `qsub jobfile`     | `sbatch jobfile`         | [sbatch](https://slurm.schedmd.com/sbatch.html) |
| Job Cancellation | `qdel jobID`       | `scancel jobID`          | [scancel](https://slurm.schedmd.com/scancel.html) |
| Queue Status     | `qstat`            | `squeue`                 | [squeue](https://slurm.schedmd.com/squeue.html) |
| Job Hold         | `qhold jobID`      | `scontrol hold jobID`    | [scontrol](https://slurm.schedmd.com/scontrol.html) |
| Queue/Partition List | `qconf -sql`   | `scontrol show partition` | [scontrol](https://slurm.schedmd.com/scontrol.html) |
| Node List        | `qhost`            | `scontrol show nodes`    | [scontrol](https://slurm.schedmd.com/scontrol.html) |
| Cluster Status   | `qhost -q`         | `sinfo`                  | [sinfo](https://slurm.schedmd.com/sinfo.html) |

Please note that each command may have different options, so refer to the online manual or the documentation site of the current developer, SchedMD Inc., for more details.

- Slurm Quick Start Guide: https://slurm.schedmd.com/quickstart.html
- Slurm Command Summary: https://slurm.schedmd.com/pdfs/summary.pdf
- Comparison of Various Job Management Systems
