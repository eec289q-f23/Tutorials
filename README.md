# Tutorials

The tutorials here are designed to introduce tools or software that we will use in EEC289Q. If you already know how to use these tools, we still suggest you quickly browse the topic so you know what information is here. New tutorials will be added and old ones may be updated throughout the course. When this happens we will post to both [Canvas: EEC289Q F23](https://canvas.ucdavis.edu/courses/825913) and [Slack: #eec289q-f23-general](https://ucdavis.slack.com/). 

If you have additional questions or comments please use the Slack channel so everyone can see the discussion. We will add FAQ sections here if necessary.

If you find any errors in the tutorials, please open an [Issue](https://github.com/eec289q-f23/tutorials/issues) so we can fix it.

## Development Tools 

This course uses Linux for all homeworks and projects. We will have access to [HPC2](https://hpc.ucdavis.edu/clusters), a high-performance computing cluster, which you can use for both development and running jobs.

If using HPC2 for development, we highly recommend using VSCode with the SSH/C++ extensions. You can also develop locally if you use WSL2 for Windows, or run OSX/Linux natively. We will do our best to tell you which packages you need to install to do so, but ultimately it will be up to you to get local development environments working. HPC2 will be already setup for development without needing any additional packages installed.

- [WSL](./WSL.md): Install Linux on Windows with WSL2 for developing locally (optional)
- [VSCode](./VSCODE.md): Install VSCode for developing on HPC2 or locally (recommended)
- [SSH](./SSH.md): Manage SSH keys and use SSH for HPC2 and GitHub (required)
- More coming soon...

## HPC

HPC2, like most high-performance computing clusters, uses Slurm to manage jobs. This allows many people to share computing resources by submitting work into a job queue. If resources are available, your job will execute immediately. If they are not, you will be queued until they become available. You may request specific servers and CPUs to ensure your job is maximally performant.

- [HPC2](./HPC2.md): How to create an account on HPC2, login, and transfer files
- [Slurm](./SLURM.md): Use Slurm to launch jobs on HPC clusters
- More coming soon...

## Languages and Packages

- Coming soon...
