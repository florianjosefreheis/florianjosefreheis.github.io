---
title: "CircleCI - Received killed signal"
layout: post
date: 2024-02-16 08:40
tag:
    - ruby
    - circleci
    - linux
category: blog
author: flo
description: Upgrading to Ruby 3.3.0 caused Gem GRPC 1.60 on CircleCI throw Received 'killed' signal as a result of running out of memory.
---

---

I was recently upgrading our core platform from 3.2.2 to 3.3.0 and when running into an issue with the gem [GRPC](https://github.com/grpc/grpc/tree/master/src/ruby). The gem was causing the CircleCI build to fail with **Received "killed" signal** error.

![received_kill_signal_circleci_error](/assets/images/circleci_received_killed_signal/received_killed_signal_circleci_error.png){:class="img-responsive" width="60%"}

## Issue

After a quick search through the vast internet, I found a GitHub [issue](https://github.com/grpc/grpc/issues/28244) from 2021 that was resolved via [PR-28250 - Add an env var to override make parallelism in ruby build](https://github.com/grpc/grpc/pull/28250). During the installation process GRPC used the number of CPUs of the host machine rather than the number of CPUs that is assigned to the docker container.

Let's say you're running a CicleCi `medium` [resource class](https://circleci.com/product/features/resource-classes/) that has 2 CPU and 4 GB of RAM. The docker container will have 2 CPUs and 4 GB of RAM. However, the host machine has 36 CPUs and 64 GB of RAM. When running **`bundle install`**, it will spawn 36 processes, each with new memory allocations resulting in the host machine running out of memory.

## Solution

The above mentioned PR introduced a new CircleCI configuration ENV `GRPC_RUBY_BUILD_PROCS` that allows overwriting the CPU allocation. Setting **`GRPC_RUBY_BUILD_PROCS: 4`** ensured that the number of processes spawned was limited to 4. One issue, one PR, and one CircleCI config later and the error was fixed.

I was wondering how we could debug this without googling. Let's have a look at the debugging process.

## SSH into CircleCI

We can jump on the box via CircleCI's [Rerun job with SSH](https://circleci.com/docs/ssh-access-jobs/) and run **`ps axfn`**.

```circleci
circleci@a64186f7a238:~$ ps axfn
    PID TTY      STAT   TIME COMMAND
      8 ?        Ssl    0:08 /bin/circleci-agent _internal inner --innerConfig -
    136 pts/1    Ss+    0:00  \_ /bin/bash -eo pipefail -c #!/usr/bin/env bash  if bundle config set > /dev/null 2>&1; then   if [ "$PARAM_PATH" == "./vendor/bundle" ]; then
    142 pts/1    Sl+    0:15  |   \_ /usr/local/bin/ruby /home/circleci/.rubygems/bin/bundle install
    554 pts/1    S+     0:00  |       \_ /usr/local/bin/ruby -rrubygems /home/circleci/project/vendor/bundle/ruby/3.2.0/gems/rake-13.1.0/exe/rake RUBYARCHDIR=/home/circleci/proje
   2036 pts/1    S+     0:00  |       |   \_ /usr/local/bin/ruby -S extconf.rb
   2042 pts/1    S+     0:00  |       |       \_ gcc -I/usr/local/include/ruby-3.3.0/x86_64-linux -I/usr/local/include/ruby-3.3.0/ruby/backward -I/usr/local/include/ruby-3.3.0 -I
   2043 pts/1    R+     0:00  |       |           \_ /usr/lib/gcc/x86_64-linux-gnu/11/cc1 -quiet -I /usr/local/include/ruby-3.3.0/x86_64-linux -I /usr/local/include/ruby-3.3.0/ru
   1817 pts/1    S+     0:00  |       \_ /usr/local/bin/ruby extconf.rb
   1828 pts/1    R+     0:01  |           \_ make -j36 -C /home/circleci/project/vendor/bundle/ruby/3.2.0/gems/grpc-1.60.0 /home/circleci/project/vendor/bundle/ruby/3.2.0/gems/gr
   1417 pts/2    Ss     0:00  \_ -bash
   2044 pts/2    R+     0:00      \_ ps axfn
      1 pts/0    Ss     0:00 /sbin/docker-init -- /bin/sh
      7 pts/0    S+     0:00 /bin/sh
```

Skimming through the output we can see that **`make`** is called with **`-j36`** via the file **`extconf.rb`**. It seems CircleCI machines provide 36 CPUs, as in reach for to use that will result in Ruby allowing the creation of 36 jobs. And all of them need memory. This can significantly increase memory overhead, as the total memory usage can be up to 36 times of a single process in this case.

The **`bundle install`** operation will likely run out of memory (OOM). Subsequently, the kernel will kill the process, resulting in the **Received "killed" signal** error.

### Setting GRPC_RUBY_BUILD_PROCS

Let's go to the [GRPC Ruby source](https://github.com/grpc/grpc/blob/master/src/ruby/README.md) GitHub repo and look at the **`extconf.rb`** file:

```ruby
...
128   unless windows
129     puts 'Building internal gRPC into ' + grpc_lib_dir
130     nproc = 4
131     nproc = Etc.nprocessors if Etc.respond_to? :nprocessors
132     nproc_override = ENV['GRPC_RUBY_BUILD_PROCS']
133     unless nproc_override.nil? or nproc_override.size == 0
134       nproc = nproc_override
135       puts "Overriding make parallelism to #{nproc}"
136     end
137     make = bsd ? 'gmake' : 'make'
138     cmd = "#{make} -j#{nproc} -C #{grpc_root} #{grpc_lib_dir}/libgrpc.a CONFIG=#{grpc_config} Q="
139     puts "Building grpc native library: #{cmd}"
140     system(cmd)
141     exit 1 unless $? == 0
142   end
...
```

[Etc.nprocessors](https://www.rubydoc.info/stdlib/etc/Etc.nprocessors) on line 131 will return the available number of physical CPUs of the host machine. On line 132 we can see the added overwrite via **`ENV['GRPC_RUBY_BUILD_PROCS']`** which is used in the executing command on line 138.

## Fixing the error

Configuring the CircleCI config YAML file to set the **`GRPC_RUBY_BUILD_PROCS`** as an environment variable will limit the max spawned processes to avoid running out of memory.

Below is the final code snippet for the CircleCI config YAML file:

```yaml
...
- run:
    name: Bundle Install
    command: bundle config set path 'vendor/bundle' && bundle install --deployment --jobs=4
    environment:
      GRPC_RUBY_BUILD_PROCS: 4
    ...
```
