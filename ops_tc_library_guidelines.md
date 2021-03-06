# OPS Topology Common Library Guidelines

## Overview
This document contains guidelines for teams working on modular framework libraries. The main intent
is to provide solutions for common questions and provide consistency throughout different libraries,
which are by nature, emergent, as new projects or improvements generate more testing needs.

## Code organization
New library code normally falls in two categories: 1) feature-specific and 2) common. The decision
of which functionality belongs to which category is something that should be discussed with the
feature dev/test team and the common library maintainers, who will provide guidance (for example,
if a given common functionality is already available or being worked on).

Feature-specific code should be checked in the feature repo itself, within the tests/component
folder and is meant to be maintained by the feature team.

Common code should ideally be made available for other teams to reduce work duplication and maintain
consistency. Whenever a team needs common functionality, it should first check to see if it's
available and if not, coordinate with the common library maintainers on how to contribute that code.

For the organization of code itself, refer to the [How to guide](how_to_guide.md).

## Guidelines
1. Code should follow the [Python Style Guide](https://www.python.org/dev/peps/pep-0008/) as well as
comply with Flake8 (already required in order to write component tests).
  1. Follow the general guidelines in [PEP 0257](https://www.python.org/dev/peps/pep-0257/) for
  docstrings.
  2. Use the guidelines in [AutoAPI](https://autoapi.readthedocs.io/#documenting-the-code).
  This allows autogenerating the documentation for the complete topology common code library, which
  users can consult to see if a function already exists.
2. Lower case with underscores for variable and function names. Try to keep function names compact.

        restd_process_state = get_process_state(name="restd")

3. Avoid hardcoding values -- try to use function arguments if possible.
4. Use named arguments to improved readability:

        create_vlan(vlan_id="100")

5. Prefer parameters vs creating many methods with different names and use strings instead of
integers in default values.

        create_vlan(vlan_id="100", mode="access") and create_vlan(vlan_id="100", mode="native")
            vs
        create_access_vlan(vlan_id=100) and create_native_vlan(vlan_id="100")

6. Provide sensible argument defaults which cover most use cases if possible.

        create_vlan(vlan_id="100", mode="access", status="no_shutdown")

7. Action verbs:
  1. **assert**: assert a condition -- no values are returned.

            assert_process_is_running()

  2. **create**: a new entity is added to the system.

            create_vlan(vlan_id="100")

  3. **delete**: an existing entity is deleted from the system.

            delete_vlan(vlan_id="100")

  4. **add**: add a reference or entity to an existing one on the system.

            add_port_to_vlan(vlan_id="100", port="1")

  5. **remove**: remove a reference or entity from an existing one on the system.

            remove_port_from_vlan(vlan_id="100", port="1")

  6. **get**: get a reference to or information from an existing entity in the system.

            get_process_pid(name="restd")

  7. **wait_until**: wait for a condition on the system to be met.

            wait_until_port_is_up(port="1")

  8. **send_..._packet**: send a packet.

            send_mcast_packet(count="10")

  9. **set**: set an entity value.

            set_vlan_state(state="down")

8. The step fixture object is required as an argument, it should always be the last argument with
default value of 'None'.

        send_mcast_packet(count="1", step=step)

9. Common functions should verify and invoke the step() call internally if possible. This helps
with keeping text consistent and reduces retyping.

10. Step messages should include argument information whenever possible.

        step and step("Adding VLAN {}".format(vlan_id))

11. Autogenerated code shouldn't normally be checked in unless it is impossible/impractical to
generate on the fly.

## Guideline usage examples
The following examples illustrate the guidelines in practice, along with design explanations where
relevant.
### Service information example
#### Library
    [service.py]
    <license_text>
    """
    SystemD Service Management functions for OpenSwitch nodes

    This file is part of the Topology Modular Framework Common Code Library.
    It uses systemd (systemctl), the default service manager in OpenSwitch.

    This module is not supported in:
        - Modular Topology objects that do not support the Bash shell
        - Systems that do not use systemd as its service manager
    """

    def is_running(node, service, step=None):
        """
        Uses systemctl to verify that a service is running

        :param node: A modular framework node object that supports the Bash shell
        :param service: Filename of process to check if is running
        :param step: A modular framework step object to set a debugging mark.
            Default: None
        :returns: Whether ``service`` is running or not
        :rtype: Boolean
        """

        if step is not None:
            step("Assert {service} is running".format(**locals()))

        output = node("systemctl status {service}".format(**locals()),
                      shell='bash')

        lines = output.split('\n')
        return "active (running)" in (lines[2] if len(lines) > 2 else "")

    def get_pid(node, service):
        """
        Uses systemctl to obtain the PID of a running service

        Returns 0 for a process that does not exist or is not running.

        :param node: A modular framework node object that supports the Bash shell
        :param service: The name of the service
        :returns: The pid of ``service``
        :rtype: int
        """

        output = node("systemctl show {service} "
                      "--property=MainPID | cat".format(**locals()),
                      shell="bash")

        lines = output.split('=')
        return int(lines[1]) if len(lines) > 1 else 0

#### VLAN
    [vlan.py]
    <license_text>
    """
    VLAN CLI functions for OpenSwitch nodes

    This file is part of the Topology Modular Framework Common Code Library.

    This module is supported in:
        - Modular Topology objects that support vtysh
    """

    def create_vlan_interface(node, vlan_id, ip_address, step=None):
        """
        Configure VLAN interface using libvtysh

        :param node: A modular framework node object that supports the Bash shell
        :param vlan_id: VLAN ID for interface to be configured
        :param ip_address: IP address to configure on interface
        :param step: A modular framework step object to set a debugging mark.
            Default: None
        """

        step and step("# Creating vlan interface {} in {}".format(vlan_id,
                                                                  node.alias))

        with node.libs.vtysh.ConfigVlan(vlan_id) as ctx:
            ctx.no_shutdown()

        with node.libs.vtysh.ConfigInterfaceVlan(vlan_id) as ctx:
            ctx.ip_address(ip_address)
            ctx.no_shutdown()

#### Usage
    [test_restd_starts_on_boot.py]
    <license_text>
    """
    Component Test: System library usage example
    """

    from topology_common.ops.system import service
    from topology_common.ops.l2.vlan import vlan

    TOPOLOGY = """
    #
    # +-------+
    # |  sw1  |
    # +-------+
    #

    # Nodes
    [type=openswitch name="Switch 1" image="topology/ops:latest"] sw1
    """

    def test_restd_starts_on_boot(topology, step):
        sw1 = topology.get('sw1')

        assert sw1 is not None

        vlan.create_vlan_interface(node=sw1, vlan_id="1", ip_address="10.10.10.5/8")

        assert service.is_running(node=sw1, service="restd", step=step)

Follow the coding guidelines defined in the [Writing doctrings for Sphinx](writing_docstrings.md)
document for further details.
