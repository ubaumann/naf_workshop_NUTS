For this lab, we use a prepared environment created with `netlab`. You can run the virtual lab with containerlab, but to conserve resources, everything is set up to run with the mock driver from NAPALM. Without starting the lab, only the test cases using NAPALM can be executed.

Use the following repository for your work. You can fork and clone it.

[https://github.com/ubaumann/naf_workshop_nuts_lab](https://github.com/ubaumann/naf_workshop_nuts_lab)

---

## Setup

### 📝 Install

Follow the instructions in the README.md

??? example "Solution"

    ```bash
    $ uv sync
    Resolved 59 packages in 2ms
    Audited 54 packages in 0.15ms
    ```

### 📝Initialize Project

Since version v3.5.0, NUTS includes a small helper script, `nuts-init`, to bootstrap the project structure.
Set the test directory to `./tests`, the nornir config file to `./nr-config.yaml`, and add one Arista EOS host. The inventory is already provided in the `./mocked_inventory` directory. Specify the inventory as a path, but do not let the script create the inventory, as this would overwrite the existing files.

The helper script will create the `nr-config.yaml` file and a demo test case at `tests/test_lldp_neighbors_demo.yaml`.

??? example "Solution"

    ```bash
    $ uv run nuts-init
    Test dir [./tests]: 
    Nornir config file [./nr-config.yaml]: 
    Inventory directory [./mocked_inventory]: 
    Create simple inventory [y/N]: N
    Add Cisco XE host [y/N]: N
    Add Juniper Junos host [y/N]: N
    Add Arista EOS host [y/N]: y
    Add Cisco NX host [y/N]: N
    Add Cisco XR host [y/N]: N
    Use netmiko session logs [y/N]: N
    ```

    ```yaml title="nr_config.yaml"
    inventory:
    plugin: SimpleInventory
    options:
        host_file: mocked_inventory/hosts.yaml
        group_file: mocked_inventory/groups.yaml
    transform_function:  # consider using "load_credentials" from nornir_utils
    runner:
    plugin: threaded
    options:
        num_workers: 100
    logging:
    enabled: false
    ```

!!! note

    If you already have a nornir config file, you can use the pytest option `--nornir-config` (this option is automatically added when pytest discovers the nuts plugin)

    ```
    pytest --nornir-config "config.yaml"
    ```


## My first test

The bootstrap script has already created a demo test case. If you run pytest now, the test will fail with `No hosts found for filter ... in Nornir inventory.` This is because the bootstrap script does not know the naming of the hosts in your lab/inventory.

### 📝 No hosts found

Run the test using the pytest command. This test should fail because the hostname is not found in your inventory.

??? example "Solution"

    ```bash
    $ uv run pytest --disable-warnings -q

    ====================================== ERRORS =======================================
    _______________ ERROR collecting tests/test_lldp_neighbors_demo.yaml ________________
    .venv/lib/python3.11/site-packages/pluggy/_hooks.py:512: in __call__
        return self._hookexec(self.name, self._hookimpls.copy(), kwargs, firstresult)
    .venv/lib/python3.11/site-packages/pluggy/_manager.py:120: in _hookexec
        return self._inner_hookexec(hook_name, methods, kwargs, firstresult)
    .venv/lib/python3.11/site-packages/_pytest/python.py:271: in pytest_pycollect_makeitem
        return list(collector._genfunctions(name, obj))
    .venv/lib/python3.11/site-packages/_pytest/python.py:498: in _genfunctions
        self.ihook.pytest_generate_tests.call_extra(methods, dict(metafunc=metafunc))
    .venv/lib/python3.11/site-packages/pluggy/_hooks.py:573: in call_extra
        return self._hookexec(self.name, hookimpls, kwargs, firstresult)
    .venv/lib/python3.11/site-packages/pluggy/_manager.py:120: in _hookexec
        return self._inner_hookexec(hook_name, methods, kwargs, firstresult)
    .venv/lib/python3.11/site-packages/nuts/plugin.py:71: in pytest_generate_tests
        parametrize_args, parametrize_data = get_parametrize_data(
    .venv/lib/python3.11/site-packages/nuts/yamlloader.py:202: in get_parametrize_data
        data["test_data"] = ctx.parametrize(data.get("test_data", []))
    .venv/lib/python3.11/site-packages/nuts/context.py:191: in parametrize
        raise NutsSetupError(
    E   nuts.helpers.errors.NutsSetupError: No hosts found for filter <Filter ({'name__any': ['arista-eos-demo-01']})> in Nornir inventory.
    ============================== short test summary info ==============================
    ERROR tests/test_lldp_neighbors_demo.yaml::TestNapalmLldpNeighborsCount - nuts.helpers.errors.NutsSetupError: No hosts found for filter <Filter ({'name__a...
    !!!!!!!!!!!!!!!!!!!!!! Interrupted: 1 error during collection !!!!!!!!!!!!!!!!!!!!!!!
    3 warnings, 1 error in 0.65s
    ```

    ```yaml title="tests/test_lldp_neighbors_demo.yaml"
    - test_class: TestNapalmLldpNeighborsCount
        test_data:
        - host: arista-eos-demo-01
            neighbor_count: 3  # Number of LLDP neighbors need to be updated
    ```

### 📝 Host r02

Change the hostname to `r02` in the test definition `tests/test_lldp_neighbors_demo.yaml` and run the test again.
The test will still fail, but now the reason is "AssertionError: assert 3 == 5". The test checks if `r02` has 3 neighbors, but the device actually has 5 neighbors, so the test fails.

??? example "Solution"

    ```bash
    uv run pytest --disable-warnings -q
    F                                                                             [100%]
    ===================================== FAILURES ======================================
    ______________ TestNapalmLldpNeighborsCount.test_neighbor_count[r02_] _______________

    self = <nuts.base_tests.napalm_lldp_neighbors.TestNapalmLldpNeighborsCount object at 0x7f3dd2f5f910>
    single_result = <nuts.helpers.result.NutsResult object at 0x7f3dd335f090>
    neighbor_count = 3

        @pytest.mark.nuts("neighbor_count")
        def test_neighbor_count(self, single_result, neighbor_count):
    >       assert neighbor_count == len(single_result.result)
    E       AssertionError: assert 3 == 5
    E        +  where 5 = len({'Ethernet1': {'parent_interface': 'Ethernet1', 'remote_chassis_id': '00:1C:73:F6:74:93', 'remote_host': 'r01', 'remot...e': 'Ethernet4', 'remote_chassis_id': '00:1C:73:B1:E5:CD', 'remote_host': 'r04', 'remote_port': 'Ethernet1', ...}, ...})
    E        +    where {'Ethernet1': {'parent_interface': 'Ethernet1', 'remote_chassis_id': '00:1C:73:F6:74:93', 'remote_host': 'r01', 'remot...e': 'Ethernet4', 'remote_chassis_id': '00:1C:73:B1:E5:CD', 'remote_host': 'r04', 'remote_port': 'Ethernet1', ...}, ...} = <nuts.helpers.result.NutsResult object at 0x7f3dd335f090>.result

    .venv/lib/python3.11/site-packages/nuts/base_tests/napalm_lldp_neighbors.py:74: AssertionError
    ============================== short test summary info ==============================
    FAILED tests/test_lldp_neighbors_demo.yaml::TestNapalmLldpNeighborsCount::test_neighbor_count[r02_] - AssertionError: assert 3 == 5
    1 failed, 3 warnings in 0.41s
    ```

    ```yaml title="tests/test_lldp_neighbors_demo.yaml"
    - test_class: TestNapalmLldpNeighborsCount
        test_data:
        - host: r02
            neighbor_count: 3  # Number of LLDP neighbors need to be updated
    ```

### 📝 Host r02, 5 LLDP neighbors

Update the expected neighbor count to 5 and run the test again. This time, the test should pass.

??? example "Solution"

    ```bash
    $ uv run pytest --disable-warnings -v
    ================================ test session starts ================================
    platform linux -- Python 3.11.12, pytest-7.4.4, pluggy-1.6.0 -- /workspaces/naf_workshop_nuts_lab/.venv/bin/python
    cachedir: .pytest_cache
    rootdir: /workspaces/naf_workshop_nuts_lab
    plugins: nuts-3.5.0
    collected 1 item                                                                    

    tests/test_lldp_neighbors_demo.yaml::TestNapalmLldpNeighborsCount::test_neighbor_count[r02_] PASSED [100%]

    =========================== 1 passed, 3 warnings in 0.33s ===========================
    ```

    ```yaml title="tests/test_lldp_neighbors_demo.yaml"
    - test_class: TestNapalmLldpNeighborsCount
        test_data:
        - host: r02
            neighbor_count: 5  # Number of LLDP neighbors need to be updated
    ```
