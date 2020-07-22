commit 4affa14414dcd1a55fcecddd96a5062670f5d3d6
Author: Larry Smith Jr <mrlesmithjr@gmail.com>
Date:   Sun Jul 19 02:11:57 2020 -0400

    Added beginning of systemd-resolved/Consul/Dnsmasq integrations

commit 1675767c7ed6ea51cb5458c7cd71d858abc77d47
Author: Larry Smith Jr <mrlesmithjr@gmail.com>
Date:   Sat Jul 18 13:12:16 2020 -0400

    Added .DS_Store to Git ignore

commit 2f132c44ba5ffb17599a227e90ed95df01a709c2
Author: Larry Smith Jr <mrlesmithjr@gmail.com>
Date:   Sat Jul 18 11:15:34 2020 -0400

    Fixes issue with connecting to Consul
    
    The Consul service is not yet up at this point. So, we need to let handlers kick in

commit 73cde4f62c890f10c9782fdf61ebaa0a3c9aad59
Author: Larry Smith Jr <mrlesmithjr@gmail.com>
Date:   Sat Jul 18 00:23:27 2020 -0400

    Added missing screenshot

commit dc146c77aa79342879b4383bc273131b68cea2ce
Author: Larry Smith Jr <mrlesmithjr@gmail.com>
Date:   Sat Jul 18 00:21:20 2020 -0400

    Added info on halting environment

commit 4a525fffe369a69842b8a19fe20af7f0f9c2ef39
Author: Larry Smith Jr <mrlesmithjr@gmail.com>
Date:   Fri Jul 17 23:26:17 2020 -0400

    Previous group_vars fix was still wrong

commit ea22b23daa40df4567ba2aff2b21b9d52d64b578
Author: Larry Smith Jr <mrlesmithjr@gmail.com>
Date:   Fri Jul 17 23:23:37 2020 -0400

    Fixed link to group_vars

commit b15791851a3bcd06275a8245fcaaa53bfec2ddde
Author: Larry Smith Jr <mrlesmithjr@gmail.com>
Date:   Fri Jul 17 23:18:42 2020 -0400

    Added HAProxy and App Servers
    
    This adds HAProxy and Consul integration for service discovery. We have
    added three app servers that are running NGINX, Consul client, and
    registering the web service into Consul.

commit 2d633f99be5dfd7b08fb020cf404f716cecdbc0b
Author: Larry Smith Jr <mrlesmithjr@gmail.com>
Date:   Fri Jul 17 19:30:57 2020 -0400

    Updated info for Consul validations

commit a6720736fe71baeba6c9b5d14407b161a2290da0
Author: Larry Smith Jr <mrlesmithjr@gmail.com>
Date:   Fri Jul 17 19:29:16 2020 -0400

    Added new submodules to prep for HAProxy/Consul

commit 6a553dd3548f68bda4657cddabafdb13911e658c
Author: Larry Smith Jr <mrlesmithjr@gmail.com>
Date:   Fri Jul 17 12:21:51 2020 -0400

    Added initial Vault validations

commit abc6a933cae2ffc48f97c16bc78d3ce7b73dec52
Author: Larry Smith Jr <mrlesmithjr@gmail.com>
Date:   Fri Jul 17 11:39:27 2020 -0400

    Updated initial images

commit aa3eba2c1ea8df0d7fdf2e8e5d907846735c9ae7
Author: Larry Smith Jr <mrlesmithjr@gmail.com>
Date:   Fri Jul 17 11:21:31 2020 -0400

    Fixed proper path to inventory to exclude

commit d2060d8a4de6521d02c120d9b68f7ba53b4956e4
Author: Larry Smith Jr <mrlesmithjr@gmail.com>
Date:   Fri Jul 17 11:17:49 2020 -0400

    Updated gitignore to exclude inventory file

commit 48f6e1a14832cd46e1367ddbd5402b0d353eb0ec
Author: Larry Smith Jr <mrlesmithjr@gmail.com>
Date:   Fri Jul 17 11:09:48 2020 -0400

    First commit
