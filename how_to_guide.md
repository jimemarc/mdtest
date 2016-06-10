# OpenSwitch Topology Modular Framework Common Code
A library for the Topology Modular Framework that contains code that can be shared between
repositories in OpenSwitch.

## Directory structure
```
.
├── MAINTAINERS
├── MANIFEST.in
├── README.md
├── doc
│   └── ... (other_doc_files)
├── lib
│   ├── topology_common
│   │   ├── host
│   │   │   ├── __init__.py (feature's specific Python package init file)
│   │   │   └── vlan.py
│   │   ├── ops
│   │   │   ├── l2
│   │   │   │   ├── l2_physical_interfaces
│   │   │   │   │   └── port_vsctl.py
│   │   │   │   ├── link_aggregation
│   │   │   │   │   └── link_aggregation.py
│   │   │   │   └── vlans
│   │   │   │       ├── vlan.py
│   │   │   │       └── vlan_vsctl.py
│   │   │   ├── l3
│   │   │   │   ├── basic_l3
│   │   │   │   │   ├── l3_interfaces.py
│   │   │   │   │   └── routing.py
│   │   │   │   ├── static_route
│   │   │   │   │   └── static_route.py
│   │   │   │   └── vrf
│   │   │   │       └── vrf_vsctl.py
│   │   │   └── system
│   │   │       └── service.py
│   │   └── utils
│   │       ├── ip_address.py
│   │       ├── mac_address.py
│   │       └── tcpdump.py
│   └── topology_common.egg-info
├── ... (other_config_files)
├── requirements.dev.txt
├── setup.py
└── test
    ├── host
    ├── ops
    │   └── vlans
    │       └── test_vlan.py
    └── utils
        └── test_tcpdump.py
```


Under the root directory several configuration files such as the `setup.py` script, the
`MANIFEST.in`, `requirements.dev.txt` and others can be found.

The most important directories for developers contributing common code are `lib/topology_common/`
and `test/`, where the production and test code live.


## Production code
The production code lives in  the `lib/topology_common` directory.

The common code can be separated by the following criteria:

- Is the code specific to a linux host?
    - Create the files directly under `lib/topology_common/host`
- Is the code specific to OpenSwitch feature configuration and/or verification?
    - Create a file named after the feature and place it under
      `lib/topology_common/ops/<feature_path>`.
- Is the code generic, meaning can be used largely in all test cases (e.g. create MAC addresses,
  increment IP)?
    - Place the files directly under `lib/topology_common/utils`


Example that contains code for the VLAN feature and common code for services and file management:

```
lib
├── topology_common
│   ├── host
│   │   ├── __init__.py (feature's specific Python package init file)
│   │   ├── file_management.py
│   │   └── vlan.py
│   ├── ops
│   │   ├── l2
│   │   │   └── vlans
│   │   │       ├── vlan.py
│   │   │       └── vlan_vsctl.py
│   │   └── system
│   │       ├── process_information.py
│   │       └── service.py
│   └── utils
│       └── mac_address.py
└── topology_common.egg-info
```

> ### Note
> For every new directory created under `lib/topology_common`, a new `__init__.py` (can be empty)
> needs to be created under the directory, so that it is treated as a Python package.

## Using the library in feature or component tests
The Topology Common library is built and installed as a Python package for the tests if added to the
`requirements.txt` file.

1. Add the repo to the `tox` `requirements.txt` file, e.g.:
```
# Topology Common Code library
-e git+https://git-nos.rose.rdlabs.hpecorp.net/hpe/hpe-topology-common@master#egg=topology_common
```

2. Import the modules of interest in the tests, e.g.:
```
from topology_common.ops.system import service as ops.service
```
or
```
from topology_common.host import vlan
```

3. Use the code in the tests, e.g.:
```
    def test_verify_service_running():

        assert ops.service.is_running("ops-my-service") is True
```

## Topology Common Code library tests
The library defines APIs that many tests will be using and depending on. As changes are added to the
library, the API may change and dependent tests may break. **As a responsible developer please
make sure to add tests along with the library contributions.**

The test code lives in  the `test/` directory.

The Topology Common library supports `pytest` and `tox`. The tests can be easily run from the repo's
root directory with the command:
```
tox
```

> ### Note
> A Jenkins' job will execute `tox` as part of the workflow.  Zuul will reject a review if `tox`
> fails.

The `topology_common\lib` directory is a Python package and `tox` is responsible for installing the
package when running tests. Hence, in order to access the production code from the test code, simply
import the modules.

Example using the following structure:

```
lib
├── topology_common
│   ├── host
│   │   ├── __init__.py (feature's specific Python package init file)
│   │   └── vlan.py
│   ├── ops
│   │   ├── l2
│   │   │   └── vlans
│   │   │       ├── vlan.py
│   │   │       └── vlan_vsctl.py
│   │   └── system
│   │       └── service.py
│   └── utils
│       └── mac_address.py
└── topology_common.egg-info
```

To access the production code from the `test_vlan.py` test file:
```
from topology_common.ops.l2.vlan import vlan_vsctl
```


## Contributing to the library
The following is expected with each contribution:

 1. Follow the coding guidelines defined in the [OPS Topology Common
    Library Guidelines](ops_tc_library_guidelines.md) document.
 2. Self-documented code using docstrings. Follow the coding guidelines defined in the
    [Writing doctrings for Sphinx](writing_docstrings.md) document for further details.
 3. Tests for every new feature or change to existing features.
 4. All existing tests pass. Verify by running tox in the root of the repo before sending for
review.
