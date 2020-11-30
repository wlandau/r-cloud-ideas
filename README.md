# Problems and limitations

* In R, function-oriented distributed computing workloads are already possible through packages such as `clustermq` and `future`. However, these packages rely on traditional HPC systems such as SLURM and SGE, and there is not yet support for cloud-based services from companies like Amazon, Google, or Azure.
* The `paws` package interfaces with AWS, but it deliberately operates at a low level and does not support the abstractions necessary for arbitrary function-oriented distributed cloud computing accessible by the broader R community.
* The `cloudyr` project has undergone shifts in maintainership, and efforts like https://github.com/cloudyr/aws.batch are uncertain.

# Goal

The goal is to arrive at a reusable, efficient, user-friendly, cloud-based distributed computing solution for R:

1. Distributed computing powered by AWS or similar service.
1. Automatic setup and teardown (e.g. Batch is preferred over ParallelCluster).
1. Easy customization, e.g. arbitrary Docker containers.
1. Inexpensive for users (e.g. spot instance support).
1. Arbitrary iterated function-oriented workflows, e.g. `furrr::future_map()` or `clustermq::Q()`.
1. Minimal overhead. Rely on efficient network communication to manage results and dependencies, as opposed to the [classic AWS Batch "fetch-and-run" example](https://aws.amazon.com/blogs/compute/creating-a-simple-fetch-and-run-aws-batch-job/) or transactions over EFS.
1. An interface high-level and user-friendly enough for general consumption by the R community.
1. Full integration into the existing `clustermq` and/or `future` frameworks (which would carry over to the `drake` and `targets` packages automatically).

# Ideas

## AWS Batch backend for `clustermq`

See https://github.com/mschubert/clustermq/issues/208 for a discussion. Submitting array jobs to AWS Batch through `paws` is straightforward, but it requires reverse SSH tunneling, which might not be possible in Batch. And `clustermq` common data might not broadcast efficiently.

## AWS Fargate backend for `clustermq`

Mentioned at https://github.com/paws-r/paws/issues/330. Fargate looks promising at a glance, but more exploratory work remains.

## `clustermq` backend for `future`

See https://github.com/HenrikBengtsson/future/issues/204 and https://github.com/HenrikBengtsson/future.clustermq. If we go with one of the `clustermq`-native solutions, then the `future` ecosystem would automatically get it through a new  `future.clustermq` package (currently under development).

## AWS Batch backend for `future`

See https://github.com/HenrikBengtsson/future/issues/423. A new `future.awsbatch` package could provide direct AWS Batch support to the `future` ecosystem.

## Direct interface to EC2

As [documented here](https://davisvaughan.github.io/furrr/articles/advanced-furrr-remote-connections.html), this is already possible with `future` through a PSOCK cluster. However, it is not ideal becuase it requires complicated manual setup and teardown, and it restricts users to AWS AMIs (not arbitrary Docker images). 
