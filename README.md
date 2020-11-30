# Problem

In R, function-oriented distributed computing workloads are already possible through packages such as `clustermq` and `future`. However, these packages rely on traditional HPC systems such as SLURM and SGE, and there is not yet support for cloud-based services from companies like Amazon, Google, or Azure. The `paws` package interfaces with AWS, but it deliberately operates at a low level and does not support the abstractions necessary for native function-oriented distributed computing.

# Goal

The goal is to arrive at a reusable cloud-based distributed computing solution for R:

1. Distributed computing powered by AWS or similar service.
1. Automatic setup and teardown (e.g. Batch is preferred over ParallelCluster).
1. Easy setup, e.g. arbitrary Docker containers.
1. Inexpensive for users (e.g. spot instance support).
1. Arbitrary iterated function-oriented workflows, e.g. `furrr::future_map()` or `clustermq::Q()`.
1. Rely on efficient network communication to manage results and dependencies, as opposed to the [classic AWS Batch "fetch-and-run" example](https://aws.amazon.com/blogs/compute/creating-a-simple-fetch-and-run-aws-batch-job/) or transactions over EFS.
1. An interface high-level and user-friendly enough for general consumption by the R community.
1. Full integration into the existing `clustermq` and/or `future` frameworks (which would carry over to the `drake` and `targets` packages automatically).

# Ideas

## AWS Batch backend for `clustermq`
