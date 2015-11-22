# liblightnvm - User Space Support for Application-Driven FTLs on Open-Channel SSDs

liblightnvm is a user space library that manages provisioning and I/O submission
for physical flash. The motivation is to enable I/O-intensive applications to
implement their own Flash Translation Tables (FTLs) directly on their logic.
Our system design is based on
the principle that high-performance I/O applications use log-structured data
structures to map flash constrains and implement their own data placement,
garbage collection,
and I/O scheduling strategies. Using liblightnvm, applications have direct
access to a physical flash provisioning interface, and perceive storage as large
single address space
flash pool from which they can allocate flash blocks from individual NAND flash
chips (LUNs) in the SSDs. This is relevant to match parallelism in the host and
in the device. Each application submits I/Os using physical addressing that
corresponds to specific block, page, etc. on device.

liblightnvm relies on Open-Channel SSDs, devices that share responsibilities
with the host to enable full host-based FTLs; and LightNVM, the Linux Kernel
subsystem supporting them. To know more about Open-Channel SSDs and the software
ecosystem around them please see [6].

For example, most key-value stores use Log Structured Merge Trees (LSMs) as
their base data structure (e.g., RocksDB, MongoDG, Apache Cassandra). The LSM is
in itself a form of FTL, which manages data placement and garbage collection.
This class of applications can benefit from a direct path to physical flash to
take full advantage of the optimizations they do and spend host resources on, instead of
missing them through the several levels of indirection that the traditional I/O
stack imposes to enable genericity: page cache, VFS, file system, and device
physical - logical translation table. liblightnvm exposes append-only primitives
using direct physical flash to support this class of applications.

## API description

TODO - Please see liblightnvm.h in the meantime.

## How to use

liblightnvm runs on top of LightNVM. However, the code is still not ready to
be sent upstream. Development is taking place on the liblnvm branch [1]; please
check it out to test liblightnvm. This branch will follow master. This is, it
will have all new functionality and fixes sent upstream to LightNVM.

Also, it is necessary to have an Open-Channel SSD that supports LightNVM
commands. For testing purposes you can use QEMU. Please checkout [2] to get
Keith Busch's qemu-nvme with support for Open-Channel SSDs. Documentation on how
to setup LightNVM and QEMU can be found in [3]. Refer to [4] for LightNVM sanity
checks.

Once LightNVM is running on QEMU you can install liblightnvm and test it. Note
that superuser privileges are required to install the library. Also, superuser
privileges are required to execute the library sanity checks. This is because
LightNVM target creation requires it.

- $ sudo make install_local - Copy the necessary headers
- $ sudo make install - Install liblightnvm
- # sudo make check - Execute sanity checks

Refer to tests/ to find example code.

A storage backend for RocksDB using liblightnvm will follow soon [5]

To know more about Open-Channel SSDs and the software ecosystem around
them please see [6].

[1] https://github.com/OpenChannelSSD/linux/tree/liblnvm
[2] http://openchannelssd.readthedocs.org/en/latest/gettingstarted/#configure-qemu
[3] https://github.com/OpenChannelSSD/qemu-nvme
[4] https://github.com/OpenChannelSSD/lightnvm-hw
[5] https://github.com/OpenChannelSSD/rocksdb
[6] http://openchannelssd.readthedocs.org/en/latest/

## Contact and Contributions

liblightnvm is in active development. Until the basic functionality is in
place, development will take place on master. We will start using feature
branches when the library reaches a stable version.

Please write to Javier at jg@lightnvm.io for more information.
