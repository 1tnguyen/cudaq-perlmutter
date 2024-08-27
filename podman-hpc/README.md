On the login node (zohim@login19:~>) in Perlmutter, run the following commands:

1. Build the image:

`podman-hpc build -t thien:test .`

2. Make the image available for jobs (e.g., compute nodes):

`podman-hpc migrate  thien:test`

3. Run the image

`srun -N 2 -n 8 podman-hpc run --rm --gpu -v $HOME:/work -v /dev/xpmem:/dev/xpmem -v /dev/shm:/dev/shm  -v /usr/lib/shifter/mpich-2.2:/opt/udiImage/modules/mpich -v /var/spool/slurmd:/var/spool/slurmd -v /run/munge:/run/munge -v /run/nscd:/run/nscd -v /etc/libibverbs.d:/etc/libibverbs.d -e LD_LIBRARY_PATH=/opt/udiImage/modules/mpich:/opt/udiImage/modules/mpich/dep:/usr/local/cuda/targets/x86_64-linux/lib/:/usr/lib64:$LD_LIBRARY_PATH -e MPICH_GPU_SUPPORT_ENABLED=1 -e LD_PRELOAD=/opt/udiImage/modules/mpich/libmpi_gtl_cuda.so.0 -e SLURM_* -e PALS_* -e PMI_* --ipc=host --network=host --pid=host --privileged -u root localhost/thien:test /work/a.out`


Note: This is temporary until podman-hpc support `LD_LIBRARY_PATH` prepend. We basically load the https://github.com/NERSC/podman-hpc/blob/main/etc/modules.d/cuda-mpich.yaml module manually.